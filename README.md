# Payment Provider SDK
Package de definições para integração de um provedor de pagamentos para a integração com sistemas da plataforma Fpass.
## Responsability
Receber a solicitação de pedidos e informar resultado de sucesso e falha da cobrança dos pedidos.
## Overview System Integration 
![Overview System Integration](https://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.githubusercontent.com/Holding-Fpass/payment-provider-sdk/main/uml/overview-systems-integration.iuml)

### Checkout Forward
O redirecionamento dos sistemas da plataforma Fpass encaminharam o usuário no fluxo de experiência de pagamento para uma link onde este realizará o pagamento do serviço ou produto:

Request (GET)
```sh
curl --location -g --request GET '{schema}://{checkoutUrl}/{token}'
```

### Checkout Product Metadata
Os metadados para prenchimento de dados visuais da interface serão obtidos por URL segura:

Request (GET)
```sh
curl --location -g --request GET 'https://api.fpass.com.br/payment-provider/{token}'
```

Response (200)
```json
{
  "id": "12345",
  "totalValue": "10000",
  "receipt": "FPASS COMPANY",
  "transactionTax": "GRATUITO",
  "items": [
    {
      "title": "Python for dummies!",
      "imageURL": "https://m.media-amazon.com/images/I/51ZkYg-SHSL._SX260_.jpg",
      "description": "Learn Python in 6 hours!",
      "price": "10000"
    }
  ]
}
```

### Checkout Webhook
Após a conclusão do processo de cobrança deve haver uma notificação do eventos gerados.

Request (POST)
```sh
curl --location -g --request POST 'https://api.fpass.com.br/webhook/{providerCode}' \
--header 'x-api-key: {uuidv4}' \
--header 'x-hash: {object-hash with secret}' \
--header 'Content-Type: application/json' \
--data-raw '{
  "id":"{uuidv4}",
  "type": "created | processing | failed | paid",
  "token": "{token}",
  "message": "string",
  "date": "2022-10-22T23:51:12.832Z"
}'
```

Response (HTTP 201)
```json
{
  "success": true
}
```

## Hash
A geração do hash seguem os seguintes passos:
1. Ao _payload_ é incluido o parâmetro _key_ com o valor de uma chave secreta, compartilhada entre as duas partes (origem e destino) que não é trafegada.
```json
{
  "foo": "bar",
  "key": "{uuidv4}"
}
```
2. É gerado o hash com algoritmo SHA1 do conteúdo.
Exemplo de implementação: https://www.npmjs.com/package/object-hash
```typescript
var hash = require('object-hash');
hash({"foo": "bar", "key": "{uuidv4}"}) // hash => '67b69634f9880a282c14a0f0cb7ba20cf5d677e9'
```
3. Código _hash_ é incluído no _header_ no parametro _x-hash_ 