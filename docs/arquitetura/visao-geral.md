# Vidsnap

## Descrição

Vidsnap é um sistema de processamento de vídeos e imagens, com foco em produzir imagens a partir dos vídeos enviados pelos usuários. 

Possui múltiplos componentes que trabalham juntos para atingir o objetivo esperado, aplicando conceitos de arquitetura em microsserviços orientada a eventos, sistemas em cloud e qualidade de software.

O sistema trabalha com vários artefatos da AWS, cloud da Amazon, que fornecem a estrutura para atender as necessidades infraestruturais.

## Componentes

### Gateway

Ponto de entrada por onde usuários (pessoas ou outros sistemas) podem interagir com a aplicação. Possui responsabilidades de autorizar e autenticar os usuários antes de encaminhar as interações ao sistema.

### API

A interface responsável por receber as requisições e executar os devidos processamentos. Possui operações de envio e busca dos recursos disponibilizados. Trabalha com Urls pré assinadas do S3 para ser feito o download e upload de arquvos de maneira segura, sem utilizar recursos de rede da API.

### Sistema de Autenticação e Autorização

Processo que utiliza alguns componentes para aplicar a identificação e validação dos usuários, garantindo a permissão às operações realizadas. O Gateway faz uso desse sistema para fazer seu serviço.

### Base de Dados

Estrutura de base relacional SQL para armazenar e recuperar dados de usuários e seus respectivos vídeos e arquivos.

### Armazenamento de Arquivos

O sistema utiliza o S3 (Simple Storage Service), uma estrutura diferente da Base de Dados para guardar arquivos como vídeos, imagens e zips. É uma estrutura focada no armazenamento de arquivos o que trás muitos benefícios como disponibilidade, durabilidade e controle de acesso.

### Serviço de Mensageria

A arquitetura trabalha com geração e consumo de eventos orquestrados para a execução de processos. Esses eventos são disponibilizados via mensageria através do SQS (Simple Queue Service)

### Componentes de Processamento em Background

Trabalhos que demandam mais recursos como o processamento de vídeo e operações em segundo plano como a atualização do status de processamento são realizados por aplicações chamadas Workers, que fazem o consumo dos respectivos eventos e executam seus processos de acordo. Temos o Worker de Vídeo e o Worker de atualização de status, alem do Notificador de Emails

### Serviço KEDA

Permite aplicar scaling de componentes a partir de regras como quantidade de mensagens esperando processamento, o que permite processamento paralelo, performance em cenários de picos e estabilidade geral do sistema

### Notificador

O notificador permite avisar os usuários de forma assíncrona (ou seja, o usuário não precisa ficar checando a todo momento se terminou a execução) sobre o resultado do processamento dos vídeos, disparando um e-mail para o endereço que o mesmo forneceu.

## Características Arquiteturais

- **Arquitetura orientada a eventos**: Serviços se comunicam via mensagens ou eventos entre suas operações
- **Processamento assíncrono**: Workers rodam tarefas em background, deixando recursos livres para as APIs do sistema
- **Escalabilidade automática**: Componentes escalam de acordo com a necessidade via regras de HPA ou do KEDA
- **Armazenamento de dados**: Estruturas adequadas para armazenar e retornar cada tipo de dado trabalhado
- **Segurança**: Sistema possui fluxo dedicado para autenticação e autorização de operações

Com esses pontos é possível o sistema processar grandes volumes de requisições e mídias, com segurança e notificação em tempo "real" sobre o status de cada operação.