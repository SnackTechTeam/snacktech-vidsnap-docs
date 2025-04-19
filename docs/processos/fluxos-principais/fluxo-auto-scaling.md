# Fluxo de escalabilidade automática

Aplicações do VidSnap como a API e os Workers trabalham com computação em nuvem via Kubernetes, através do Amazon EKS.

A API precisa escalar se começar a requisitar mais memória e/ou cpu, consequências do aumento de requisições que chegam até ela, e os workers precisam escalar se nas suas filas SQS começar a ter um grande volume de mensagens esperando para serem processadas.

Todas essas necessidades de scaling são atendidas de forma automática através do Horizontal Pod Autoscaling do próprio Kubernetes e do KEDA, Kubernetes Event-driven Autoscaling. Estes 2 itens permitem executar o scaling automático de nossas aplicações de acordo com as regras definidas.