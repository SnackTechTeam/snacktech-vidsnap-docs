## Infraestrutura da Aplicação
O sistema Snapvid foi projetado para funcionamento no serviço de nuvem da AWS e para tal se utiliza dos serviços descritos abaixo.

### Provisionamento de recursos
O projeto utiliza Terraform para automatizar o provisionamento e gerenciamento de recursos. Esse processo é realizado no repositório [Infra](https://github.com/SnackTechTeam/snapvid-infra). A seguir uma descriação dos principais recursos utilizados.

### VPC
Para hospedagem dos recursos é inicialmente criada uma VPC (Virtual Private Cloud) composta de 2 redes privadas e 2 redes públicas. 
Nas duas redes privadas serão hospedados os nodes do EKS e as instâncias de RDS usadas pela aplicação.
As redes públicas destinam-se a futuros recursos e não são utilizadas para o escopo do que foi desenvolvido e descrito aqui. 

### EKS
O recurso principal da infraestrutura é o cluster EKS (Elastic Kubernetes Service). O cluster possui capacidade de escalabilidade automatica partindo do mínimo de 1 node e aumentando até o limite configurado. 
O acesso aos PODs dentro do EKS é restrito apenas a trafego originado na VPC e nas portas usadas pelos PODs. Isso é atingido através do uso de Security Groups e pelo uso de subredes privadas. Com isso garantimos o requisito de liberar apenas o mínimo necessário para o funcionamento do sistema.

### RDS
Para o armazenamento de dados da aplicação é provisionada uma instancia de banco no serviço RDS (Relacional Database Service). Através do RDS conseguimos delegar a complexidade da gestão dos detalhes do serviço e focar na gestão dos dados.
O tamanho e caracteristicas da instância são configuraveis para se adequar ao volume de demanda previsto para a aplicação. 
Quanto a segurança, a instância tem seu acesso restrito apenas para trafego originado na VPC (através de security groups). Além disso, ela é provisionada nas redes privadas, ficando assim inacessivel a partir da rede externa. 

### ECR
São provisionados ECRs (Elastic Container Registry) para hospedagem das imagens dos diversos componentes da aplicação.

### SQS
Para operacionalização da mensageria da aplicação é utilizado o serviço SQS (Simple Queue Service). As filas utilizam o padrão DLQ (Dead Letter Queue), onde mensagens que sofrem falha de processamento são armazenadas numa fila a parte para posterior analise e tratamento.
São 3 filas onde cada uma destina-se a um fluxo de comunicação:
- Recepção de novo vídeo: Fila alimentada pelo serviço S3 e consumida pelo worker que processa os videos recebidos.
- Atualização de status de vídeo: Fila alimentada pelo worker de processamento e consumida pelo worker de atualização de status.
- Notificação de conclusão de processamento: Fila alimentada pelo worker de atualização de status e consumida pelo serviço de envio de e-mail. 

### S3
Para armazenamento dos vídeos e dos arquivos resultantes do processamento, é provisionado um bucket no serviço S3 (Simple Storage Service).
Os arquivos desse backet são privados e só podem ser manipulados por meio do console da AWS ou por meio de URLs assinadas. 
Também é configurável o tempo que os videos e demais arquivos ficaram armazenados. Para os testes do prototipo esse tempo foi configurado para 1 dia, como forma de evitar consumo excessivo dos créditos da conta LAB.

### API Gateway
Com a API protegida nas redes privadas da VPC, é através do API Gateway que todos os acessos a ela devem passar.
As requisições são encaminhadas para a API através de um VPC Link criado entre o API Gateway e um Load Balancer interno instanciado dentro do EKS. Esse load balancer é responsável por distribuir as requisições entre as diversas instâncias da API.
Para proteger a API contra acessos de usuários não cadastrados, o gateway se utiliza de uma autorização customizada. Esse processo é realizado pela função lambda que além de autorizar o acesso, retorna o ID e o e-mail do usuário. Esses dados são, por sua vez, adicionados aos headers da requisição para que sejam usados na API para identificar o usuário e restrigir o escopo dos arquivos manipuláveis por ele.  

### Cognito e Autenticação
A função lambda usada pelo API Gateway assume a função de autorização e autenticação de usuários. Para isso ela se utiliza do serviço AWS Cognito. Através dele o gerenciamento de usuário é delegado trazendo mais segurança a guarda das credenciais e validação destas por meio do processo OAuth2. 