# Fluxo de Autenticação e Autorização

Toda requisição ao VidSnap passa pelo API Gateway. 

Antes de fazer a requisição que o usuário deseja (enviar um vídeo, buscar uma lista ou um vídeo específico, etc.) é necessário que se autentique através do Cognito. Com essa autenticação, ele receberá um token que pode ser fornecido à rota desejada.

Ao chamar a rota, o API Gateway chamará a Lambda Function de autorização, que validará o token fornecido. Uma vez validado com sucesso, a requisição será direcionada para a API de fato, onde ocorrerão as operações necessárias