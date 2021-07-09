# Payment Provider SDK
Package de definições para integração de um provedor de pagamentos para a integração com sistemas da plataforma Fpass.
## Responsability
Receber a solicitação de pedidos e informar resultado de sucesso e falha da cobrança dos pedidos.
## Overview System Integration 
![Overview System Integration](https://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.githubusercontent.com/Holding-Fpass/payment-provider-sdk/main/uml/overview-systems-integration.iuml)

### Checkout Forward
O redirecionamento dos sistemas da plataforma Fpass encaminharam o usuário no fluxo de experiência de pagamento para uma link onde este realizará o pagamento do serviço ou produto:

```sh
curl --location -g --request GET '{schema}://{checkoutUrl}/{token}'
```

### Checkout Product Metadata
Os metadados para prenchimento de dados visuais da interface serão obtidos por URL segura:

```sh
curl --location -g --request POST 'https://api.fpass.com.br/payment-provider/{token}' \
--header 'x-api-key: {uuidv4}'
```

### Checkout Webhook
Após a conclusão do processo de cobrança deve haver uma notificação do eventos gerados.

```sh
curl --location -g --request POST 'https://api.fpass.com.br/webhook/{providerCode}' \
--header 'x-api-key: {uuidv4}' \
--header 'Content-Type: application/json' \
--data-raw '{
  "id":"{uuidv4}",
  "type": "created | processing | failed | paid",
  "token": "{token}",
  "message": "string",
  "date": "2022-10-22T23:51:12.832Z",
  "hash": "67b69634f9880a282c14a0f0cb7ba20cf5d677e9"
}'
```