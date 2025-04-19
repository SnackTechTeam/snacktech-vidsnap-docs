# Fluxo de Envio de Vídeo 

Uma vez a requisição de envio de vídeo chega na API do sistema, a mesma cria um registro na base de dados, vinculando o vídeo ao usuário. Além disso cria uma Url Pré Assinada que tambem é vinculada ao vídeo. 

Uma vez que a url pré assinada é usada, ou seja, ela é chamada fazendo o download do vídeo e armazenando corretamente no S3, assim que o armazenamento ocorre é gerado um evento do S3 para uma fila de SQS indicando qual foi o arquivo, pelos dados do arquivo (onde ficou salvo, qual é o caminho dele, etc) é possível identificar não só qual é o vídeo, mas a quem ele pertence