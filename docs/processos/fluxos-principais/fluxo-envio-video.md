# Fluxo de Envio de Vídeo 

Uma vez a requisição de envio de vídeo chega na API do sistema, a mesma cria um registro na base de dados, vinculando o vídeo ao usuário. Além disso cria uma Url Pré Assinada através da qual o usuário poderá fazer o upload do video para o serviço AWS S3. 

Quando o vídeo é recebido no S3 através da URL Pré Assinada acima, ocorre a geração de um evento do S3 que envia uma mensagem para uma fila SQS solicitando o processamento de um novo arquivo. Pelos dados do arquivo (onde ficou salvo, qual é o caminho dele, etc) é possível identificar não só qual é o vídeo, mas a quem ele pertence. Com base nesses dados o worker de processamento de video irá realizar a próxima etapa do processo.

Essa abordagem permite desacoplar a aplicação (API) do processo de upload/recepção do arquivo. A transferencia ocorre diretamente para o S3 e este cuida de notificar a conclusão bem sucessedida do upload.