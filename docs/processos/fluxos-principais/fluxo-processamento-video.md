# Fluxo de Processamento de Vídeo

Quando o vídeo é armazenado no S3, o próprio serviço gera um evento que cai numa fila SQS. o Worker de Processamento consome essa mensagem e a partir dela faz o processamento do vídeo.
[Repositório do Worker de Processamento de Vídeo](https://github.com/SnackTechTeam/snacktech-vidsnap-worker-video)

Esse processamento envolve os seguintes passos:

- Validar que o evento se trata de um arquivo válido para processamento
- Notificar que o vídeo está começando a ser processado
- Baixar o vídeo do S3 localmente
- Fazer a extração das imagens
- Criar um arquivo zip a partir das imagens geradas
- Fazer o upload do arquivo zip e da primeira imagem gerada, no mesmo local onde está o vídeo no S3
- Limpar arquivos do Worker, deletar vídeos, imagens e zip para liberar recurso.
- Notificar que o vídeo foi processado com sucesso
- Fazer o commit/remoção da mensagem que foi consumida pelo Worker

Há também o processo alternativo onde se algum dos passos acima falharem, ao invés de ser notificado o sucesso do processamento do vídeo, será notificado que ele foi processado com falha.

## Validar que o evento se trata de um arquivo válido para processamento

Dentro da mensagem consumida existem várias informações que são usadas na validação. Uma delas é de que o formato do arquivo precisa ser o formato de um vídeo válido. Até o momento esses são os formatos permitidos:

 - mp4
 - avi
 - mov
 - mkv
 - flv
 - wmv

 ## Notificar que o vídeo está começando a ser processado

 Isso é usado para atualizar o registro da base de dados e dessa forma o usuário pode saber se o vídeo já começou a ser processado ou não ao fazer buscas na API

 ## Baixar o vídeo do S3 localmente

 Para fazer a extração de imagens é necessário o arquivo existir localmente, então o vídeo é baixado do S3 e armazenado dentro da aplicação de forma temporária

 ## Fazer a extração das imagens

 O Worker usa o FFmpeg para trabalhar com o vídeo, e executa o comando necessário para gerar imagens a partir de intervalos do vídeo (por exemplo, uma imagem a partir do frame que aparece aos 10 segundos de vídeo). Essas imagens são armazenadas localmente tambem de forma temporária

 ## Criar um arquivo zip a partir das imagens geradas

 Após gerar as imagens, todas elas são compiladas e compactadas em um arquivo zip, tambem armazenado localmente.

 ## Fazer o upload do arquivo zip e da primeira imagem gerada, no mesmo local onde está o vídeo no S3

 O vídeo fica dentro de um bucket no S3, em uma estrutura de pastas que permite separar por usuário e por vídeo a localização. Para a mesma pasta onde está o vídeo do usuário, são enviados o arquivo zip produzido e o primeiro frame que foi gerado. Assim cada vídeo terá ao seu lado o zip e a imagem.

 ## Limpar arquivos do Worker, deletar vídeos, imagens e zip para liberar recurso.

 O vídeo, todas as imagens e o arquivo zip são deletados localmente, para que a aplicação não gaste espaço em disco, podendo causar instabilidade ou gasto de recursos.

 ## Notificar que o vídeo foi processado com sucesso

 Essa notificação será usada para atualizar o registro na base de dados e tambem iniciar o processo de notificação ao usuário.

 ## Fazer o commit da mensagem que foi consumida pelo Worker

 No SQS esse processo significa deletar a mensagem da fila, assim ela não será processada novamente caso haja restart da aplicação ou algo do gênero.

 ## Notificar que o vídeo foi processado com falha

 Isso somente ocorre se algum passo anterior falhou por alguma razão. Nesse cenário é feita essa notificação para atualizar o registro na base de dados e tambem iniciar o processo de notificação ao usuário.