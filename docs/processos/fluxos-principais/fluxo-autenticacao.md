# Fluxo de Autenticação e Autorização

Toda requisição ao VidSnap passa pelo API Gateway. 

Antes de fazer a requisição que o usuário deseja (enviar um vídeo, buscar uma lista ou um vídeo específico, etc.) é necessário que se autentique através do Cognito. Com essa autenticação, ele receberá um token que deve ser fornecido à rota desejada.

Ao chamar a rota, o cliente/usuário deve enviar um token do tipo ID para que o API Gateway proceda com a identificação e autorização do usuário através Lambda Function de autenticação. Essa função usará o Cognito para validar se o token fornecido pertence a um usuário válido e retornará para o API Gateway o ID e o email desse usuário. Por sua vez, O API Gateway vai adicionar os dados do usuário nos headers da requisição e encaminhar a requisição para a API de fato. Dessa forma, apenas requisições de usuários chegaram até a API. Além disso, a identificação do usuário no API Gateway proteje a API contra ataques que tentem acessar os dados de outro usuáro através da manipulação de credenciais.