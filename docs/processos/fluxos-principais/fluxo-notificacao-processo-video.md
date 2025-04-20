# Fluxo de notificação após processamento de vídeo

Quando o worker conclui com falha ou sucesso o processamento de um vídeo, ele gera eventos que são consumidos por um Worker que fará o acerto da informação na base de dados, e esse mesmo worker fará o disparo de um evento para que inicie a notificação ao usuário por e-mail.

Esse evento é consumido por uma Lambda Function que de acordo com as informações do evento fará um disparo de e-mail ao usuário, indicando se o processo foi feito com sucesso ou falha.

[Repositório do Worker de Atualização de Status](https://github.com/SnackTechTeam/snacktech-vidsnap-lambda-notification)