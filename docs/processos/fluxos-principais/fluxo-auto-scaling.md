# Fluxo de escalabilidade automática

Aplicações do VidSnap como a API e os Workers trabalham com computação em nuvem via Kubernetes, através do Amazon EKS.

A API foi configurada para escalar com base no uso de CPU dos PODs, consequências do aumento de requisições que chegam até ela. Os PODs dos workers foram configurados para escalar com base em suas filas SQS. Viablizando a alocação desses recursos as necessidades reais de processamento.

Todas essas necessidades de scaling são atendidas de forma automática através do Horizontal Pod Autoscaling do próprio Kubernetes e do KEDA (Kubernetes Event-driven Autoscaling). Estes 2 itens permitem executar o scaling automático de nossas aplicações de acordo com as regras definidas.