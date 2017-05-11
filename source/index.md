---
title: Integração API

language_tabs:
  - json: JSON
  - shell: cURL

search: true
---

# Integração Pagador API

O objetivo desta documentação é orientar o desenvolvedor sobre como integrar com o API do Pagador, gateway de pagamentos da Braspag, descrevendo os serviços disponíveis com exemplos de requisição e respostas.

Todas as operações requerem credenciais de acesso (Merchant ID e Merchant Key) específicos para respectivos ambientes: **Sandbox**, **Homologação** e **Produção**. Para executar uma operação, combine o endpoint base do ambiente com o endpoint da operação desejada e envie utilizando o VERBO HTTP conforme descrito na operação.

## Ambientes

### Ambiente Sandbox

Experimente as nossas APIs sem compromisso! 

|Informação|Descrição|
|----------|---------|
|Credenciais de Acesso à API|Acesse o [Cadastro do Sandbox](https://cadastrosandbox.braspag.com.br/) e crie uma conta de testes.<BR>Ao fim do cadastro você receberá um `MerchantId` e um `MerchantKey`,<BR> que deverão ser utilizados para autenticar todas as requisições feitas para os endpoints da API|
|Endpoint Transacional|https://apisandbox.braspag.com.br/|
|Endpoint para Serviços de Consultas|https://apiquerysandbox.braspag.com.br/|

### Ambiente de Homologação

Valide seu desenvolvimento junto à nossa equipe! 

|Informação|Descrição|
|----------|---------|
|Credenciais de Acesso à API|Consulte-nos!|
|Endpoint Transacional|https://apihomolog.braspag.com.br/|
|Endpoint para Serviços de Consultas|https://apiqueryhomolog.braspag.com.br/|

### Ambiente de Produção

Já estou pronto para entrar em Produção!

|Informação|Descrição|
|----------|---------|
|Credenciais de Acesso à API|Consulte-nos!|
|Endpoint Transacional|https://api.braspag.com.br/|
|Endpoint para Serviços de Consultas|https://apiquery.braspag.com.br/|

## Suporte Braspag

<aside class="notice">A Braspag oferece suporte de alta disponibilidade, com atendimento de segunda à sexta, das 9h às 19h, e telefone de emergência 24×7, através de ferramenta via web. Contamos com a equipe que poderá atender em português, inglês e espanhol.</aside>

* Atendimento Web: [Suporte Braspag via Portal](http://suporte.braspag.com.br/)
* E-mail: [mailto:suporte@braspag.com.br](suporte@braspag.com.br)
* Telefone: (11)2184-0550

# Visão Geral

Aqui você pode encontrar detalhamentos de todas as operações disponíveis na API REST do Pagador. Essas operações podem ser executadas utilizando sua chave específica nos ambientes (Sandbox, Homologação ou Produção).

## Características da Solução

A solução API Pagador foi desenvolvida com a tecnologia REST, que é padrão de mercado e independe da tecnologia utilizada por nossos clientes. Desta forma, é possível integrar-se utilizando as mais variadas linguagens de programação, tais como: ASP, ASP. Net, Java, PHP, Ruby, Python, entre outras.

Entre outras características, os atributos que mais se destacam na plataforma Braspag eCommerce:

* **Ausência de aplicativos proprietários**: não é necessário instalar aplicativos no ambiente da loja virtual em nenhuma hipótese.
* **Simplicidade**: o protocolo utilizado é puramente o HTTPS.
* **Facilidade de testes**: a plataforma Braspag oferece um ambiente Sandbox publicamente acessível, que permite ao desenvolvedor a criação de uma conta de testes sem a necessidade de credenciamento, facilitando e agilizando o início da integração.
* **Credenciais**: o tratamento das credenciais do cliente (número de afiliação e chave de acesso) trafega no cabeçalho da requisição HTTP da mensagem.
* **Segurança**: a troca de informações se dá sempre entre o Servidor da Loja e da Braspag, ou seja, sem o browser do comprador.
* **Multiplataforma**: a integração é realizada através de Web Service REST.

## Arquitetura

A integração é realizada através de serviços disponibilizados como Web Services. O modelo empregado é bastante simples: Existem duas URLs (endpoint), uma específica operações que causam efeitos colaterais - como autorização, captura e cancelamento de transações, e uma URL específica para operações que não causam efeitos colaterais, como pesquisa de transações. Essas duas URLs receberão as mensagens HTTP através dos métodos POST, GET ou PUT. Cada tipo de mensagem deve ser enviada para um recurso identificado através do path.

* **POST** - O método HTTP POST é utilizado na criação dos recursos ou no envio de informações que serão processadas. Por exemplo, criação de uma transação.
* **PUT** - O método HTTP PUT é utilizado para atualização de um recurso já existente. Por exemplo, captura ou cancelamento de uma transação previamente autorizada.
* **GET** - O método HTTP GET é utilizado para consultas de recursos já existentes. Por exemplo, consulta de transações.

# Pagamentos com Cartão de Crédito

Para que você possa desfrutar de todos os recursos disponíveis em nossa API, é importante que antes você conheça os conceitos envolvidos no processamento de uma transação de cartão de crédito.

* **Autorização**: A autorização (ou pré-autorização) é uma operação que viabiliza o processamento de uma venda com um cartão de crédito.A pré-autorização apenas sensibiliza o limite do cliente, mas ainda não gera cobrança na fatura para o consumidor. Desta forma, é necessário uma segunda operação, chamada 'captura'. 
* **Captura**: Ao realizar uma pré-autorização, é necessário confirmá-la para que a cobrança seja efetivada. O tempo limite para capturar uma transação pré-autorizada varia de adquirente para adquirente, que pode ser por exemplo, de até 5 dias após a data da pré-autorização.
* **Captura Automática**: É quando uma transação é autorizada e capturada no mesmo momento, isentando do lojista enviar uma confirmação posterior.
* **Cancelamento**: O cancelamento é necessário quando, por algum motivo, não se quer mais efetivar uma venda. No caso de uma pré-autorização, o cancelamento irá liberar o limite do cartão que foi sensibilizado em uma pré-autorização. Quando a transação já estiver sido capturada, o cancelamento irá desfazer a venda, mas deve ser executado até às 23:59:59 da data da autorização/captura.
* **Estorno**: O estorno é aplicável quando uma transação criada no dia anterior ou antes já estiver capturada. Neste caso, a transação será submetida no processo de 'chargeback' pela adquirente. 
* **Autenticação**: O processo de autenticação possibilita realizar uma venda a qual passará pelo processo de autenticação do banco emissor do cartão, assim trazendo mais segurança para a venda e transferindo para o banco, o risco de fraude.
* **Cartão Protegido**: É uma plataforma que permite o armazenamento seguro de dados sensíveis de cartão de crédito. Estes dados são transformados em um código criptografrado chamado de "token”, que poderá ser armazenado em banco de dados. Com a plataforma, a loja poderá oferecer recursos como "Compra com 1 clique” e "Retentativa de envio de transação”, sempre preservando a integridade e a confidencialidade das informações.
* **Antifraude**: É uma plataforma de prevenção à fraude que fornece uma análise de risco detalhada das compras on-line. Este processo é totalmente transparente para o portador do cartão. De acordo com os critérios preestabelecidos, o pedido pode ser automaticamente aceito, recusado ou encaminhado para análise manual.
* **Recorrente**: A Recorrência Inteligente é um recurso indispensável para estabelicimentos que precisam cobrar regularmente por seus produtos/serviços.
É muito utilizado para assinaturas de revistas, mensalidades, licenças de software, entre outros. Os lojistas contarão com recursos diferenciados para modelar sua cobrança de acordo com o seu negócio, pois toda parametrização é configurável, tais como: periodicidade, data de início e fim, quantidade de tentativas, intervalo entre elas, entre outros.

<aside class="warning">Uma transação autorizada somente gera o crédito para o lojista se ela for capturada.</aside>
<aside class="warning">A disponibilidade do serviço de Estorno varia de adquirente para adquirente.</aside>

## Criando uma transação básica

Exemplo de uma transação com dados obrigatórios para um simples processamento de transação de cartão de crédito. Algumas funcionalidades, tais como Captura Automática, Autenticação, Antifraude, Velocity entre outras, requerem preenchimento de mais dados. Neste caso, por favor, consulte os demais exemplos no manual.

### Requisição

<aside class="request"><span class="method post">POST</span> <span class="endpoint">/v2/sales/</span></aside>

```json
{
   "MerchantOrderId":"2017051001",
   "Customer":{
      "Name":"Nome do Cliente"
   },
   "Payment":{
     "Provider":"Simulado",
     "Type":"CreditCard",
     "Amount":10000,
     "Installments":1,
     "CreditCard":{
         "CardNumber":"4551870000000181",
         "Holder":"Nome do Portador",
         "ExpirationDate":"12/2021",
         "SecurityCode":"123",
         "Brand":"Visa"
     }
   }
}
```

```shell
curl
--request POST "https://apihomolog.braspag.com.br/v2/sales/"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
   "MerchantOrderId":"2017051001",
   "Customer":{
      "Name":"Nome do Cliente"
   },
   "Payment":{
     "Provider":"Simulado",
     "Type":"CreditCard",
     "Amount":10000,
     "Installments":1,
     "CreditCard":{
         "CardNumber":"4551870000000181",
         "Holder":"Nome do Portador",
         "ExpirationDate":"12/2021",
         "SecurityCode":"123",
         "Brand":"Visa"
     }
   }
}
--verbose
```

|Propriedade|Tipo|Tamanho|Obrigatório|Descrição|
|-----------|----|-------|-----------|---------|
|`MerchantId`|Guid|36|Sim|Identificador da loja na Braspag|
|`MerchantKey`|Texto|40|Sim|Chave Publica para Autenticação Dupla na Braspag|
|`RequestId`|Guid|36|Não|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT|
|`MerchantOrderId`|Texto|50|Sim|Numero de identificação do Pedido|
|`Customer.Name`|Texto|255|Sim|Nome do comprador|
|`Payment.Provider`|Texto|15|Sim|Nome da provedora de Meio de Pagamento|
|`Payment.Type`|Texto|100|Sim|Tipo do Meio de Pagamento|
|`Payment.Amount`|Número|15|Sim|Valor do Pedido (ser enviado em centavos)|
|`Payment.Installments`|Número|2|Sim|Número de Parcelas|
|`CreditCard.CardNumber`|Texto|16|Sim|Número do Cartão do comprador|
|`CreditCard.Holder`|Texto|25|Sim|Nome do Comprador impresso no cartão|
|`CreditCard.ExpirationDate`|Texto|7|Sim|Data de validade impresso no cartão, no formato MM/AAAA|
|`CreditCard.SecurityCode`|Texto|4|Sim|Código de segurança impresso no verso do cartão|
|`CreditCard.Brand`|Texto|10|Sim |Bandeira do cartão|

### Resposta

```json
{
  "MerchantOrderId": "2017051001",
  "Customer": {
    "Name": "Nome do Cliente"
  },
  "Payment": {
    "ServiceTaxAmount": 0,
    "Installments": 1,
    "Interest": "ByMerchant",
    "Capture": false,
    "Authenticate": false,
    "Recurrent": false,
    "CreditCard": {
      "CardNumber": "455187******0181",
      "Holder": "Nome do Portador",
      "ExpirationDate": "12/2021",
      "SaveCard": false,
      "Brand": "Visa"
    },
    "ProofOfSale": "2539492",
    "AcquirerTransactionId": "0510042539492",
    "AuthorizationCode": "759497",
    "PaymentId": "f8078b32-be17-4c35-b164-ad74c3cd0725",
    "Type": "CreditCard",
    "Amount": 10000,
    "ReceivedDate": "2017-05-10 16:25:38",
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Simulado",
    "ReasonCode": 0,
    "ReasonMessage": "Successful",
    "Status": 1,
    "ProviderReturnCode": "4",
    "ProviderReturnMessage": "Operation Successful",
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/f8078b32-be17-4c35-b164-ad74c3cd0725"
      },
      {
        "Method": "PUT",
        "Rel": "capture",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/f8078b32-be17-4c35-b164-ad74c3cd0725/capture"
      },
      {
        "Method": "PUT",
        "Rel": "void",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/f8078b32-be17-4c35-b164-ad74c3cd0725/void"
      }
    ]
  }
}
```

```shell
--header "Content-Type: application/json"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
  "MerchantOrderId": "2017051001",
  "Customer": {
    "Name": "Nome do Cliente"
  },
  "Payment": {
    "ServiceTaxAmount": 0,
    "Installments": 1,
    "Interest": "ByMerchant",
    "Capture": false,
    "Authenticate": false,
    "Recurrent": false,
    "CreditCard": {
      "CardNumber": "455187******0181",
      "Holder": "Nome do Portador",
      "ExpirationDate": "12/2021",
      "SaveCard": false,
      "Brand": "Visa"
    },
    "ProofOfSale": "2539492",
    "AcquirerTransactionId": "0510042539492",
    "AuthorizationCode": "759497",
    "PaymentId": "f8078b32-be17-4c35-b164-ad74c3cd0725",
    "Type": "CreditCard",
    "Amount": 10000,
    "ReceivedDate": "2017-05-10 16:25:38",
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Simulado",
    "ReasonCode": 0,
    "ReasonMessage": "Successful",
    "Status": 1,
    "ProviderReturnCode": "4",
    "ProviderReturnMessage": "Operation Successful",
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/f8078b32-be17-4c35-b164-ad74c3cd0725"
      },
      {
        "Method": "PUT",
        "Rel": "capture",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/f8078b32-be17-4c35-b164-ad74c3cd0725/capture"
      },
      {
        "Method": "PUT",
        "Rel": "void",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/f8078b32-be17-4c35-b164-ad74c3cd0725/void"
      }
    ]
  }
}
```

|Propriedade|Descrição|Tipo|Tamanho|Formato|
|-----------|---------|----|-------|-------|
|`AcquirerTransactionId`|Id da transação no provedor de meio de pagamento|Texto|40|Texto alfanumérico|
|`ProofOfSale`|Número do Comprovante de Venda|Texto|20|Texto alfanumérico|
|`AuthorizationCode`|Código de autorização|Texto|300|Texto alfanumérico|
|`PaymentId`|Campo Identificador do Pedido|Guid|36|xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx|
|`ReceivedDate`|Data em que a transação foi recebida pela Brapag|Texto|19|AAAA-MM-DD HH:mm:SS|
|`ReasonCode`|Código de retorno da Adquirência|Texto|32|Texto alfanumérico|
|`ReasonMessage`|Mensagem de retorno da Adquirência|Texto|512|Texto alfanumérico|
|`Status`|Status da Transação|Byte|2|1|
|`ProviderReturnCode`|Código retornado pelo provedor do meio de pagamento (adquirente e bancos)|Texto|32|57|
|`ProviderReturnMessage`|Mensagem retornada pelo provedor do meio de pagamento (adquirente e bancos)|Texto|512|Transação Aprovada|

## Criando uma transação com dados do comprador

Este é um exemplo de uma transação com todos os dados preenchidos. Isso inclui os dados do comprador e campos específicos para definir o comportamento de autorização, autenticação e dados extras.

### Requisição

<aside class="request"><span class="method post">POST</span> <span class="endpoint">/v2/sales/</span></aside>

```json
{  
   "MerchantOrderId":"2017051002",
   "Customer":{  
      "Name":"Nome do Comprador",
      "Identity":"12345678909",
      "IdentityType":"CPF",
      "Email":"comprador@braspag.com.br",
      "Birthdate":"1991-01-02",
      "Address":{  
         "Street":"Alameda Xingu",
         "Number":"512",
         "Complement":"27 andar",
         "ZipCode":"12345987",
         "City":"São Paulo",
         "State":"SP",
         "Country":"BRA",
		 "District":"Alphaville"
      },
	  "DeliveryAddress": {
         "Street":"Alameda Xingu",
         "Number":"512",
         "Complement":"27 andar",
         "ZipCode":"12345987",
         "City":"São Paulo",
         "State":"SP",
         "Country":"BRA",
		 "District":"Alphaville"
	    }
   },
   "Payment":{  
     "Provider":"Simulado",
     "Type":"CreditCard",
     "Amount":10000,
     "ServiceTaxAmount":0,
     "Currency":"BRL",
     "Country":"BRA",
     "Installments":1,
     "Interest":"ByMerchant",
     "Capture":true,
     "Authenticate":false,    
     "Recurrent": false,
     "SoftDescriptor":"Mensagem",
     "CreditCard":{  
         "CardNumber":"4551870000000181",
         "Holder":"Nome do Portador",
         "ExpirationDate":"12/2021",
         "SecurityCode":"123",
         "Brand":"Visa",
         "SaveCard":"false"
     },
     "ExtraDataCollection":[{
         "Name":"NomeDoCampo",
         "Value":"ValorDoCampo"
     }]
   }
}
```

```shell
curl
--request POST "https://apisandbox.braspag.com.br/v2/sales/"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{  
   "MerchantOrderId":"2017051002",
   "Customer":{  
      "Name":"Nome do Comprador",
      "Identity":"12345678909",
      "IdentityType":"CPF",
      "Email":"comprador@braspag.com.br",
      "Birthdate":"1991-01-02",
      "Address":{  
         "Street":"Alameda Xingu",
         "Number":"512",
         "Complement":"27 andar",
         "ZipCode":"12345987",
         "City":"São Paulo",
         "State":"SP",
         "Country":"BRA",
		 "District":"Alphaville"
      },
	  "DeliveryAddress": {
         "Street":"Alameda Xingu",
         "Number":"512",
         "Complement":"27 andar",
         "ZipCode":"12345987",
         "City":"São Paulo",
         "State":"SP",
         "Country":"BRA",
		 "District":"Alphaville"
	    }
   },
   "Payment":{  
     "Provider":"Simulado",
     "Type":"CreditCard",
     "Amount":10000,
     "ServiceTaxAmount":0,
     "Currency":"BRL",
     "Country":"BRA",
     "Installments":1,
     "Interest":"ByMerchant",
     "Capture":true,
     "Authenticate":false,    
     "Recurrent": false,
     "SoftDescriptor":"Mensagem",
     "CreditCard":{  
         "CardNumber":"4551870000000181",
         "Holder":"Nome do Portador",
         "ExpirationDate":"12/2021",
         "SecurityCode":"123",
         "Brand":"Visa",
         "SaveCard":"false"
     },
     "ExtraDataCollection":[{
         "Name":"NomeDoCampo",
         "Value":"ValorDoCampo"
     }]
   }
}
--verbose
```

|Propriedade|Tipo|Tamanho|Obrigatório|Descrição|
|-----------|----|-------|-----------|---------|
|`MerchantId`|Guid|36|Sim|Identificador da loja na Braspag|
|`MerchantKey`|Texto|40|Sim|Chave Publica para Autenticação Dupla na Braspag|
|`RequestId`|Guid|36|Não|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT|
|`MerchantOrderId`|Texto|50|Sim|Numero de identificação do Pedido|
|`Customer.Name`|Texto|255|Sim|Nome do comprador|
|`Customer.Identity`|Texto |14 |Não|Número do RG, CPF ou CNPJ do Cliente| 
|`Customer.IdentityType`|Texto|255|Não|Tipo de documento de identificação do comprador (CPF ou CNPJ)|
|`Customer.Email`|Texto|255|Não|Email do comprador|
|`Customer.Birthdate`|Date|10|Não|Data de nascimento do Comprador no formato AAAA-MM-DD|
|`Customer.Address.Street`|Texto|255|Não|Endereço de contato do comprador|
|`Customer.Address.Number`|Texto|15|Não|Número endereço de contato do comprador|
|`Customer.Address.Complement`|Texto|50|Não|Complemento do endereço de contato do Comprador|
|`Customer.Address.ZipCode`|Texto|9|Não|CEP do endereço de contato do comprador|
|`Customer.Address.City`|Texto|50|Não|Cidade do endereço de contato do comprador|
|`Customer.Address.State`|Texto|2|Não|Estado do endereço de contato do comprador|
|`Customer.Address.Country`|Texto|35|Não|Pais do endereço de contato do comprador|
|`Customer.Address.District`|Texto |50 |Não|Bairro do Comprador. |
|`Customer.DeliveryAddress.Street`|Texto|255|Não|Endereço do comprador|
|`Customer.DeliveryAddress.Number`|Texto|15|Não|Número do endereço de entrega do pedido|
|`Customer.DeliveryAddress.Complement`|Texto|50|Não|Complemento do endereço de entrega do pedido|
|`Customer.DeliveryAddress.ZipCode`|Texto|9|Não|CEP do endereço de entrega do pedido|
|`Customer.DeliveryAddress.City`|Texto|50|Não|Cidade do endereço de entrega do pedido|
|`Customer.DeliveryAddress.State`|Texto|2|Não|Estado do endereço de entrega do pedido|
|`Customer.DeliveryAddress.Country`|Texto|35|Não|Pais do endereço de entrega do pedido|
|`Customer.DeliveryAddress.District`|Texto |50 |Não|Bairro do Comprador. |
|`Payment.Provider`|Texto|15|Sim|Nome da provedora de Meio de Pagamento|
|`Payment.Type`|Texto|100|Sim|Tipo do Meio de Pagamento|
|`Payment.Amount`|Número|15|Sim|Valor do Pedido (ser enviado em centavos)|
|`Payment.ServiceTaxAmount`|Número|15|Sim|Montante do valor da autorização que deve ser destinado à taxa de serviço. Obs.: Esse valor não é adicionado ao valor da autorização|
|`Payment.Currency`|Texto|3|Não|Moeda na qual o pagamento será feito (BRL / USD / MXN / COP / CLP / ARS / PEN / EUR / PYN / UYU / VEB / VEF / GBP)|
|`Payment.Country`|Texto|3|Não|País na qual o pagamento será feito|
|`Payment.Installments`|Número|2|Sim|Número de Parcelas|
|`Payment.Interest`|Texto|10|Não|Tipo de parcelamento - Loja (ByMerchant) ou Cartão (ByIssuer)|
|`Payment.Capture`|Booleano|---|Não (Default false)|Booleano que indica se a autorização deve ser com captura automática (true) ou não (false). Deverá verificar junto à adquirente a disponibilidade desta funcionalidade|
|`Payment.Authenticate`|Booleano|---|Não (Default false)|Booleano que indica se a transação deve ser autenticada (true) ou não (false). Deverá verificar junto à adquirente a disponibilidade desta funcionalidade|
|`Payment.Recurrent`|Booleano|---|Não (Default false)|Booleano que indica se a transação é do tipo recorrente (true) ou não (false). Este com valor true não originará uma nova Recorrência, apenas permitirá a realização de uma transação sem a necessidade de envio do CVV. Somente para transações Cielo. Authenticate deve ser false quando Recurrent é true|
|`Payment.SoftDescriptor`|Texto|13|Não|Texto que será impresso na fatura do portador|
|`Payment.ExtraDataCollection.Name`|Texto|50|Não|Nome do campo que será gravado o Dado Extra|
|`Payment.ExtraDataCollection.Value`|Texto|1024|Não|Valor do campo que será gravado o Dado Extra|
|`CreditCard.CardNumber`|Texto|16|Sim|Número do Cartão do comprador|
|`CreditCard.Holder`|Texto|25|Sim|Nome do portador impresso no cartão|
|`CreditCard.ExpirationDate`|Texto|7|Sim|Data de validade impresso no cartão|
|`CreditCard.SecurityCode`|Texto|4|Sim|Código de segurança impresso no verso do cartão|
|`CreditCard.Brand`|Texto|10|Sim |Bandeira do cartão|
|`CreditCard.SaveCard`|Booleano|---|Não (Default false)|Booleano que identifica se o cartão será salvo para gerar o token (CardToken)|

### Resposta

```json
{
  "MerchantOrderId": "2017051002",
  "Customer": {
    "Name": "Nome do Comprador",
    "Identity": "12345678909",
    "IdentityType": "CPF",
    "Email": "comprador@braspag.com.br",
    "Birthdate": "1991-01-02",
    "Address": {
      "Street": "Alameda Xingu",
      "Number": "512",
      "Complement": "27 andar",
      "ZipCode": "12345987",
      "City": "São Paulo",
      "State": "SP",
      "Country": "BRA",
	  "District":"Alphaville"
    },
    "DeliveryAddress": {
      "Street": "Alameda Xingu",
      "Number": "512",
      "Complement": "27 andar",
      "ZipCode": "12345987",
      "City": "São Paulo",
      "State": "SP",
      "Country": "BRA",
	  "District":"Alphaville"
    }
  },
  "Payment": {
    "ServiceTaxAmount": 0,
    "Installments": 1,
    "Interest": "ByMerchant",
    "Capture": true,
    "Authenticate": false,
    "Recurrent": false,
    "CreditCard": {
      "CardNumber": "455187******0181",
      "Holder": "Nome do Portador",
      "ExpirationDate": "12/2021",
      "SaveCard": false,
      "Brand": "Visa"
    },
    "ProofOfSale": "20170510053219433",
    "AcquirerTransactionId": "0510053219433",
    "AuthorizationCode": "936403",
    "SoftDescriptor": "Mensagem",
    "VelocityAnalysis": {
      "Id": "c374099e-c474-4916-9f5c-f2598fec2925",
      "ResultMessage": "Accept",
      "Score": 0
    },
    "PaymentId": "c374099e-c474-4916-9f5c-f2598fec2925",
    "Type": "CreditCard",
    "Amount": 10000,
    "ReceivedDate": "2017-05-10 17:32:19",
    "CapturedAmount": 10000,
    "CapturedDate": "2017-05-10 17:32:19",
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Simulado",
    "ExtraDataCollection": [
      {
        "Name": "NomeDoCampo",
        "Value": "ValorDoCampo"
      }
    ],
    "ReasonCode": 0,
    "ReasonMessage": "Successful",
    "Status": 2,
    "ProviderReturnCode": "6",
    "ProviderReturnMessage": "Operation Successful",
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/c374099e-c474-4916-9f5c-f2598fec2925"
      },
      {
        "Method": "PUT",
        "Rel": "void",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/c374099e-c474-4916-9f5c-f2598fec2925/void"
      }
    ]
  }
}
```

```shell
--header "Content-Type: application/json"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
  "MerchantOrderId": "2017051002",
  "Customer": {
    "Name": "Nome do Comprador",
    "Identity": "12345678909",
    "IdentityType": "CPF",
    "Email": "comprador@braspag.com.br",
    "Birthdate": "1991-01-02",
    "Address": {
      "Street": "Alameda Xingu",
      "Number": "512",
      "Complement": "27 andar",
      "ZipCode": "12345987",
      "City": "São Paulo",
      "State": "SP",
      "Country": "BRA",
	  "District":"Alphaville"
    },
    "DeliveryAddress": {
      "Street": "Alameda Xingu",
      "Number": "512",
      "Complement": "27 andar",
      "ZipCode": "12345987",
      "City": "São Paulo",
      "State": "SP",
      "Country": "BRA",
	  "District":"Alphaville"
    }
  },
  "Payment": {
    "ServiceTaxAmount": 0,
    "Installments": 1,
    "Interest": "ByMerchant",
    "Capture": true,
    "Authenticate": false,
    "Recurrent": false,
    "CreditCard": {
      "CardNumber": "455187******0181",
      "Holder": "Nome do Portador",
      "ExpirationDate": "12/2021",
      "SaveCard": false,
      "Brand": "Visa"
    },
    "ProofOfSale": "20170510053219433",
    "AcquirerTransactionId": "0510053219433",
    "AuthorizationCode": "936403",
    "SoftDescriptor": "Mensagem",
    "VelocityAnalysis": {
      "Id": "c374099e-c474-4916-9f5c-f2598fec2925",
      "ResultMessage": "Accept",
      "Score": 0
    },
    "PaymentId": "c374099e-c474-4916-9f5c-f2598fec2925",
    "Type": "CreditCard",
    "Amount": 10000,
    "ReceivedDate": "2017-05-10 17:32:19",
    "CapturedAmount": 10000,
    "CapturedDate": "2017-05-10 17:32:19",
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Simulado",
    "ExtraDataCollection": [
      {
        "Name": "NomeDoCampo",
        "Value": "ValorDoCampo"
      }
    ],
    "ReasonCode": 0,
    "ReasonMessage": "Successful",
    "Status": 2,
    "ProviderReturnCode": "6",
    "ProviderReturnMessage": "Operation Successful",
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/c374099e-c474-4916-9f5c-f2598fec2925"
      },
      {
        "Method": "PUT",
        "Rel": "void",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/c374099e-c474-4916-9f5c-f2598fec2925/void"
      }
    ]
  }
}
```

|Propriedade|Descrição|Tipo|Tamanho|Formato|
|-----------|---------|----|-------|-------|
|`AcquirerTransactionId`|Id da transação no provedor de meio de pagamento|Texto|40|Texto alfanumérico|
|`ProofOfSale`|Número do Comprovante de Venda|Texto|20|Texto alfanumérico|
|`AuthorizationCode`|Código de autorização|Texto|300|Texto alfanumérico|
|`PaymentId`|Campo Identificador do Pedido|Guid|36|xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx|
|`ReceivedDate`|Data em que a transação foi recebida pela Brapag|Texto|19|AAAA-MM-DD HH:mm:SS|
|`CapturedDate`|Data em que a transação foi capturada a transação|Texto|19|AAAA-MM-DD HH:mm:SS|
|`CapturedAmount`|Valor capturado (sem pontuação)|Número|15|100 equivale a R$ 1,00|
|`ECI`|Eletronic Commerce Indicator. Representa o resultado da autenticação|Texto|2|Exemplos: 5|
|`ReasonCode`|Código de retorno da Adquirência|Texto|32|Texto alfanumérico|
|`ReasonMessage`|Mensagem de retorno da Adquirência|Texto|512|Texto alfanumérico|
|`Status`|Status da Transação|Byte|2|1|
|`ProviderReturnCode`|Código retornado pelo provedor do meio de pagamento (adquirente e bancos)|Texto|32|57|
|`ProviderReturnMessage`|Mensagem retornada pelo provedor do meio de pagamento (adquirente e bancos)|Texto|512|Transação Aprovada|

## Criando uma transação com Autenticação

Quando uma transação é submetida ao processo de autenticação, o portador será redirecionado ao ambiente do emissor, onde deverá realizar a confirmação de seus dados. Quando validado corretamente, o "liability" da transação passa a ser do banco, ou seja, em casos de contestação, o banco será o responsável. 

<aside class="notice"><strong>Autenticação:</strong>Nesta modalidade o portador do cartão é direcionado para o ambiente de autenticação do banco emissor do cartão onde será solicitada a inclusão da senha do cartão. Aplicável somente para Cielo.</aside>

### Requisição

<aside class="request"><span class="method post">POST</span> <span class="endpoint">/v2/sales/</span></aside>

```json
{  
   "MerchantOrderId":"2017051101",
   "Customer":{  
      "Name":"Nome do Comprador"
   },
   "Payment":{  
      "Provider":"Cielo",
	  "Type":"CreditCard",
      "Amount":10000,      
      "Installments":1,
      "Authenticate":true,
      "ReturnUrl":"http://www.braspag.com.br",
      "CreditCard":{  
         "CardNumber":"4551870000000181",
         "Holder":"Nome do Portador",
         "ExpirationDate":"12/2015",
         "SecurityCode":"123",
         "Brand":"Visa"
      }
   }
}
```

```shell
curl
--request POST "https://apisandbox.braspag.com.br/v2/sales/"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{  
   "MerchantOrderId":"2017051101",
   "Customer":{  
      "Name":"Nome do Comprador"
   },
   "Payment":{  
      "Provider":"Cielo",
	  "Type":"CreditCard",
      "Amount":10000,      
      "Installments":1,
      "Authenticate":true,
      "ReturnUrl":"http://www.braspag.com.br",
      "CreditCard":{  
         "CardNumber":"4551870000000181",
         "Holder":"Nome do Portador",
         "ExpirationDate":"12/2015",
         "SecurityCode":"123",
         "Brand":"Visa"
      }
   }
}
--verbose
```

|Propriedade|Tipo|Tamanho|Obrigatório|Descrição|
|-----------|----|-------|-----------|---------|
|`MerchantId`|Guid|36|Sim|Identificador da loja na Braspag|
|`MerchantKey`|Texto|40|Sim|Chave Publica para Autenticação Dupla na Braspag|
|`RequestId`|Guid|36|Não|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT|
|`MerchantOrderId`|Texto|50|Sim|Numero de identificação do Pedido|
|`Customer.Name`|Texto|255|Sim|Nome do comprador|
|`Payment.Provider`|Texto|15|Sim|Nome da provedora de Meio de Pagamento|
|`Payment.Type`|Texto|100|Sim|Tipo do Meio de Pagamento|
|`Payment.Amount`|Número|15|Sim|Valor do Pedido (ser enviado em centavos)|
|`Payment.Installments`|Número|2|Sim|Número de Parcelas|
|`Payment.Authenticate`|Booleano|---|Não (Default false)|Define se o comprador será direcionado ao Banco emissor para autenticação do cartão|
|`Payment.ReturnUrl`|Texto|1024|Sim (quando Autenticate é true)|URL para onde o usuário será redirecionado após o fim da autenticação|
|`CreditCard.CardNumber`|Texto|16|Sim|Número do Cartão do comprador|
|`CreditCard.Holder`|Texto|25|Sim|Nome do Comprador impresso no cartão|
|`CreditCard.ExpirationDate`|Texto|7|Sim|Data de validade impresso no cartão, no formato MM/AAAA|
|`CreditCard.SecurityCode`|Texto|4|Sim|Código de segurança impresso no verso do cartão|
|`CreditCard.Brand`|Texto|10|Sim |Bandeira do cartão|

### Resposta

```json
{
  "MerchantOrderId": "2017051101",
  "Customer": {
    "Name": "Nome do Comprador"
  },
  "Payment": {
    "ServiceTaxAmount": 0,
    "Installments": 1,
    "Interest": "ByMerchant",
    "Capture": false,
    "Authenticate": true,
    "Recurrent": false,
    "CreditCard": {
      "CardNumber": "455187******0181",
      "Holder": "Nome do Portador",
      "ExpirationDate": "12/2015",
      "SaveCard": false,
      "Brand": "Visa"
    },
    "AuthenticationUrl": "https://qasecommerce.cielo.com.br/web/index.cbmp?id=9e61c78c0b0ca3e5db41fa7e31585eab",
    "AcquirerTransactionId": "10069930690009D2A47A",
    "ReturnUrl": "http://www.braspag.com.br",
    "PaymentId": "b125109f-681b-4338-8450-f3e38bc71b32",
    "Type": "CreditCard",
    "Amount": 10000,
    "ReceivedDate": "2017-05-11 11:09:49",
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Cielo",
    "ReasonCode": 9,
    "ReasonMessage": "Waiting",
    "Status": 0,
    "ProviderReturnCode": "0",
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/b125109f-681b-4338-8450-f3e38bc71b32"
      }
    ]
  }
}
```

```shell
--header "Content-Type: application/json"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
  "MerchantOrderId": "2017051101",
  "Customer": {
    "Name": "Nome do Comprador"
  },
  "Payment": {
    "ServiceTaxAmount": 0,
    "Installments": 1,
    "Interest": "ByMerchant",
    "Capture": false,
    "Authenticate": true,
    "Recurrent": false,
    "CreditCard": {
      "CardNumber": "455187******0181",
      "Holder": "Nome do Portador",
      "ExpirationDate": "12/2015",
      "SaveCard": false,
      "Brand": "Visa"
    },
    "AuthenticationUrl": "https://qasecommerce.cielo.com.br/web/index.cbmp?id=9e61c78c0b0ca3e5db41fa7e31585eab",
    "AcquirerTransactionId": "10069930690009D2A47A",
    "ReturnUrl": "http://www.braspag.com.br",
    "PaymentId": "b125109f-681b-4338-8450-f3e38bc71b32",
    "Type": "CreditCard",
    "Amount": 10000,
    "ReceivedDate": "2017-05-11 11:09:49",
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Cielo",
    "ReasonCode": 9,
    "ReasonMessage": "Waiting",
    "Status": 0,
    "ProviderReturnCode": "0",
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/b125109f-681b-4338-8450-f3e38bc71b32"
      }
    ]
  }
}
```

|Propriedade|Descrição|Tipo|Tamanho|Formato|
|-----------|---------|----|-------|-------|
|`AcquirerTransactionId`|Id da transação no provedor de meio de pagamento|Texto|40|Texto alfanumérico|
|`ProofOfSale`|Número do Comprovante de Venda|Texto|20|Texto alfanumérico|
|`AuthorizationCode`|Código de autorização|Texto|300|Texto alfanumérico|
|`PaymentId`|Campo Identificador do Pedido|Guid|36|xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx|
|`ReceivedDate`|Data em que a transação foi recebida pela Brapag|Texto|19|AAAA-MM-DD HH:mm:SS|
|`ReasonCode`|Código de retorno da Adquirência|Texto|32|Texto alfanumérico|
|`ReasonMessage`|Mensagem de retorno da Adquirência|Texto|512|Texto alfanumérico|
|`Status`|Status da Transação|Byte|2|1|
|`ProviderReturnCode`|Código retornado pelo provedor do meio de pagamento (adquirente e bancos)|Texto|32|57|
|`ProviderReturnMessage`|Mensagem retornada pelo provedor do meio de pagamento (adquirente e bancos)|Texto|512|Transação Aprovada|
|`AuthenticationUrl`|URL para qual o Lojista deve redirecionar o Cliente para o fluxo de autenticação|Texto|256|https://qasecommerce.cielo.com.br/web/index.cbmp?id=5f177203bf524c78982ad28f7ece5f08|

## Criando uma transação com Análise de Fraude

Para que a análise de fraude seja efetuada em tempo de transação, é necessário complementar a mensagem com os dados mencionados no nó "FraudAnalysis". 

### Requisição

<aside class="request"><span class="method post">POST</span> <span class="endpoint">/v2/sales/</span></aside>

```json
{  
   "MerchantOrderId":"2017051102",
   "Customer":{  
      "Name":"Nome do Comprador",
      "Identity":"12345678909",
      "IdentityType":"CPF",
      "Email":"comprador@braspag.com.br",
      "Birthdate":"1991-01-02",
      "Address":{  
         "Street":"Alameda Xingu",
         "Number":"512",
         "Complement":"27 andar",
         "ZipCode":"12345987",
         "City":"São Paulo",
         "State":"SP",
         "Country":"BRA",
		 "District":"Alphaville"
      },
	  "DeliveryAddress": {
         "Street":"Alameda Xingu",
         "Number":"512",
         "Complement":"27 andar",
         "ZipCode":"12345987",
         "City":"São Paulo",
         "State":"SP",
         "Country":"BRA",
		 "District":"Alphaville"
	    }
   },
   "Payment":{  
     "Provider":"Simulado",
     "Type":"CreditCard",
     "Amount":10000,
     "ServiceTaxAmount":0,
     "Currency":"BRL",
     "Country":"BRA",
     "Installments":1,
     "Interest":"ByMerchant",
     "Capture":true,
     "Authenticate":false,    
     "Recurrent": false,
     "SoftDescriptor":"Mensagem",
     "CreditCard":{  
         "CardNumber":"4551870000000181",
         "Holder":"Nome do Portador",
         "ExpirationDate":"12/2021",
         "SecurityCode":"123",
         "Brand":"Visa",
         "SaveCard":"false"
     },
     "FraudAnalysis":{
       "Sequence":"AnalyseFirst",
       "SequenceCriteria":"Always",
       "CaptureOnLowRisk":false,
       "VoidOnHighRisk":false,       
       "FingerPrintId":"074c1ee676ed4998ab66491013c565e2",    
	   "Browser":{
		 "CookiesAccepted":false,
		 "Email":"comprador@braspag.com.br",
		 "HostName":"Teste",
		 "IpAddress":"127.0.0.1",
		 "Type":"Chrome"
		},
       "Cart":{
         "IsGift":false,
         "ReturnsAccepted":true,
         "Items":[{
				"GiftCategory":"Undefined",
				"HostHedge":"Off",
				"NonSensicalHedge":"Off",
				"ObscenitiesHedge":"Off",
				"PhoneHedge":"Off",
				"Name":"ItemTeste",
				"Quantity":1,
				"Sku":"20170511",
				"UnitPrice":10000,
				"Risk":"High",
				"TimeHedge":"Normal",
				"Type":"AdultContent",
				"VelocityHedge":"High",
				"Passenger":{
				"Email":"comprador@braspag.com.br",
				"Identity":"1234567890",
				"Name":"Nome do Comprador",
				"Rating":"Adult",
				"Phone":"999994444",
				"Status":"Accepted"
			}
           }]
       },
	    "MerchantDefinedFields":[{
			"Id":95,
			"Value":"Dado Definido pela Loja"
		}],
		"Shipping":{
			"Addressee":"Alameda Xingu, 512",
			"Method":"LowCost",
			"Phone":"1121840540"
		},
		"Travel":{
			"DepartureTime":"2020-01-01",
			"JourneyType":"Ida",
			"Route":"MAO-RJO",
		"Legs":[{
			"Destination":"GYN",
			"Origin":"VCP"
          }]
		}
     }
  }
}
```

```shell
curl
--request POST "https://apisandbox.braspag.com.br/v2/sales/"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{  
   "MerchantOrderId":"2017051102",
   "Customer":{  
      "Name":"Nome do Comprador",
      "Identity":"12345678909",
      "IdentityType":"CPF",
      "Email":"comprador@braspag.com.br",
      "Birthdate":"1991-01-02",
      "Address":{  
         "Street":"Alameda Xingu",
         "Number":"512",
         "Complement":"27 andar",
         "ZipCode":"12345987",
         "City":"São Paulo",
         "State":"SP",
         "Country":"BRA",
		 "District":"Alphaville"
      },
	  "DeliveryAddress": {
         "Street":"Alameda Xingu",
         "Number":"512",
         "Complement":"27 andar",
         "ZipCode":"12345987",
         "City":"São Paulo",
         "State":"SP",
         "Country":"BRA",
		 "District":"Alphaville"
	    }
   },
   "Payment":{  
     "Provider":"Simulado",
     "Type":"CreditCard",
     "Amount":10000,
     "ServiceTaxAmount":0,
     "Currency":"BRL",
     "Country":"BRA",
     "Installments":1,
     "Interest":"ByMerchant",
     "Capture":true,
     "Authenticate":false,    
     "Recurrent": false,
     "SoftDescriptor":"Mensagem",
     "CreditCard":{  
         "CardNumber":"4551870000000181",
         "Holder":"Nome do Portador",
         "ExpirationDate":"12/2021",
         "SecurityCode":"123",
         "Brand":"Visa",
         "SaveCard":"false"
     },
     "FraudAnalysis":{
       "Sequence":"AnalyseFirst",
       "SequenceCriteria":"Always",
       "CaptureOnLowRisk":false,
       "VoidOnHighRisk":false,       
       "FingerPrintId":"074c1ee676ed4998ab66491013c565e2",    
	   "Browser":{
		 "CookiesAccepted":false,
		 "Email":"comprador@braspag.com.br",
		 "HostName":"Teste",
		 "IpAddress":"127.0.0.1",
		 "Type":"Chrome"
		},
       "Cart":{
         "IsGift":false,
         "ReturnsAccepted":true,
         "Items":[{
				"GiftCategory":"Undefined",
				"HostHedge":"Off",
				"NonSensicalHedge":"Off",
				"ObscenitiesHedge":"Off",
				"PhoneHedge":"Off",
				"Name":"ItemTeste",
				"Quantity":1,
				"Sku":"20170511",
				"UnitPrice":10000,
				"Risk":"High",
				"TimeHedge":"Normal",
				"Type":"AdultContent",
				"VelocityHedge":"High",
				"Passenger":{
				"Email":"comprador@braspag.com.br",
				"Identity":"1234567890",
				"Name":"Nome do Comprador",
				"Rating":"Adult",
				"Phone":"999994444",
				"Status":"Accepted"
			}
           }]
       },
	    "MerchantDefinedFields":[{
			"Id":95,
			"Value":"Dado Definido pela Loja"
		}],
		"Shipping":{
			"Addressee":"Alameda Xingu, 512",
			"Method":"LowCost",
			"Phone":"1121840540"
		},
		"Travel":{
			"DepartureTime":"2020-01-01",
			"JourneyType":"Ida",
			"Route":"MAO-RJO",
		"Legs":[{
			"Destination":"GYN",
			"Origin":"VCP"
          }]
		}
     }
  }
}
--verbose
```

|Propriedade|Tipo|Tamanho|Obrigatório|Descrição|
|-----------|----|-------|-----------|---------|
|`MerchantId`|Guid|36|Sim|Identificador da loja na Braspag|
|`MerchantKey`|Texto|40|Sim|Chave Publica para Autenticação Dupla na Braspag|
|`RequestId`|Guid|36|Não|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT|
|`MerchantOrderId`|Texto|50|Sim|Numero de identificação do Pedido|
|`Customer.Name`|Texto|255|Sim|Nome do comprador|
|`Customer.Identity`|Texto |14 |Não|Número do RG, CPF ou CNPJ do Cliente| 
|`Customer.IdentityType`|Texto|255|Não|Tipo de documento de identificação do comprador (CPF ou CNPJ)|
|`Customer.Email`|Texto|255|Não|Email do comprador|
|`Customer.Birthdate`|Date|10|Não|Data de nascimento do Comprador no formato AAAA-MM-DD|
|`Customer.Address.Street`|Texto|255|Não|Endereço de contato do comprador|
|`Customer.Address.Number`|Texto|15|Não|Número endereço de contato do comprador|
|`Customer.Address.Complement`|Texto|50|Não|Complemento do endereço de contato do Comprador.br|
|`Customer.Address.ZipCode`|Texto|9|Não|CEP do endereço de contato do comprador|
|`Customer.Address.City`|Texto|50|Não|Cidade do endereço de contato do comprador|
|`Customer.Address.State`|Texto|2|Não|Estado do endereço de contato do comprador|
|`Customer.Address.Country`|Texto|35|Não|Pais do endereço de contato do comprador|
|`Customer.Address.District`|Texto |50 |Não|Bairro do Comprador. |
|`Customer.DeliveryAddress.Street`|Texto|255|Não|Endereço do comprador|
|`Customer.DeliveryAddress.Number`|Texto|15|Não|Número do endereço de entrega do pedido|
|`Customer.DeliveryAddress.Complement`|Texto|50|Não|Complemento do endereço de entrega do pedido|
|`Customer.DeliveryAddress.ZipCode`|Texto|9|Não|CEP do endereço de entrega do pedido|
|`Customer.DeliveryAddress.City`|Texto|50|Não|Cidade do endereço de entrega do pedido|
|`Customer.DeliveryAddress.State`|Texto|2|Não|Estado do endereço de entrega do pedido|
|`Customer.DeliveryAddress.Country`|Texto|35|Não|Pais do endereço de entrega do pedido|
|`Customer.DeliveryAddress.District`|Texto |50 |Não|Bairro do Comprador. |
|`Payment.Provider`|Texto|15|Sim|Nome da provedora de Meio de Pagamento|
|`Payment.Type`|Texto|100|Sim|Tipo do Meio de Pagamento|
|`Payment.Amount`|Número|15|Sim|Valor do Pedido (ser enviado em centavos)|
|`Payment.ServiceTaxAmount`|Número|15|Sim|Montante do valor da autorização que deve ser destinado à taxa de serviço. Obs.: Esse valor não é adicionado ao valor da autorização|
|`Payment.Currency`|Texto|3|Não|Moeda na qual o pagamento será feito (BRL / USD / MXN / COP / CLP / ARS / PEN / EUR / PYN / UYU / VEB / VEF / GBP)|
|`Payment.Country`|Texto|3|Não|País na qual o pagamento será feito|
|`Payment.Installments`|Número|2|Sim|Número de Parcelas|
|`Payment.Interest`|Texto|10|Não|Tipo de parcelamento - Loja (ByMerchant) ou Cartão (ByIssuer)|
|`Payment.Capture`|Booleano|---|Não (Default false)|Booleano que indica se a autorização deve ser com captura automática (true) ou não (false). Deverá verificar junto à adquirente a disponibilidade desta funcionalidade|
|`Payment.Authenticate`|Booleano|---|Não (Default false)|Booleano que indica se a transação deve ser autenticada (true) ou não (false). Deverá verificar junto à adquirente a disponibilidade desta funcionalidade|
|`Payment.Recurrent`|Booleano|---|Não (Default false)|Booleano que indica se a transação é do tipo recorrente (true) ou não (false). Este com valor true não originará uma nova Recorrência, apenas permitirá a realização de uma transação sem a necessidade de envio do CVV. Somente para transações Cielo. Authenticate deve ser false quando Recurrent é true|
|`Payment.SoftDescriptor`|Texto|13|Não|Texto que será impresso na fatura do portador|
|`Payment.ExtraDataCollection.Name`|Texto|50|Não|Nome do campo que será gravado o Dado Extra|
|`Payment.ExtraDataCollection.Value`|Texto|1024|Não|Valor do campo que será gravado o Dado Extra|
|`CreditCard.CardNumber`|Texto|16|Sim|Número do Cartão do comprador|
|`CreditCard.Holder`|Texto|25|Sim|Nome do portador impresso no cartão|
|`CreditCard.ExpirationDate`|Texto|7|Sim|Data de validade impresso no cartão|
|`CreditCard.SecurityCode`|Texto|4|Sim|Código de segurança impresso no verso do cartão|
|`CreditCard.Brand`|Texto|10|Sim |Bandeira do cartão|
|`CreditCard.SaveCard`|Booleano|---|Não (Default false)|Booleano que identifica se o cartão será salvo para gerar o token (CardToken)|
|`FraudAnalysis.Sequence`|Texto|14|Sim|Tipo de Fluxo para realização da análise de fraude. Primeiro Analise (AnalyseFirst) ou Primeiro Autorização (AuthorizeFirst)|
|`FraudAnalysis.SequenceCriteria`|Texto|9|Sim|Critério do fluxo.<BR><UL><LI>OnSuccess - Só realiza a analise se tiver sucesso na transação</LI><LI>Always - Sempre realiza a analise</LI></UL>|
|`FraudAnalysis.CaptureOnLowRisk`|Booleano|---|Não|Quando true, a autorização deve ser com captura automática quando o risco de fraude for considerado baixo (Accept). Em casos de Reject ou Review, o fluxo permanece o mesmo, ou seja, a captura acontecerá conforme o valor especificado no parâmetro "Capture". Para a utilização deste parâmetro, a sequência do fluxo de análise de risco deve ser obrigatoriamente "AuthorizeFirst". Por depender do resutlado de análise de risco, este parâmetro só terá efeito quando o serviço de Antifraude for contratado	|
|`FraudAnalysis.VoidOnHighRisk`|Booleano|---|Não|Quando true, o estorno deve acontecer automaticamente quando o risco de fraude for considerado alto (Reject). Em casos de Accept ou Review, o fluxo permanece o mesmo, ou seja, o estorno deve ser feito manualmente. Para a utilização deste parâmetro, a sequência do fluxo de análise de risco deve ser obrigatoriamente "AuthorizeFirst". Por depender do resutlado de análise de risco, este parâmetro só terá efeito quando o serviço de Antifraude for contratado. |
|`FraudAnalysis.FingerPrintId`|Texto|50|Sim|Identificador utilizado para cruzar informações obtidas pelo Browser do internauta com os dados enviados para análise. Este mesmo valor deve ser passado na variável SESSIONID do script do DeviceFingerPrint|
|`FraudAnalysis.Browser.CookiesAccepted`|Booleano|---|Sim|Booleano para identificar se o browser do cliente aceita cookies|
|`FraudAnalysis.Browser.Email`|Texto|100|Não|E-mail registrado no browser do comprador|
|`FraudAnalysis.Browser.HostName`|Texto|60|Não|Nome do host onde o comprador estava antes de entrar no site da loja|
|`FraudAnalysis.Browser.IpAddress`|Texto|15|Sim|Endereço IP do comprador. É altamente recomendável o envio deste campo|
|`FraudAnalysis.Browser.Type`|Texto|40|Não|Nome do browser utilizado pelo comprador|
|`FraudAnalysis.Cart.IsGift`|Booleano|---|Não|Booleano que indica se o pedido é para presente ou não|
|`FraudAnalysis.Cart.ReturnsAccepted`|Booleano|---|Não|Booleano que define se devoluções são aceitas para o pedido|
|`FraudAnalysis.Items.GiftCategory`|Texto|9|Não|Campo que avaliará os endereços de cobrança e entrega para difrentes cidades, estados ou países<BR><UL><LI>Yes (Em caso de divergência entre endereços de cobrança e entrega, marca com risco pequeno)</LI><LI>No (Em caso de divergência entre endereços de cobrança e entrega, marca com risco alto)</LI><LI>Off (Ignora a análise de risco para endereços divergentes)</LI></UL>|
|`FraudAnalysis.Items.HostHedge`|Texto||Não|Nível de importância do e-mail e endereços IP dos clientes em risco de pontuação. <BR><UL><LI>Low (Baixa importância do e-mail e endereço IP na análise de risco)</LI><LI>Normal (Média importância do e-mail e endereço IP na análise de risco)</LI><LI>High (Alta importância do e-mail e endereço IP na análise de risco)</LI><LI>Off (E-mail e endereço IP não afetam a análise de risco)</LI></UL>|
|`FraudAnalysis.Items.NonSensicalHedge`|Texto|6|Não|Nível dos testes realizados sobre os dados do comprador com pedidos recebidos sem sentido. <BR><UL><LI>Low (Baixa importância da verificação feita sobre o pedido do comprador, na análise de risco)</LI><LI>Normal (Média importância da verificação feita sobre o pedido do comprador, na análise de risco)</LI><LI>High (Alta importância da verificação feita sobre o pedido do comprador, na análise de risco)</LI><LI>Off (Verificação do pedido do comprador não afeta a análise de risco)</LI></UL>|
|`FraudAnalysis.Items.ObscenitiesHedge`|Texto|6|Não|Nível de obscenidade dos pedidos recebedidos. <BR><UL><LI>Low (Baixa importância da verificação sobre obscenidades do pedido do comprador, na análise de risc0)</LI><LI>Normal (Média importância da verificação sobre obscenidades do pedido do comprador, na análise de risco)</LI><LI>High (Alta importância da verificação sobre obscenidades do pedido do comprador, na análise de risco)</LI><LI>Off (Verificação de obscenidade no pedido do comprador não afeta a análise de risco)</LI></UL>|
|`FraudAnalysis.Items.PhoneHedge`|Texto|6|Não|Nível dos testes realizados com os números de telefones. <BR><UL><LI>Low (Baixa importância nos testes realizados com números de telefone)</LI><LI>Normal (Média importância nos testes realizados com números de telefone)</LI><LI>High (Alta importância nos testes realizados com números de telefone)</LI><LI>Off (Testes de números de telefone não afetam a análise de risco)</LI></UL>|
|`FraudAnalysis.Items.Name`|Texto|255|Sim|Nome do Produto|
|`FraudAnalysis.Items.Quantity`|Número|15|Sim|Quantidade do produto a ser adquirido|
|`FraudAnalysis.Items.Sku`|Texto|255|Sim|Código comerciante identificador do produto|
|`FraudAnalysis.Items.UnitPrice`|Número|15|Sim|Preço unitário do produto|
|`FraudAnalysis.Items.Risk`|Texto|6|Não|Nível do risco do produto. <BR><UL><LI>Low (O produto tem um histórico de poucos chargebacks)</LI><LI>Normal (O produto tem um histórico de chargebacks considerado normal)</LI><LI>High (O produto tem um histórico de chargebacks acima da média)</LI></UL>|
|`FraudAnalysis.Items.TimeHedge`|Texto||Não|Nível de importância da hora do dia do pedido do cliente. <BR><UL><LI>Low (Baixa importância no horário do dia em que foi feita a compra, para a análise de risco)</LI><LI>Normal (Média importância no horário do dia em que foi feita a compra, para a análise de risco)</LI><LI>High (Alta importância no horário do dia em que foi feita a compra, para a análise de risco)</LI><LI>Off (O horário da compra não afeta a análise de risco)</LI></UL>|
|`FraudAnalysis.Items.Type`|Texto||Não|Tipo do produto. <BR><UL><LI>AdultContent(Conteúdo adulto)</LI><LI>Coupon(Cupon de desconto)</LI><LI>Default(Opção padrão para análise na CyberSource quando nenhum outro valor é selecionado)</LI><LI>EletronicGood(Produto eletrônico)</LI><LI>EletronicSoftware(Softwares distribuídos eletronicamente via download)</LI><LI>GiftCertificate(Vale presente)</LI><LI>HandlingOnly(Taxa de instalação ou manuseio)</LI><LI>Service(Serviço)</LI><LI>ShippingAndHandling(Frete e taxa de instalação ou manuseio)</LI><LI>ShippingOnly(Frete)</LI><LI>Subscription(Assinatura)</LI></UL>|
|`FraudAnalysis.Items.VelocityHedge`|Texto|6|Não|Nível de importância de frequência de compra do cliente. <BR><UL><LI>Low (Baixa importância no número de compras realizadas pelo cliente nos últimos 15 minutos)</LI><LI>Normal (Média importância no número de compras realizadas pelo cliente nos últimos 15 minutos)</LI><LI>High (Alta importância no número de compras realizadas pelo cliente nos últimos 15 minutos)</LI><LI>Off (A frequência de compras realizadas pelo cliente não afeta a análise de fraude)</LI></UL>|
|`FraudAnalysis.Items.Passenger.Email`|Texto|255|Não|Email do Passageiro|
|`FraudAnalysis.Items.Passenger.Identity`|Texto|32|Não|Id do passageiro a quem o bilheite foi emitido|
|`FraudAnalysis.Items.Passenger.Name`|Texto|120|Não|Nome do passageiro|
|`FraudAnalysis.Items.Passenger.Rating`|Texto||Não|Classificação do Passageiro. <BR><UL><LI>Adult (Passageiro adulto)</LI><LI>Child(Passageiro criança)</LI><LI>Infant(Passageiro infantil)</LI><LI>Youth(Passageiro adolescente)</LI><LI>Student(Passageiro estudante)</LI><LI>SeniorCitizen(Passageiro idoso)</LI><LI>Military(Passageiro militar)</LI></UL>|
|`FraudAnalysis.Items.Passenger.Phone`|Texto|15|Não|Número do telefone do passageiro. Para pedidos fora do U.S., a CyberSource recomenda que inclua o código do país. 552133665599 (Ex. Código do Pais 55, Código da Cidade 21, Telefonme 33665599)|
|`FraudAnalysis.Items.Passenger.Status`|Texto|32|Não|Classificação da empresa aérea. Pode-se usar valores como Gold ou Platina|
|`FraudAnalysis.MerchantDefinedFields.Id`|Texto|---|Sim (se aplicável)|Id das informações adicionais a serem enviadas|
|`FraudAnalysis.MerchantDefinedFields.Value`|Texto|255|Sim (se aplicável)|Valor das informações adicionais a serem enviadas|
|`FraudAnalysis.Shipping.Addressee`|Texto|255|Não|Nome do destinatário da entrega|
|`FraudAnalysis.Shipping.Method`|Texto||Não|Tipo de serviço de entrega do produto. <BR><UL><LI>SameDay(Serviço de entrega no mesmo dia)</LI><LI>OneDay(Serviço de entrega noturna ou no dia seguint)</LI><LI>TwoDay(Serviço de entrega em dois dias)</LI><LI>ThreeDay(Serviço de entrega em três dias)</LI><LI>LowCost(Serviço de entrega de baixo custo)</LI><LI>Pickup(Produto retirado na loja)</LI><LI>Other(Outro método de entrega)</LI><LI>None(Sem serviço de entrega, pois é um serviço ou assinatura)</LI></UL>|
|`FraudAnalysis.Shipping.Phone`|Texto|15|Não|Telefone do destinatário da entrega. Ex. 552133665599 (Código do Pais 55, Código da Cidade 21, Telefonme 33665599)|
|`FraudAnalysis.Travel.DepartureTime`|DateTime|23|Não|Data, hora e minuto de partida do vôo. AAAA-MM-DD HH:mm:SS|
|`FraudAnalysis.Travel.JourneyType`|Texto|32|Não|Tipo de viagem. Ex. Só Ida, Só Volta, Ida e Volta |
|`FraudAnalysis.Travel.Route`|Texto|255|Não|Rota da viagem. Concatenação de pernas de viagem individuais no formato ORIG1-DEST1|
|`FraudAnalysis.Travel.Legs.Destination`|Texto|3|Não|Código do aeroporto do ponto de destino da viagem|
|`FraudAnalysis.Travel.Legs.Origin`|Texto|3|Não |Código do aeroporto do ponto de origem da viagem|

### Resposta

```json
{
  "MerchantOrderId": "2017051102",
  "Customer": {
    "Name": "Nome do Comprador",
    "Identity": "12345678909",
    "IdentityType": "CPF",
    "Email": "comprador@braspag.com.br",
    "Birthdate": "1991-01-02",
    "Address": {
      "Street": "Alameda Xingu",
      "Number": "512",
      "Complement": "27 andar",
      "ZipCode": "12345987",
      "City": "São Paulo",
      "State": "SP",
      "Country": "BRA",
	  "District":"Alphaville"
    },
    "DeliveryAddress": {
      "Street": "Alameda Xingu",
      "Number": "512",
      "Complement": "27 andar",
      "ZipCode": "12345987",
      "City": "São Paulo",
      "State": "SP",
      "Country": "BRA",
	  "District":"Alphaville"
    }
  },
  "Payment": {
    "ServiceTaxAmount": 0,
    "Installments": 1,
    "Interest": "ByMerchant",
    "Capture": true,
    "Authenticate": false,
    "Recurrent": false,
    "CreditCard": {
      "CardNumber": "455187******0181",
      "Holder": "Nome do Portador",
      "ExpirationDate": "12/2021",
      "SaveCard": false,
      "Brand": "Undefined"
    },
    "SoftDescriptor": "Mensagem",
	"FraudAnalysis": {
            "Sequence": "AnalyseFirst",
            "SequenceCriteria": "Always",
            "FingerPrintId": "074c1ee676ed4998ab66491013c565e2",
            "MerchantDefinedFields": [
                {
                    "Id": 95,
                    "Value": "Eu defini isso"
                }
            ],
            "Cart": {
                "IsGift": false,
                "ReturnsAccepted": true,
                "Items": [
                    {
                        "Type": "AdultContent",
                        "Name": "ItemTeste",
                        "Risk": "High",
                        "Sku": "201411170235134521346",
                        "UnitPrice": 123,
                        "Quantity": 1,
                        "HostHedge": "Off",
                        "NonSensicalHedge": "Off",
                        "ObscenitiesHedge": "Off",
                        "PhoneHedge": "Off",
                        "TimeHedge": "Normal",
                        "VelocityHedge": "High",
                        "GiftCategory": "Undefined",
                        "Passenger": {
                            "Name": "Comprador accept",
                            "Identity": "1234567890",
                            "Status": "Accepted",
                            "Rating": "Adult",
                            "Email": "compradorteste@live.com",
                            "Phone": "999994444"
                        }
                    }
                ]
            },
            "Travel": {
                "Route": "MAO-RJO",
                "DepartureTime": "2010-01-02T00:00:00",
                "JourneyType": "Ida",
                "Legs": [
                    {
                        "Destination": "GYN",
                        "Origin": "VCP"
                    }
                ]
            },
            "Browser": {
                "HostName": "Teste",
                "CookiesAccepted": false,
                "Email": "compradorteste@live.com",
                "Type": "Chrome",
                "IpAddress": "200.190.150.350"
            },
            "Shipping": {
                "Addressee": "Sr Comprador Teste",
                "Phone": "21114740",
                "Method": "LowCost"
            },
            "Id": "0e4d0a3c-e424-4fa5-a573-4eabbd44da42",
            "Status": 1,
            "CaptureOnLowRisk": false,
            "VoidOnHighRisk": false,
            "FraudAnalysisReasonCode": 100,
            "ReplyData": {
                "AddressInfoCode": "COR-BA^MM-BIN",
                "FactorCode": "B^D^R^Z",
                "Score": 42,
                "BinCountry": "us",
                "CardIssuer": "FIA CARD SERVICES, N.A.",
                "CardScheme": "VisaCredit",
                "HostSeverity": 1,
                "InternetInfoCode": "FREE-EM^RISK-EM",
                "IpRoutingMethod": "Undefined",
                "ScoreModelUsed": "default_lac",
                "CasePriority": 3
            }
        },
    "PaymentId": "4219584b-6d23-49f0-a24c-2b677bc60df8",
    "Type": "CreditCard",
    "Amount": 10000,
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Simulado",
    "ReasonCode": 16,
    "ReasonMessage": "AbortedByFraud",
    "Status": 13,
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/4219584b-6d23-49f0-a24c-2b677bc60df8"
      }
    ]
  }
}
```

```shell
--header "Content-Type: application/json"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
  "MerchantOrderId": "2017051102",
  "Customer": {
    "Name": "Nome do Comprador",
    "Identity": "12345678909",
    "IdentityType": "CPF",
    "Email": "comprador@braspag.com.br",
    "Birthdate": "1991-01-02",
    "Address": {
      "Street": "Alameda Xingu",
      "Number": "512",
      "Complement": "27 andar",
      "ZipCode": "12345987",
      "City": "São Paulo",
      "State": "SP",
      "Country": "BRA",
	  "District":"Alphaville"
    },
    "DeliveryAddress": {
      "Street": "Alameda Xingu",
      "Number": "512",
      "Complement": "27 andar",
      "ZipCode": "12345987",
      "City": "São Paulo",
      "State": "SP",
      "Country": "BRA",
	  "District":"Alphaville"
    }
  },
  "Payment": {
    "ServiceTaxAmount": 0,
    "Installments": 1,
    "Interest": "ByMerchant",
    "Capture": true,
    "Authenticate": false,
    "Recurrent": false,
    "CreditCard": {
      "CardNumber": "455187******0181",
      "Holder": "Nome do Portador",
      "ExpirationDate": "12/2021",
      "SaveCard": false,
      "Brand": "Undefined"
    },
    "SoftDescriptor": "Mensagem",
	"FraudAnalysis": {
            "Sequence": "AnalyseFirst",
            "SequenceCriteria": "Always",
            "FingerPrintId": "074c1ee676ed4998ab66491013c565e2",
            "MerchantDefinedFields": [
                {
                    "Id": 95,
                    "Value": "Eu defini isso"
                }
            ],
            "Cart": {
                "IsGift": false,
                "ReturnsAccepted": true,
                "Items": [
                    {
                        "Type": "AdultContent",
                        "Name": "ItemTeste",
                        "Risk": "High",
                        "Sku": "201411170235134521346",
                        "UnitPrice": 123,
                        "Quantity": 1,
                        "HostHedge": "Off",
                        "NonSensicalHedge": "Off",
                        "ObscenitiesHedge": "Off",
                        "PhoneHedge": "Off",
                        "TimeHedge": "Normal",
                        "VelocityHedge": "High",
                        "GiftCategory": "Undefined",
                        "Passenger": {
                            "Name": "Comprador accept",
                            "Identity": "1234567890",
                            "Status": "Accepted",
                            "Rating": "Adult",
                            "Email": "compradorteste@live.com",
                            "Phone": "999994444"
                        }
                    }
                ]
            },
            "Travel": {
                "Route": "MAO-RJO",
                "DepartureTime": "2010-01-02T00:00:00",
                "JourneyType": "Ida",
                "Legs": [
                    {
                        "Destination": "GYN",
                        "Origin": "VCP"
                    }
                ]
            },
            "Browser": {
                "HostName": "Teste",
                "CookiesAccepted": false,
                "Email": "compradorteste@live.com",
                "Type": "Chrome",
                "IpAddress": "200.190.150.350"
            },
            "Shipping": {
                "Addressee": "Sr Comprador Teste",
                "Phone": "21114740",
                "Method": "LowCost"
            },
            "Id": "0e4d0a3c-e424-4fa5-a573-4eabbd44da42",
            "Status": 1,
            "CaptureOnLowRisk": false,
            "VoidOnHighRisk": false,
            "FraudAnalysisReasonCode": 100,
            "ReplyData": {
                "AddressInfoCode": "COR-BA^MM-BIN",
                "FactorCode": "B^D^R^Z",
                "Score": 42,
                "BinCountry": "us",
                "CardIssuer": "FIA CARD SERVICES, N.A.",
                "CardScheme": "VisaCredit",
                "HostSeverity": 1,
                "InternetInfoCode": "FREE-EM^RISK-EM",
                "IpRoutingMethod": "Undefined",
                "ScoreModelUsed": "default_lac",
                "CasePriority": 3
            }
        },
    "PaymentId": "4219584b-6d23-49f0-a24c-2b677bc60df8",
    "Type": "CreditCard",
    "Amount": 10000,
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Simulado",
    "ReasonCode": 16,
    "ReasonMessage": "AbortedByFraud",
    "Status": 13,
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/4219584b-6d23-49f0-a24c-2b677bc60df8"
      }
    ]
  }
}
```

|Propriedade|Descrição|Tipo|Tamanho|Formato|
|-----------|---------|----|-------|-------|
|`AcquirerTransactionId`|Id da transação no provedor de meio de pagamento|Texto|40|Texto alfanumérico|
|`ProofOfSale`|Número do Comprovante de Venda|Texto|20|Texto alfanumérico|
|`AuthorizationCode`|Código de autorização|Texto|300|Texto alfanumérico|
|`SoftDescriptor`|Texto que será impresso na fatura do portador|Texto|13|Texto alfanumérico|
|`PaymentId`|Campo Identificador do Pedido|Guid|36|xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx|
|`ReceivedDate`|Data em que a transação foi recebida pela Brapag|Texto|19|AAAA-MM-DD HH:mm:SS|
|`ReasonCode`|Código de retorno da Adquirência|Texto|32|Texto alfanumérico|
|`ReasonMessage`|Mensagem de retorno da Adquirência|Texto|512|Texto alfanumérico|
|`Status`|Status da Transação|Byte|2|1|
|`ProviderReturnCode`|Código retornado pelo provedor do meio de pagamento (adquirente e bancos)|Texto|32|57|
|`ProviderReturnMessage`|Mensagem retornada pelo provedor do meio de pagamento (adquirente e bancos)|Texto|512|Transação Aprovada|
|`FraudAnalysis.Id`|Indentificação da Transação no Antifraud|Texto|300|Texto alfanumérico|
|`FraudAnalysis.Status`|Status da Transação|Byte|---|2|
|`FraudAnalysis.FraudAnalysisReasonCode`|Resultado da análise|Byte|---|<ul><li>100 - Operação bem sucedida.</li><li>101 - O pedido está faltando um ou mais campos necessários. Possível ação: Veja os campos que estão faltando na lista AntiFraudResponse.MissingFieldCollection. Reenviar o pedido com a informação completa.</li><li>102 - Um ou mais campos do pedido contêm dados inválidos. Possível ação: Veja os campos inválidos na lista AntiFraudResponse.InvalidFieldCollection. Reenviar o pedido com as informações corretas.</li><li>150 Falha no sistema geral. Possível ação: Aguarde alguns minutos e tente reenviar o pedido.</li><li>151 - O pedido foi recebido, mas ocorreu time-out no servidor. Este erro não inclui time-out entre o cliente e o servidor. Possível ação: Aguarde alguns minutos e tente reenviar o pedido.</li><li>152 O pedido foi recebido, mas ocorreu time-out. Possível ação: Aguarde alguns minutos e reenviar o pedido.</li><li>202 – Prevenção à Fraude recusou o pedido porque o cartão expirou. Você também pode receber este código se a data de validade não coincidir com a data em arquivo do banco emissor. Se o processador de pagamento permite a emissão de créditos para cartões expirados, a CyberSource não limita essa funcionalidade. Possível ação: Solicite um cartão ou outra forma de pagamento.</li><li>231 O número da conta é inválido. Possível ação: Solicite um cartão ou outra forma de pagamento.</li><li>234 - Há um problema com a configuração do comerciante. Possível ação: Não envie o pedido. Entre em contato com o Suporte ao Cliente para corrigir o problema de configuração.</li><li>400 A pontuação de fraude ultrapassa o seu limite. Possível ação: Reveja o pedido do cliente.</li><li>480 O pedido foi marcado para revisão pelo Gerenciador de Decisão.</li><li>481 - O pedido foi rejeitado pelo Gerenciador de Decisão</li></ul>|
|`FraudAnalysis.ReplyData.AddressInfoCode`|Combinação de códigos que indicam erro no endereço de cobrança e/ou entrega. Os códigos são concatenados usando o caractere ^|Texto|255|Ex: COR-BA^MM-BIN<br /><ul><li>COR-BA - O endereço de cobrança pode ser normalizado.</li><li>COR-SA - O endereço de entrega pode ser normalizado.</li><li>INTL-BA - O país de cobrança é fora dos U.S.</li><li>INTL-SA - O país de entrega é fora dos U.S.</li><li>MIL-USA - Este é um endereço militar nos U.S.</li><li>MM-A - Os endereços de cobrança e entrega usam nomes de ruas diferentes.</li><li>MM-BIN - O BIN do cartão (os seis primeiros dígitos do número) não corresponde ao país.</li><li>MM-C - Os endereços de cobrança e entrega usam cidades diferentes.</li><li>MM-CO - Os endereços de cobrança e entrega usam países diferentes.</li><li>MM-ST - Os endereços de cobrança e entrega usam estados diferentes.</li><li>MM-Z - Os endereços de cobrança e entrega usam códidos postais diferentes.</li><li>UNV-ADDR - O endereço é inverificável.</li></ul>|
|`FraudAnalysis.ReplyData.FactorCode`|Combinação de códigos que indicam o score do pedido. Os códigos são concatenados usando o caractere ^|Texto|100|Ex: B^D^R^Z<br /><ul><li>A - Mudança de endereço excessiva. O cliente mudou o endereço de cobrança duas ou mais vezes nos últimos seis meses.</li><li>B - BIN do cartão ou autorização de risco. Os fatores de risco estão relacionados com BIN de cartão de crédito e/ou verificações de autorização do cartão.</li><li>C - Elevado números de cartões de créditos. O cliente tem usado mais de seis números de cartões de créditos nos últimos seis meses.</li><li>D - Impacto do endereço de e-mail. O cliente usa um provedor de e-mail gratuito ou o endereço de email é arriscado.</li><li>E - Lista positiva. O cliente está na sua lista positiva.</li><li>F - Lista negativa. O número da conta, endereço, endereço de e-mail ou endereço IP para este fim aparece sua lista negativa.</li><li>G - Inconsistências de geolocalização. O domínio do cliente de e-mail, número de telefone, endereço de cobrança, endereço de envio ou endereço IP é suspeito.</li><li>H - Excessivas mudanças de nome. O cliente mudou o nome duas ou mais vezes nos últimos seis meses.</li><li>I - Inconsistências de internet. O endereço IP e de domínio de e-mail não são consistentes com o endereço de cobrança.</li><li>N - Entrada sem sentido. O nome do cliente e os campos de endereço contém palavras sem sentido ou idioma.</li><li>O - Obscenidades. Dados do cliente contém palavras obscenas.</li><li>P - Identidade morphing. Vários valores de um elemento de identidade estão ligados a um valor de um elemento de identidade diferentes. Por exemplo, vários números de telefone estão ligados a um número de conta única.</li><li>Q - Inconsistências do telefone. O número de telefone do cliente é suspeito.</li><li>R - Ordem arriscada. A transação, o cliente e o lojista mostram informações correlacionadas de alto risco.</li><li>T - Cobertura Time. O cliente está a tentar uma compra fora do horário esperado.</li><li>U - Endereço não verificável. O endereço de cobrança ou de entrega não pode ser verificado.</li><li>V - Velocity. O número da conta foi usado muitas vezes nos últimos 15 minutos.</li><li>W - Marcado como suspeito. O endereço de cobrança ou de entrega é semelhante a um endereço previamente marcado como suspeito.</li><li>Y - O endereço, cidade, estado ou país dos endereços de cobrança e entrega não se correlacionam.</li><li>Z - Valor inválido. Como a solicitação contém um valor inesperado, um valor padrão foi substituído. Embora a transação ainda possa ser processada, examinar o pedido com cuidado para detectar anomalias.</li></ul>|
|`FraudAnalysis.ReplyData.Score`|Score total calculado para o pedido|Número|---|Número|
|`FraudAnalysis.ReplyData.BinCountry`|Sigla do país de origem da compra|Texto|2|us|
|`FraudAnalysis.ReplyData.CardIssuer`|Nome do banco ou entidade emissora do cartão|Texto|128|Bradesco|
|`FraudAnalysis.ReplyData.CardScheme`|Tipo da bandeira|Texto|20|<ul><li>MaestroInternational - Maestro International</li><li>MaestroUkDomestic - Maestro UK Domestic</li><li>MastercardCredit - MasterCard Credit</li><li>MastercardDebit - MasterCard Debit</li><li>VisaCredit - Visa Credit</li><li>VisaDebit - Visa Debit</li><li>VisaElectron - Visa Electron</li></ul>|
|`FraudAnalysis.ReplyData.HostSeverity`|Nível de risco do domínio de e-mail do comprador, de 0 a 5, onde 0 é risco indeterminado e 5 representa o risco mais alto|Número|---|5|
|`FraudAnalysis.ReplyData.InternetInfoCode`|Sequência de códigos que indicam que existe uma excessiva alteração de identidades do comprador. Os códigos são concatenados usando o caractere ^|Texto|255|Ex: <br /><ul><li>MORPH-B - O mesmo endereço de cobrança tem sido utilizado várias vezes com identidades de clientes múltiplos.</li><li>MORPH-C - O mesmo número de conta tem sido utilizado várias vezes com identidades de clientes múltiplos.</li><li>MORPH-E - O mesmo endereço de e-mail tem sido utilizado várias vezes com identidades de clientes múltiplos. MORPH-I O mesmo endereço IP tem sido utilizado várias vezes com identidades de clientes múltiplos.</li><li>MORPH-P - O mesmo número de telefone tem sido usado várias vezes com identidades de clientes múltiplos.</li><li>MORPH-S - O mesmo endereço de entrega tem sido utilizado várias vezes com identidades de clientes múltiplos.</li></ul>|
|`FraudAnalysis.ReplyData.IpRoutingMethod`|Tipo de roteamento de IP utilizado pelo computador|Texto|---|<ul><li>Anonymizer</li><li>AolBased</li><li>CacheProxy</li><li>Fixed</li><li>InternationalProxy</li><li>MobileGateway</li><li>Pop</li><li>RegionalProxy</li><li>Satellite</li><li>SuperPop</li></ul>|
|`FraudAnalysis.ReplyData.ScoreModelUsed`|Nome do modelo de score utilizado|Texto|20|Ex: default_lac|
|`FraudAnalysis.ReplyData.CasePriority`|Caso o lojista seja assinante do Enhanced Case Management, ele recebe este valor com o nível de prioridade, sendo 1 o mais alto e 5 o mais baixo|Número|---|3|

## Criando uma transação que salva o cartão

Caso você tenha contratado o Cartão Protegido, é possível salvar um cartão no formato de um Token, para substituir os dados do cartão numa próxima transação do mesmo comprador. É importante ressaltar que por questões de segurança, o CVV (Código de Segurança) não é tokenizado.

### Requisição

<aside class="request"><span class="method post">POST</span> <span class="endpoint">/v2/sales/</span></aside>

```json
{
   "MerchantOrderId":"2017051104",
   "Customer":{
      "Name":"Nome do Cliente"
   },
   "Payment":{
     "Provider":"Simulado",
     "Type":"CreditCard",
     "Amount":10000,
     "Installments":1,
     "CreditCard":{
         "CardNumber":"4551870000000181",
         "Holder":"Nome do Portador",
         "ExpirationDate":"12/2021",
         "SecurityCode":"123",
         "Brand":"Visa",
         "SaveCard":true
     }
   }
}
```

```shell
curl
--request POST "https://apisandbox.braspag.com.br/v2/sales/"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
   "MerchantOrderId":"2017051104",
   "Customer":{
      "Name":"Nome do Cliente"
   },
   "Payment":{
     "Provider":"Simulado",
     "Type":"CreditCard",
     "Amount":10000,
     "Installments":1,
     "CreditCard":{
         "CardNumber":"4551870000000181",
         "Holder":"Nome do Portador",
         "ExpirationDate":"12/2021",
         "SecurityCode":"123",
         "Brand":"Visa",
         "SaveCard":true
     }
   }
}
--verbose
```

|Propriedade|Tipo|Tamanho|Obrigatório|Descrição|
|-----------|----|-------|-----------|---------|
|`MerchantId`|Guid|36|Sim|Identificador da loja na Braspag|
|`MerchantKey`|Texto|40|Sim|Chave Publica para Autenticação Dupla na Braspag|
|`RequestId`|Guid|36|Não|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT|
|`MerchantOrderId`|Texto|50|Sim|Numero de identificação do Pedido|
|`Customer.Name`|Texto|255|Sim|Nome do comprador|
|`Payment.Provider`|Texto|15|Sim|Nome da provedora de Meio de Pagamento|
|`Payment.Type`|Texto|100|Sim|Tipo do Meio de Pagamento|
|`Payment.Amount`|Número|15|Sim|Valor do Pedido (ser enviado em centavos)|
|`Payment.Installments`|Número|2|Sim|Número de Parcelas|
|`CreditCard.CardNumber`|Texto|16|Sim|Número do Cartão do comprador|
|`CreditCard.Holder`|Texto|25|Sim|Nome do Comprador impresso no cartão|
|`CreditCard.ExpirationDate`|Texto|7|Sim|Data de validade impresso no cartão, no formato MM/AAAA|
|`CreditCard.SecurityCode`|Texto|4|Sim|Código de segurança impresso no verso do cartão|
|`CreditCard.Brand`|Texto|10|Sim |Bandeira do cartão|
|`CreditCard.SaveCard`|Booleano|10|Não |true se salva o cartão e false para não salvar|

### Resposta

```json
{
  "MerchantOrderId": "2017051104",
  "Customer": {
    "Name": "Nome do Cliente"
  },
  "Payment": {
    "ServiceTaxAmount": 0,
    "Installments": 1,
    "Interest": "ByMerchant",
    "Capture": false,
    "Authenticate": false,
    "Recurrent": false,
    "CreditCard": {
      "CardNumber": "455187******0181",
      "Holder": "Nome do Portador",
      "ExpirationDate": "12/2021",
      "SaveCard": true,
      "CardToken": "250e7c7c-5501-4a7c-aa42-a33d7ad61167",
      "Brand": "Visa"
    },
    "ProofOfSale": "3519928",
    "AcquirerTransactionId": "0511023519928",
    "AuthorizationCode": "536934",
    "PaymentId": "3af00b2d-dbd0-42d6-a669-d4937f0881da",
    "Type": "CreditCard",
    "Amount": 10000,
    "ReceivedDate": "2017-05-11 14:35:19",
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Simulado",
    "ReasonCode": 0,
    "ReasonMessage": "Successful",
    "Status": 1,
    "ProviderReturnCode": "4",
    "ProviderReturnMessage": "Operation Successful",
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/3af00b2d-dbd0-42d6-a669-d4937f0881da"
      },
      {
        "Method": "PUT",
        "Rel": "capture",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/3af00b2d-dbd0-42d6-a669-d4937f0881da/capture"
      },
      {
        "Method": "PUT",
        "Rel": "void",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/3af00b2d-dbd0-42d6-a669-d4937f0881da/void"
      }
    ]
  }
}
```

```shell
curl
--request POST "https://apisandbox.braspag.com.br/v2/sales/"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
  "MerchantOrderId": "2017051104",
  "Customer": {
    "Name": "Nome do Cliente"
  },
  "Payment": {
    "ServiceTaxAmount": 0,
    "Installments": 1,
    "Interest": "ByMerchant",
    "Capture": false,
    "Authenticate": false,
    "Recurrent": false,
    "CreditCard": {
      "CardNumber": "455187******0181",
      "Holder": "Nome do Portador",
      "ExpirationDate": "12/2021",
      "SaveCard": true,
      "CardToken": "250e7c7c-5501-4a7c-aa42-a33d7ad61167",
      "Brand": "Visa"
    },
    "ProofOfSale": "3519928",
    "AcquirerTransactionId": "0511023519928",
    "AuthorizationCode": "536934",
    "PaymentId": "3af00b2d-dbd0-42d6-a669-d4937f0881da",
    "Type": "CreditCard",
    "Amount": 10000,
    "ReceivedDate": "2017-05-11 14:35:19",
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Simulado",
    "ReasonCode": 0,
    "ReasonMessage": "Successful",
    "Status": 1,
    "ProviderReturnCode": "4",
    "ProviderReturnMessage": "Operation Successful",
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/3af00b2d-dbd0-42d6-a669-d4937f0881da"
      },
      {
        "Method": "PUT",
        "Rel": "capture",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/3af00b2d-dbd0-42d6-a669-d4937f0881da/capture"
      },
      {
        "Method": "PUT",
        "Rel": "void",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/3af00b2d-dbd0-42d6-a669-d4937f0881da/void"
      }
    ]
  }
}
--verbose
```

|Propriedade|Descrição|Tipo|Tamanho|Formato|
|-----------|---------|----|-------|-------|
|`AcquirerTransactionId`|Id da transação no provedor de meio de pagamento|Texto|40|Texto alfanumérico|
|`ProofOfSale`|Número do Comprovante de Venda|Texto|20|Texto alfanumérico|
|`AuthorizationCode`|Código de autorização|Texto|300|Texto alfanumérico|
|`PaymentId`|Campo Identificador do Pedido|Guid|36|xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx|
|`ReceivedDate`|Data em que a transação foi recebida pela Brapag|Texto|19|AAAA-MM-DD HH:mm:SS|
|`ReasonCode`|Código de retorno da Adquirência|Texto|32|Texto alfanumérico|
|`ReasonMessage`|Mensagem de retorno da Adquirência|Texto|512|Texto alfanumérico|
|`Status`|Status da Transação|Byte|2|1|
|`ProviderReturnCode`|Código retornado pelo provedor do meio de pagamento (adquirente e bancos)|Texto|32|57|
|`ProviderReturnMessage`|Mensagem retornada pelo provedor do meio de pagamento (adquirente e bancos)|Texto|512|Transação Aprovada|
|`CreditCard.CardToken`|Token no Cartão Protegido que representa os dados do cartão|Guid|36|xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx|


## Criando uma transação com Card Token

Este é um exemplo de como utilizar o Card Token, previamente salvo, para criar uma transação. Por questão de segurança, um Card Token não tem guardado o Código de Segurança. Desta forma, é preciso solicitar esta informação ao portador para cada nova transação (exceto em casos de transações recorrentes).

### Requisição

<aside class="request"><span class="method post">POST</span> <span class="endpoint">/v2/sales/</span></aside>

```json
{
   "MerchantOrderId":"2017051105",
   "Customer":{
      "Name":"Nome do Cliente"
   },
   "Payment":{
     "Provider":"Simulado",
     "Type":"CreditCard",
     "Amount":10000,
     "Installments":1,
     "CreditCard":{
         "CardToken":"250e7c7c-5501-4a7c-aa42-a33d7ad61167",
         "SecurityCode":"123",
         "Brand":"Visa"
     }
   }
}
```

```shell
curl
--request POST "https://apisandbox.braspag.com.br/v2/sales/"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
   "MerchantOrderId":"2017051105",
   "Customer":{
      "Name":"Nome do Cliente"
   },
   "Payment":{
     "Provider":"Simulado",
     "Type":"CreditCard",
     "Amount":10000,
     "Installments":1,
     "CreditCard":{
         "CardToken":"250e7c7c-5501-4a7c-aa42-a33d7ad61167",
         "SecurityCode":"123",
         "Brand":"Visa"
     }
   }
}
--verbose
```

|Propriedade|Tipo|Tamanho|Obrigatório|Descrição|
|-----------|----|-------|-----------|---------|
|`MerchantId`|Guid|36|Sim|Identificador da loja na Braspag|
|`MerchantKey`|Texto|40|Sim|Chave Publica para Autenticação Dupla na Braspag|
|`RequestId`|Guid|36|Não|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT|
|`MerchantOrderId`|Texto|50|Sim|Numero de identificação do Pedido|
|`Customer.Name`|Texto|255|Sim|Nome do comprador|
|`Payment.Provider`|Texto|15|Sim|Nome da provedora de Meio de Pagamento|
|`Payment.Type`|Texto|100|Sim|Tipo do Meio de Pagamento|
|`Payment.Amount`|Número|15|Sim|Valor do Pedido (ser enviado em centavos)|
|`Payment.Installments`|Número|2|Sim|Número de Parcelas|
|`CreditCard.CardToken`|Token no Cartão Protegido que representa os dados do cartão|Guid|36|xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx|
|`CreditCard.SecurityCode`|Texto|4|Sim|Código de segurança impresso no verso do cartão|
|`CreditCard.Brand`|Texto|10|Sim |Bandeira do cartão|

### Resposta

```json
{
  "MerchantOrderId": "2017051105",
  "Customer": {
    "Name": "Nome do Cliente"
  },
  "Payment": {
    "ServiceTaxAmount": 0,
    "Installments": 1,
    "Interest": "ByMerchant",
    "Capture": false,
    "Authenticate": false,
    "Recurrent": false,
    "CreditCard": {
      "SaveCard": false,
      "CardToken": "250e7c7c-5501-4a7c-aa42-a33d7ad61167",
      "Brand": "Visa"
    },
    "ProofOfSale": "124305",
    "AcquirerTransactionId": "0511030124305",
    "AuthorizationCode": "065964",
    "PaymentId": "23cd8bf5-2251-4991-9042-533ff5608788",
    "Type": "CreditCard",
    "Amount": 10000,
    "ReceivedDate": "2017-05-11 15:01:24",
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Simulado",
    "ReasonCode": 0,
    "ReasonMessage": "Successful",
    "Status": 1,
    "ProviderReturnCode": "4",
    "ProviderReturnMessage": "Operation Successful",
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/23cd8bf5-2251-4991-9042-533ff5608788"
      },
      {
        "Method": "PUT",
        "Rel": "capture",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/23cd8bf5-2251-4991-9042-533ff5608788/capture"
      },
      {
        "Method": "PUT",
        "Rel": "void",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/23cd8bf5-2251-4991-9042-533ff5608788/void"
      }
    ]
  }
}
```

```shell
--header "Content-Type: application/json"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
  "MerchantOrderId": "2017051105",
  "Customer": {
    "Name": "Nome do Cliente"
  },
  "Payment": {
    "ServiceTaxAmount": 0,
    "Installments": 1,
    "Interest": "ByMerchant",
    "Capture": false,
    "Authenticate": false,
    "Recurrent": false,
    "CreditCard": {
      "SaveCard": false,
      "CardToken": "250e7c7c-5501-4a7c-aa42-a33d7ad61167",
      "Brand": "Visa"
    },
    "ProofOfSale": "124305",
    "AcquirerTransactionId": "0511030124305",
    "AuthorizationCode": "065964",
    "PaymentId": "23cd8bf5-2251-4991-9042-533ff5608788",
    "Type": "CreditCard",
    "Amount": 10000,
    "ReceivedDate": "2017-05-11 15:01:24",
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Simulado",
    "ReasonCode": 0,
    "ReasonMessage": "Successful",
    "Status": 1,
    "ProviderReturnCode": "4",
    "ProviderReturnMessage": "Operation Successful",
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/23cd8bf5-2251-4991-9042-533ff5608788"
      },
      {
        "Method": "PUT",
        "Rel": "capture",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/23cd8bf5-2251-4991-9042-533ff5608788/capture"
      },
      {
        "Method": "PUT",
        "Rel": "void",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/23cd8bf5-2251-4991-9042-533ff5608788/void"
      }
    ]
  }
}
```

|Propriedade|Descrição|Tipo|Tamanho|Formato|
|-----------|---------|----|-------|-------|
|`AcquirerTransactionId`|Id da transação no provedor de meio de pagamento|Texto|40|Texto alfanumérico|
|`ProofOfSale`|Número do Comprovante de Venda|Texto|20|Texto alfanumérico|
|`AuthorizationCode`|Código de autorização|Texto|300|Texto alfanumérico|
|`PaymentId`|Campo Identificador do Pedido|Guid|36|xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx|
|`ReceivedDate`|Data em que a transação foi recebida pela Brapag|Texto|19|AAAA-MM-DD HH:mm:SS|
|`ReasonCode`|Código de retorno da Adquirência|Texto|32|Texto alfanumérico|
|`ReasonMessage`|Mensagem de retorno da Adquirência|Texto|512|Texto alfanumérico|
|`Status`|Status da Transação|Byte|2|1|
|`ProviderReturnCode`|Código retornado pelo provedor do meio de pagamento (adquirente e bancos)|Texto|32|57|
|`ProviderReturnMessage`|Mensagem retornada pelo provedor do meio de pagamento (adquirente e bancos)|Texto|512|Transação Aprovada|

## Renova Fácil

O serviço do Renova Fácil precisa está habilitado junto a Cielo.
<BR>Bancos Emissores participantes:
<BR>
* Bradesco
* Banco do Brasil
* Santander
* Panamericano
* Citi

## Capturando uma transação

Uma transação pré-autorizada necessita de uma operação de "Captura" para confirmar a transação. Segue o exemplo.

### Requisição

<aside class="request"><span class="method put">PUT</span> <span class="endpoint">/v2/sales/{PaymentId}/capture</span></aside>

```json
```

```shell
curl
--request PUT "https://apisandbox.braspag.com.br/v2/sales/{PaymentId}/capture?amount=xxx&serviceTaxAmount=xxx"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--verbose
```

|Propriedade|Descrição|Tipo|Tamanho|Obrigatório|
|-----------|---------|----|-------|-----------|
|`MerchantId`|Identificador da loja na API. | Guid | 36 | Sim|
|`MerchantKey`|Chave Publica para Autenticação Dupla na API. | Texto | 40 | Sim|
|`RequestId`|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT | Guid | 36 |Não|
|`PaymentId`|Campo Identificador do Pedido. | Guid | 36 | Sim|
|`Amount`|Valor a ser capturado (em centavos). Verificar se a adquirente utilizada suporta uma captura parcial | Número | 15 | Não|
|`ServiceTaxAmount`|Aplicável para companhias aéreas. Montante do valor da autorização que deve ser destinado à taxa de serviço. Obs.: Esse valor não é adicionado ao valor da autorização. | Número | 15 | Não|

### Resposta

```json
{
    "Status": 2,
    "ReasonCode": 0,
    "ReasonMessage": "Successful",
    "ProviderReasonCode": "6",
    "ProviderReasonMessage": "Operation Successful",
    "Links": [
        {
            "Method": "GET",
            "Rel": "self",
            "Href": "https://apiquerysandbox.braspag.com.br/v2/sales/{PaymentId}"
        },
        {
            "Method": "PUT",
            "Rel": "void",
            "Href": "https://apisandbox.braspag.com.br/v2/sales/{PaymentId}/void"
        }
    ]
}
```

```shell
--header "Content-Type: application/json"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
    "Status": 2,
    "ReasonCode": 0,
    "ReasonMessage": "Successful",
    "ProviderReasonCode": "6",
    "ProviderReasonMessage": "Operation Successful",
    "Links": [
        {
            "Method": "GET",
            "Rel": "self",
            "Href": "https://apiquerysandbox.braspag.com.br/v2/sales/{PaymentId}"
        },
        {
            "Method": "PUT",
            "Rel": "void",
            "Href": "https://apisandbox.braspag.com.br/v2/sales/{PaymentId}/void"
        }
    ]
}
```

|Propriedade|Descrição|Tipo|Tamanho|Formato|
|-----------|---------|----|-------|-------|
|`Status`|Status da Transação. | Byte | --- | 2|
|`ReasonCode`|Código de retorno da adquirente. | Texto | 32 | Texto alfanumérico |
|`ReasonMessage`|Mensagem de retorno da adquirente. | Texto | 512 | Texto alfanumérico |

## Cancelando/Estornando uma transação

Para cancelar uma transação que utilizou cartão de crédito, é necessário fazer um PUT para o recurso Payment conforme o exemplo.

### Requisição

<aside class="request"><span class="method put">PUT</span> <span class="endpoint">/v2/sales/{PaymentId}/void?amount=xxx</span></aside>

```json
```

```shell
curl
--request PUT "https://apisandbox.braspag.com.br/v2/sales/{PaymentId}/void?amount=xxx"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--verbose
```

|Propriedade|Descrição|Tipo|Tamanho|Obrigatório|
|-----------|---------|----|-------|-----------|
|`MerchantId`|Identificador da loja na API. |Guid |36 |Sim|
|`MerchantKey`|Chave Publica para Autenticação Dupla na API. |Texto |40 |Sim|
|`RequestId`|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT | Guid | 36 |Não|
|`PaymentId`|Campo Identificador do Pedido. |Guid |36 |Sim|
|`Amount`|Valor a ser cancelado/estornado (ser enviado em centavos). Verifique se a adquirente contratada suporta a operação de cancelamento ou estorno|Número |15 |Não|

### Resposta

```json
{
    "Status": 10,
    "ReasonCode": 0,
    "ReasonMessage": "Successful",
    "ProviderReasonCode": "9",
    "ProviderReasonMessage": "Operation Successful",
    "Links": [
        {
            "Method": "GET",
            "Rel": "self",
            "Href": "https://apiquerysandbox.braspag.com.br/v2/sales/{PaymentId}"
        }
    ]
}
```

```shell
{
    "Status": 10,
    "ReasonCode": 0,
    "ReasonMessage": "Successful",
    "ProviderReasonCode": "9",
    "ProviderReasonMessage": "Operation Successful",
    "Links": [
        {
            "Method": "GET",
            "Rel": "self",
            "Href": "https://apiquerysandbox.braspag.com.br/v2/sales/{PaymentId}"
        }
    ]
}
```

|Propriedade|Descrição|Tipo|Tamanho|Formato|
|-----------|---------|----|-------|-------|
|`Status`|Status da Transação. |Byte |--- |10|
|`ReasonCode`|Código de retorno da Adquirência. |Texto |32 |Texto alfanumérico 
|`ReasonMessage`|Mensagem de retorno da Adquirência. |Texto |512 |Texto alfanumérico 

# Pagamentos com Cartão de Débito

## Criando uma transação

Uma transação com um Cartão de Débito se efetua de uma forma semelhante a um Cartão de Crédito, porém, é obrigatório submetê-la ao processo de autenticação. 

### Requisição

<aside class="request"><span class="method post">POST</span> <span class="endpoint">/v2/sales/</span></aside>

```json
{
   "MerchantOrderId":"2017051107",
   "Customer":{
      "Name":"Nome do Cliente"
   },
   "Payment":{
     "Provider":"Cielo",
     "Type":"DebitCard",
     "Amount":10000,
     "Installments":1,
     "ReturnUrl":"http://www.braspag.com.br",
     "DebitCard":{
         "CardNumber":"4551870000000181",
         "Holder":"Nome do Portador",
         "ExpirationDate":"12/2021",
         "SecurityCode":"123",
         "Brand":"Visa"
     }
   }
}
--verbose
```

```shell
curl
--request POST "https://apisandbox.braspag.com.br/v2/sales/"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
   "MerchantOrderId":"2017051107",
   "Customer":{
      "Name":"Nome do Cliente"
   },
   "Payment":{
     "Provider":"Cielo",
     "Type":"DebitCard",
     "Amount":10000,
     "Installments":1,
     "ReturnUrl":"http://www.braspag.com.br",
     "DebitCard":{
         "CardNumber":"4551870000000181",
         "Holder":"Nome do Portador",
         "ExpirationDate":"12/2021",
         "SecurityCode":"123",
         "Brand":"Visa"
     }
   }
}
--verbose
```

|Propriedade|Tipo|Tamanho|Obrigatório|Descrição|
|-----------|----|-------|-----------|---------|
|`MerchantId`|Guid|36|Sim|Identificador da loja na Braspag|
|`MerchantKey`|Texto|40|Sim|Chave Publica para Autenticação Dupla na Braspag|
|`RequestId`|Guid|36|Não|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT|
|`MerchantOrderId`|Texto|50|Sim|Numero de identificação do Pedido|
|`Customer.Name`|Texto|255|Sim|Nome do comprador|
|`Payment.Provider`|Texto|15|Sim|Nome da provedora de Meio de Pagamento. Atualmente somente a "Cielo" suporta esta forma de pagamento via Pagador|
|`Payment.Type`|Texto|100|Sim|Tipo do Meio de Pagamento. No caso do cartão de débito (DebitCard)|
|`Payment.Amount`|Número|15|Sim|Valor do Pedido (ser enviado em centavos)|
|`Payment.Installments`|Número|2|Sim|Número de Parcelas|
|`Payment.ReturnUrl`|URL para onde o usuário será redirecionado após o fim do pagamento|Texto |1024 |Sim|
|`DebitCard.CardNumber`|Texto|16|Sim|Número do Cartão do comprador|
|`DebitCard.Holder`|Texto|25|Sim|Nome do Comprador impresso no cartão|
|`DebitCard.ExpirationDate`|Texto|7|Sim|Data de validade impresso no cartão, no formato MM/AAAA|
|`DebitCard.SecurityCode`|Texto|4|Sim|Código de segurança impresso no verso do cartão|
|`DebitCard.Brand`|Texto|10|Sim |Bandeira do cartão|

### Resposta

```json
{
  "MerchantOrderId": "2017051107",
  "Customer": {
    "Name": "Nome do Cliente"
  },
  "Payment": {
    "DebitCard": {
      "CardNumber": "455187******0181",
      "Holder": "Nome do Portador",
      "ExpirationDate": "12/2021",
      "SaveCard": false,
      "Brand": "Visa"
    },
    "AuthenticationUrl": "https://qasecommerce.cielo.com.br/web/index.cbmp?id=13fda1da8e3d90d3d0c9df8820b96a7f",
    "AcquirerTransactionId": "10069930690009D366FA",
    "PaymentId": "21423fa4-6bcf-448a-97e0-e683fa2581ba",
    "Type": "DebitCard",
    "Amount": 10000,
    "ReceivedDate": "2017-05-11 15:19:58",
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Cielo",
    "ReturnUrl": "http://www.braspag.com.br",
    "ReasonCode": 9,
    "ReasonMessage": "Waiting",
    "Status": 0,
    "ProviderReturnCode": "0",
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/21423fa4-6bcf-448a-97e0-e683fa2581ba"
      },
      {
        "Method": "PUT",
        "Rel": "void",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/21423fa4-6bcf-448a-97e0-e683fa2581ba/void"
      }
    ]
  }
}
```

```shell
--header "Content-Type: application/json"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
  "MerchantOrderId": "2017051107",
  "Customer": {
    "Name": "Nome do Cliente"
  },
  "Payment": {
    "DebitCard": {
      "CardNumber": "455187******0181",
      "Holder": "Nome do Portador",
      "ExpirationDate": "12/2021",
      "SaveCard": false,
      "Brand": "Visa"
    },
    "AuthenticationUrl": "https://qasecommerce.cielo.com.br/web/index.cbmp?id=13fda1da8e3d90d3d0c9df8820b96a7f",
    "AcquirerTransactionId": "10069930690009D366FA",
    "PaymentId": "21423fa4-6bcf-448a-97e0-e683fa2581ba",
    "Type": "DebitCard",
    "Amount": 10000,
    "ReceivedDate": "2017-05-11 15:19:58",
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Cielo",
    "ReturnUrl": "http://www.braspag.com.br",
    "ReasonCode": 9,
    "ReasonMessage": "Waiting",
    "Status": 0,
    "ProviderReturnCode": "0",
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/21423fa4-6bcf-448a-97e0-e683fa2581ba"
      },
      {
        "Method": "PUT",
        "Rel": "void",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/21423fa4-6bcf-448a-97e0-e683fa2581ba/void"
      }
    ]
  }
}
```

|Propriedade|Descrição|Tipo|Tamanho|Formato|
|-----------|---------|----|-------|-------|
|`AcquirerTransactionId`|Id da transação no provedor de meio de pagamento|Texto|40|Texto alfanumérico|
|`ProofOfSale`|Número do Comprovante de Venda|Texto|20|Texto alfanumérico|
|`AuthorizationCode`|Código de autorização|Texto|300|Texto alfanumérico|
|`PaymentId`|Campo Identificador do Pedido|Guid|36|xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx|
|`ReceivedDate`|Data em que a transação foi recebida pela Brapag|Texto|19|AAAA-MM-DD HH:mm:SS|
|`ReasonCode`|Código de retorno da Adquirência|Texto|32|Texto alfanumérico|
|`ReasonMessage`|Mensagem de retorno da Adquirência|Texto|512|Texto alfanumérico|
|`Status`|Status da Transação|Byte|2|1|
|`ProviderReturnCode`|Código retornado pelo provedor do meio de pagamento (adquirente e bancos)|Texto|32|57|
|`ProviderReturnMessage`|Mensagem retornada pelo provedor do meio de pagamento (adquirente e bancos)|Texto|512|Transação Aprovada|
|`AuthenticationUrl`|URL para o qual o portador será redirecionado para autenticação |Texto |56 |https://qasecommerce.cielo.com.br/web/index.cbmp?id=13fda1da8e3d90d3d0c9df8820b96a7f|

# Pagamentos com Transferência Eletrônica (Débito Online)
	
## Criando uma transação

Para criar uma venda de transferência eletrônica, é necessário fazer um POST para o recurso Payment conforme o exemplo. 

### Requisição

<aside class="request"><span class="method post">POST</span> <span class="endpoint">/v2/sales/</span></aside>

```json
{  
    "MerchantOrderId":"2017051109",
    "Customer":
    {  
        "Name":"Nome do Comprador",
        "Identity": "12345678909",
        "IdentityType": "CPF",
        "Email": "comprador@braspag.com.br",
        "Address": 
        {
			 "Street":"Alameda Xingu",
			 "Number":"512",
			 "Complement":"27 andar",
			 "ZipCode":"12345987",
			 "City":"São Paulo",
			 "State":"SP",
			 "Country":"BRA",
			 "District":"Alphaville"
        }
    },
    "Payment":
    {  
        "Provider":"Bradesco",
        "Type":"EletronicTransfer",
        "Amount":10000,
        "ReturnUrl":"http://www.braspag.com.br"
    }
}
```

```shell
curl
--request POST "https://apisandbox.braspag.com.br/v2/sales/"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{  
    "MerchantOrderId":"2017051109",
    "Customer":
    {  
        "Name":"Nome do Comprador",
        "Identity": "12345678909",
        "IdentityType": "CPF",
        "Email": "comprador@braspag.com.br",
        "Address": 
        {
			 "Street":"Alameda Xingu",
			 "Number":"512",
			 "Complement":"27 andar",
			 "ZipCode":"12345987",
			 "City":"São Paulo",
			 "State":"SP",
			 "Country":"BRA",
			 "District":"Alphaville"
        }
    },
    "Payment":
    {  
        "Provider":"Bradesco",
        "Type":"EletronicTransfer",
        "Amount":10000,
        "ReturnUrl":"http://www.braspag.com.br"
    }
}
--verbose
```

|Propriedade|Descrição|Tipo|Tamanho|Obrigatório|
|-----------|---------|----|-------|-----------|
|`MerchantId`|Identificador da loja na API. |Guid |36 |Sim|
|`MerchantKey`|Chave Publica para Autenticação Dupla na API. |Texto |40 |Sim|
|`RequestId`|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT | Guid | 36 |Não|
|`MerchantOrderId`|Numero de identificação do Pedido. |Texto |50 |Sim|
|`Customer.Name`|Texto|255|Sim|Nome do comprador|
|`Customer.Identity`|Texto |14 |Não|Número do RG, CPF ou CNPJ do Cliente| 
|`Customer.IdentityType`|Texto|255|Não|Tipo de documento de identificação do comprador (CPF ou CNPJ)|
|`Customer.Email`|Texto|255|Não|Email do comprador|
|`Customer.Address.Street`|Texto|255|Sim|Endereço de contato do comprador|
|`Customer.Address.Number`|Texto|15|Sim|Número endereço de contato do comprador|
|`Customer.Address.Complement`|Texto|50|Sim|Complemento do endereço de contato do Comprador.br|
|`Customer.Address.ZipCode`|Texto|9|Sim|CEP do endereço de contato do comprador|
|`Customer.Address.City`|Texto|50|Sim|Cidade do endereço de contato do comprador|
|`Customer.Address.State`|Texto|2|Sim|Estado do endereço de contato do comprador|
|`Customer.Address.Country`|Texto|35|Sim|Pais do endereço de contato do comprador|
|`Customer.Address.District`|Texto|35|Sim|Bairro do endereço de contato do comprador|
|`Payment.Type`|Tipo do Meio de Pagamento. |Texto |100 |Sim|
|`Payment.Amount`|Valor do Pedido (ser enviado em centavos)|Número |15 |Sim|
|`Payment.Provider`|Nome da provedora de Meio de Pagamento|Texto |15 |---|

### Resposta

```json
{
    "MerchantOrderId": "2017051109",
    "Customer": {
        "Name":"Nome do Comprador",
        "Identity": "12345678909",
        "IdentityType": "CPF",
        "Email": "comprador@braspag.com.br",
        "Address": 
        {
			 "Street":"Alameda Xingu",
			 "Number":"512",
			 "Complement":"27 andar",
			 "ZipCode":"12345987",
			 "City":"São Paulo",
			 "State":"SP",
			 "Country":"BRA",
			 "District":"Alphaville"
        }
    },
    "Payment": {
        "Url": "https://xxx.xxxxxxx.xxx.xx/post/EletronicTransfer/Redirect/{PaymentId}",
        "PaymentId": "765548b6-c4b8-4e2c-b9b9-6458dbd5da0a",
        "Type": "EletronicTransfer",
        "Amount": 10000,
        "ReceivedDate": "2015-06-25 09:37:55",
        "Currency": "BRL",
        "Country": "BRA",
        "Provider": "Bradesco",
        "ReturnUrl": "http://www.braspag.com.br",
        "ReasonCode": 0,
        "ReasonMessage": "Successful",
        "Status": 0,
        "Links": [
            {
                "Method": "GET",
                "Rel": "self",
                "Href": "https://apiquerysandbox.braspag.com.br/v2/sales/{PaymentId}"
            }
        ]
    }
}
```

```shell
--header "Content-Type: application/json"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
    "MerchantOrderId": "2017051109",
    "Customer": {
        "Name": "Comprador Teste",
    },
    "Payment": {
        "Url": "https://xxx.xxxxxxx.xxx.xx/post/EletronicTransfer/Redirect/{PaymentId}",
        "PaymentId": "765548b6-c4b8-4e2c-b9b9-6458dbd5da0a",
        "Type": "EletronicTransfer",
        "Amount": 10000,
        "ReceivedDate": "2015-06-25 09:37:55",
        "Currency": "BRL",
        "Country": "BRA",
        "Provider": "Bradesco",
        "ReturnUrl": "http://www.braspag.com.br",
        "ReasonCode": 0,
        "ReasonMessage": "Successful",
        "Status": 0,
        "Links": [
            {
                "Method": "GET",
                "Rel": "self",
                "Href": "https://apiquerysandbox.braspag.com.br/v2/sales/{PaymentId}"
            }
        ]
    }
}
```

|Propriedade|Descrição|Tipo|Tamanho|Formato|
|-----------|---------|----|-------|-------|
|`PaymentId`|Campo Identificador do Pedido|Guid|36|xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx|
|`Url`|URL para a qual o comprador deverá ser redirecionado para autenticação da Transferência Eletrônica |Texto |256 |Url de Autenticação|
|`Status`|Status da Transação|Byte|2|1|

# Pagamentos com Boleto

## Criando uma transação de Boleto sem Registro

Para criar uma transação de Boleto sem Registro, basta fazer um POST conforme o exemplo.

### Requisição

<aside class="request"><span class="method post">POST</span> <span class="endpoint">/v2/sales/</span></aside>

```json
{  
    "MerchantOrderId":"2017051110",
    "Customer":
    {  
        "Name":"Nome do Comprador"     
    },
    "Payment":
    {  
        "Provider":"Simulado",
		"Type":"Boleto",
        "Amount":10000,
		"Assignor": "Braspag Tecnologia de Pagamento Ltda",
        "Demonstrative": "Texto para Demonstrativo",
        "ExpirationDate": "2017-12-31",
        "Identification": "01234567000189",
        "Instructions": "Aceitar somente até a data de vencimento"		
    }
}
--verbose
```

```shell
curl
--request POST "https://apisandbox.braspag.com.br/v2/sales/"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{  
    "MerchantOrderId":"2017051110",
    "Customer":
    {  
        "Name":"Nome do Comprador"     
    },
    "Payment":
    {  
        "Provider":"Simulado",
		"Type":"Boleto",
        "Amount":10000,
		"Assignor": "Braspag Tecnologia de Pagamento Ltda",
        "Demonstrative": "Texto para Demonstrativo",
        "ExpirationDate": "2017-12-31",
        "Identification": "01234567000189",
        "Instructions": "Aceitar somente até a data de vencimento"
    }
}
--verbose
```

|Propriedade|Tipo|Tamanho|Obrigatório|Descrição|
|-----------|----|-------|-----------|---------|
|`MerchantId`|Guid|36|Sim|Identificador da loja na Braspag|
|`MerchantKey`|Texto|40|Sim|Chave Publica para Autenticação Dupla na Braspag|
|`RequestId`|Guid|36|Não|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT|
|`MerchantOrderId`|Texto|50|Sim|Numero de identificação do Pedido|
|`Customer.Name`|Texto|255|Sim|Nome do comprador|
|`Payment.Provider`|Texto|15|Sim|Nome da provedora de Meio de Pagamento de Boleto|
|`Payment.Type`|Texto|100|Sim|Tipo do Meio de Pagamento. No caso "Boleto"|
|`Payment.Amount`|Número|15|Sim|Valor do Pedido (ser enviado em centavos)|
|`Payment.BoletoNumber`|Texto |50 |Não| Número do Boleto ("Nosso Número"). Caso preenchido, sobrepõe o valor configurado no meio de pagamento |
|`Payment.Assignor`|Texto |200|Não|Nome do Cedente. Caso preenchido, sobrepõe o valor configurado no meio de pagamento|
|`Payment.Demonstrative`|Texto |450|Não|Texto de Demonstrativo. Caso preenchido, sobrepõe o valor configurado no meio de pagamento|
|`Payment.ExpirationDate`|Date |10 |Não|Dias para vencer o boleto. Caso preenchido, sobrepõe o valor configurado no meio de pagamento|
|`Payment.Identification`|Texto |14 |Não|CNPJ do Cedente. Caso preenchido, sobrepõe o valor configurado no meio de pagamento|
|`Payment.Instructions`|Texto |450|Não|Instruções do Boleto. Caso preenchido, sobrepõe o valor configurado no meio de pagamento|

### Resposta

```json
{
  "MerchantOrderId": "2017051110",
  "Customer": {
    "Name": "Nome do Comprador"
  },
  "Payment": {
    "Instructions": "Aceitar somente até a data de vencimento.",
    "ExpirationDate": "2017-12-31",
    "Demonstrative": "Desmonstrative Teste",
    "Url": "https://homologacao.pagador.com.br/post/pagador/reenvia.asp/f892e7bb-e27f-4e81-b23d-036f8ee272a9",
    "BoletoNumber": "2633-2",
    "BarCodeNumber": "00092739000000100000494250000000263300656560",
    "DigitableLine": "00090.49420 50000.000260 33006.565601 2 73900000010000",
    "Assignor": "Empresa Teste",
    "Address": "Av. Brigadeiro Faria Lima, 160, Av. Brigadeiro Faria Lima",
    "Identification": "12346578909",
    "PaymentId": "f892e7bb-e27f-4e81-b23d-036f8ee272a9",
    "Type": "Boleto",
    "Amount": 10000,
    "ReceivedDate": "2017-05-11 16:25:36",
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Simulado",
    "ReasonCode": 0,
    "ReasonMessage": "Successful",
    "Status": 1,
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/f892e7bb-e27f-4e81-b23d-036f8ee272a9"
      }
    ]
  }
}
```

```shell
--header "Content-Type: application/json"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
  "MerchantOrderId": "2017051110",
  "Customer": {
    "Name": "Nome do Comprador"
  },
  "Payment": {
    "Instructions": "Aceitar somente até a data de vencimento.",
    "ExpirationDate": "2017-12-31",
    "Demonstrative": "Desmonstrative Teste",
    "Url": "https://homologacao.pagador.com.br/post/pagador/reenvia.asp/f892e7bb-e27f-4e81-b23d-036f8ee272a9",
    "BoletoNumber": "2633-2",
    "BarCodeNumber": "00092739000000100000494250000000263300656560",
    "DigitableLine": "00090.49420 50000.000260 33006.565601 2 73900000010000",
    "Assignor": "Empresa Teste",
    "Address": "Av. Brigadeiro Faria Lima, 160, Av. Brigadeiro Faria Lima",
    "Identification": "12346578909",
    "PaymentId": "f892e7bb-e27f-4e81-b23d-036f8ee272a9",
    "Type": "Boleto",
    "Amount": 10000,
    "ReceivedDate": "2017-05-11 16:25:36",
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Simulado",
    "ReasonCode": 0,
    "ReasonMessage": "Successful",
    "Status": 1,
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/f892e7bb-e27f-4e81-b23d-036f8ee272a9"
      }
    ]
  }
}
```

|Propriedade|Descrição|Tipo|Tamanho|Formato|
|-----------|---------|----|-------|-------|
|`PaymentId`|Campo Identificador do Pedido. |Guid |36 |xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx |
|`ExpirationDate`|Data de expiração. |Texto |10 |2014-12-25 |
|`Url`|URL do Boleto gerado |string |256 |https://.../pagador/reenvia.asp/8464a692-b4bd-41e7-8003-1611a2b8ef2d |
|`Number`|"NossoNumero" gerado. |Texto|50 |1000000012-8 |
|`BarCodeNumber`|Representação numérica do código de barras. |Texto |44 |00091628800000157000494250100000001200656560 |
|`DigitableLine`|Linha digitável. |Texto |256 |00090.49420 50100.000004 12006.565605 1 62880000015700 |
|`Address`|Endereço do Loja cadastrada no banco |Texto |256 |Av. Teste, 160 |
|`Status`|Status da Transação. |Byte |--- |1|

## Criando uma transação de Boleto com Registro

Para gerar um boleto registrado, é necessário fornecer alguns dados a mais do comprador como CPF e endereço.

### Requisição

<aside class="request"><span class="method post">POST</span> <span class="endpoint">/v2/sales/</span></aside>

```json
{  
    "MerchantOrderId":"2017051113",
    "Customer":
    {  
        "Name":"Nome do Comprador",
        "Identity":"12345678909",
		"IdentityType":"CPF",
		"Address":{  
			"Street":"Alameda Xingu",
			"Number":"512",
			"Complement":"27 andar",
			"ZipCode":"12345987",
			"City":"São Paulo",
			"State":"SP",
			"Country":"BRA",
			"District":"Alphaville"
		}	
    },
    "Payment":
    {  
        "Provider":"Simulado",
		"Type":"Boleto",
        "Amount":10000,
        "Assignor": "Empresa Teste",
        "Demonstrative": "Desmonstrative Teste",
        "ExpirationDate": "2017-12-31",
        "Identification": "12346578909",
        "Instructions": "Aceitar somente até a data de vencimento."      
    }
}
```

```shell
curl
--request POST "https://apisandbox.braspag.com.br/v2/sales/"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{  
    "MerchantOrderId":"2017051113",
    "Customer":
    {  
        "Name":"Nome do Comprador",
        "Identity":"12345678909",
		"IdentityType":"CPF",
		"Address":{  
			"Street":"Alameda Xingu",
			"Number":"512",
			"Complement":"27 andar",
			"ZipCode":"12345987",
			"City":"São Paulo",
			"State":"SP",
			"Country":"BRA",
			"District":"Alphaville"
		}	
    },
    "Payment":
    {  
        "Provider":"Simulado",
		"Type":"Boleto",
        "Amount":10000,
        "Assignor": "Empresa Teste",
        "Demonstrative": "Desmonstrative Teste",
        "ExpirationDate": "2017-12-31",
        "Identification": "12346578909",
        "Instructions": "Aceitar somente até a data de vencimento."      
    }
}
--verbose
```

|Propriedade|Tipo|Tamanho|Obrigatório|Descrição|
|-----------|----|-------|-----------|---------|
|`MerchantId`|Guid|36|Sim|Identificador da loja na Braspag|
|`MerchantKey`|Texto|40|Sim|Chave Publica para Autenticação Dupla na Braspag|
|`RequestId`|Guid|36|Não|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT|
|`MerchantOrderId`|Texto|50|Sim|Numero de identificação do Pedido|
|`Customer.Name`|Texto|255|Sim|Nome do comprador|
|`Customer.Identity`|Texto |14 |Sim|Número do RG, CPF ou CNPJ do Cliente| 
|`Customer.IdentityType`|Texto|255|Sim|Tipo de documento de identificação do comprador (CPF ou CNPJ)|
|`Customer.Address.Street`|Texto|255|Sim|Endereço de contato do comprador|
|`Customer.Address.Number`|Texto|15|Sim|Número endereço de contato do comprador|
|`Customer.Address.Complement`|Texto|50|Não|Complemento do endereço de contato do Comprador|
|`Customer.Address.ZipCode`|Texto|9|Sim|CEP do endereço de contato do comprador|
|`Customer.Address.City`|Texto|50|Sim|Cidade do endereço de contato do comprador|
|`Customer.Address.State`|Texto|2|Sim|Estado do endereço de contato do comprador|
|`Customer.Address.Country`|Texto|35|Sim|Pais do endereço de contato do comprador|
|`Payment.Provider`|Texto|15|Sim|Nome da provedora de Meio de Pagamento de Boleto|
|`Payment.Type`|Texto|100|Sim|Tipo do Meio de Pagamento. No caso "Boleto"|
|`Payment.Amount`|Número|15|Sim|Valor do Pedido (deve ser enviado em centavos)|
|`Payment.BoletoNumber`|Texto |50 |Não| Número do Boleto ("Nosso Número"). Caso preenchido, sobrepõe o valor configurado no meio de pagamento |
|`Payment.Assignor`|Texto |200|Não|Nome do Cedente. Caso preenchido, sobrepõe o valor configurado no meio de pagamento|
|`Payment.Demonstrative`|Texto |450|Não|Texto de Demonstrativo. Caso preenchido, sobrepõe o valor configurado no meio de pagamento|
|`Payment.ExpirationDate`|Date |10 |Não|Dias para vencer o boleto. Caso preenchido, sobrepõe o valor configurado no meio de pagamento|
|`Payment.Identification`|Texto |14 |Não|CNPJ do Cedente. Caso preenchido, sobrepõe o valor configurado no meio de pagamento|
|`Payment.Instructions`|Texto |450|Não|Instruções do Boleto. Caso preenchido, sobrepõe o valor configurado no meio de pagamento|

### Resposta

```json
{
  "MerchantOrderId": "2017051113",
  "Customer": {
    "Name": "Nome do Comprador",
    "Identity": "12345678909",
    "IdentityType": "CPF",
    "Address": {
      "Street": "Alameda Xingu",
      "Number": "512",
      "Complement": "27 andar",
      "ZipCode": "12345987",
      "City": "São Paulo",
      "State": "SP",
      "Country": "BRA"
    }
  },
  "Payment": {
    "Instructions": "Aceitar somente até a data de vencimento.",
    "ExpirationDate": "2017-12-31",
    "Demonstrative": "Desmonstrative Teste",
    "Url": "https://homologacao.pagador.com.br/post/pagador/reenvia.asp/d24b0aa4-21c9-449d-b85c-6279333f070f",
    "BoletoNumber": "2634-0",
    "BarCodeNumber": "00091739000000100000494250000000263400656560",
    "DigitableLine": "00090.49420 50000.000260 34006.565609 1 73900000010000",
    "Assignor": "Empresa Teste",
    "Address": "Av. Brigadeiro Faria Lima, 160",
    "Identification": "12346578909",
    "PaymentId": "d24b0aa4-21c9-449d-b85c-6279333f070f",
    "Type": "Boleto",
    "Amount": 10000,
    "ReceivedDate": "2017-05-11 16:42:55",
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Simulado",
    "ReasonCode": 0,
    "ReasonMessage": "Successful",
    "Status": 1,
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/d24b0aa4-21c9-449d-b85c-6279333f070f"
      }
    ]
  }
}
```

```shell
--header "Content-Type: application/json"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
  "MerchantOrderId": "2017051113",
  "Customer": {
    "Name": "Nome do Comprador",
    "Identity": "12345678909",
    "IdentityType": "CPF",
    "Address": {
      "Street": "Alameda Xingu",
      "Number": "512",
      "Complement": "27 andar",
      "ZipCode": "12345987",
      "City": "São Paulo",
      "State": "SP",
      "Country": "BRA",
	  "District":"Alphaville"
    }
  },
  "Payment": {
    "Instructions": "Aceitar somente até a data de vencimento.",
    "ExpirationDate": "2017-12-31",
    "Demonstrative": "Desmonstrative Teste",
    "Url": "https://homologacao.pagador.com.br/post/pagador/reenvia.asp/d24b0aa4-21c9-449d-b85c-6279333f070f",
    "BoletoNumber": "2634-0",
    "BarCodeNumber": "00091739000000100000494250000000263400656560",
    "DigitableLine": "00090.49420 50000.000260 34006.565609 1 73900000010000",
    "Assignor": "Empresa Teste",
    "Address": "Av. Brigadeiro Faria Lima, 160",
    "Identification": "12346578909",
    "PaymentId": "d24b0aa4-21c9-449d-b85c-6279333f070f",
    "Type": "Boleto",
    "Amount": 10000,
    "ReceivedDate": "2017-05-11 16:42:55",
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Simulado",
    "ReasonCode": 0,
    "ReasonMessage": "Successful",
    "Status": 1,
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/d24b0aa4-21c9-449d-b85c-6279333f070f"
      }
    ]
  }
}
```

|Propriedade|Descrição|Tipo|Tamanho|Formato|
|-----------|---------|----|-------|-------|
|`PaymentId`|Campo Identificador do Pedido. |Guid |36 |xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx |
|`ExpirationDate`|Data de expiração. |Texto |10 |2014-12-25 |
|`Url`|URL do Boleto gerado |string |256 |https://.../pagador/reenvia.asp/8464a692-b4bd-41e7-8003-1611a2b8ef2d |
|`Number`|"NossoNumero" gerado. |Texto|50 |1000000012-8 |
|`BarCodeNumber`|Representação numérica do código de barras. |Texto |44 |00091628800000157000494250100000001200656560 |
|`DigitableLine`|Linha digitável. |Texto |256 |00090.49420 50100.000004 12006.565605 1 62880000015700 |
|`Address`|Endereço do Loja cadastrada no banco |Texto |256 |Av. Teste, 160 |
|`Status`|Status da Transação. |Byte |--- |1|

# Pagamentos Recorrentes

## Autorizar uma transação e agendar as próximas recorrências

Neste exemplo, uma transação é submetida para autorização, e quando aprovada, a recorrência é agendada conforme as regras especificadas na transação. Uma transação recorrente não poderá ser parcelada. 

### Requisição

<aside class="request"><span class="method post">POST</span> <span class="endpoint">/v2/sales/</span></aside>

```json
{
   "MerchantOrderId":"2017051113",
   "Customer":{
      "Name":"Nome do Cliente"
   },
   "Payment":{
     "Provider":"Simulado",
     "Type":"CreditCard",
     "Amount":10000,
     "Installments":1,
     "CreditCard":{
         "CardNumber":"4551870000000181",
         "Holder":"Nome do Portador",
         "ExpirationDate":"12/2021",
         "SecurityCode":"123",
         "Brand":"Visa"
     },
     "RecurrentPayment":{
       "AuthorizeNow":"true",
       "EndDate":"2019-12-31",
       "Interval":"Monthly"
     }
   }
}
```

```shell
curl
--request POST "https://apisandbox.braspag.com.br/v2/sales/"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
   "MerchantOrderId":"2017051113",
   "Customer":{
      "Name":"Nome do Cliente"
   },
   "Payment":{
     "Provider":"Simulado",
     "Type":"CreditCard",
     "Amount":10000,
     "Installments":1,
     "CreditCard":{
         "CardNumber":"4551870000000181",
         "Holder":"Nome do Portador",
         "ExpirationDate":"12/2021",
         "SecurityCode":"123",
         "Brand":"Visa"
     },
     "RecurrentPayment":{
       "AuthorizeNow":"true",
       "EndDate":"2019-12-31",
       "Interval":"Monthly"
     }
   }
}
--verbose
```

|Propriedade|Tipo|Tamanho|Obrigatório|Descrição|
|-----------|----|-------|-----------|---------|
|`MerchantId`|Guid|36|Sim|Identificador da loja na Braspag|
|`MerchantKey`|Texto|40|Sim|Chave Publica para Autenticação Dupla na Braspag|
|`RequestId`|Guid|36|Não|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT|
|`MerchantOrderId`|Texto|50|Sim|Numero de identificação do Pedido|
|`Customer.Name`|Texto|255|Sim|Nome do comprador|
|`Payment.Provider`|Texto|15|Sim|Nome da provedora de Meio de Pagamento|
|`Payment.Type`|Texto|100|Sim|Tipo do Meio de Pagamento|
|`Payment.Amount`|Número|15|Sim|Valor do Pedido (ser enviado em centavos)|
|`Payment.Installments`|Número|2|Sim|Número de Parcelas|
|`Payment.RecurrentPayment.EndDate`|Texto |10 |Não|Data para termino da recorrência|
|`Payment.RecurrentPayment.Interval`|Texto |10 |Não|Intervalo da recorrência.<br /><ul><li>Monthly (Default) </li><li>Bimonthly </li><li>Quarterly </li><li>SemiAnnual </li><li>Annual</li></ul> |
|`Payment.RecurrentPayment.AuthorizeNow`|Booleano |--- |Sim|Se true, autoriza no momento da requisição. false para agendamento futuro|
|`CreditCard.CardNumber`|Texto|16|Sim|Número do Cartão do comprador|
|`CreditCard.Holder`|Texto|25|Sim|Nome do Comprador impresso no cartão|
|`CreditCard.ExpirationDate`|Texto|7|Sim|Data de validade impresso no cartão, no formato MM/AAAA|
|`CreditCard.SecurityCode`|Texto|4|Sim|Código de segurança impresso no verso do cartão|
|`CreditCard.Brand`|Texto|10|Sim |Bandeira do cartão|

### Resposta

```json
{
  "MerchantOrderId": "2017051113",
  "Customer": {
    "Name": "Nome do Cliente"
  },
  "Payment": {
    "ServiceTaxAmount": 0,
    "Installments": 1,
    "Interest": "ByMerchant",
    "Capture": false,
    "Authenticate": false,
    "Recurrent": false,
    "CreditCard": {
      "CardNumber": "455187******0181",
      "Holder": "Nome do Portador",
      "ExpirationDate": "12/2021",
      "SaveCard": false,
      "Brand": "Visa"
    },
    "ProofOfSale": "5646418",
    "AcquirerTransactionId": "0511045646418",
    "AuthorizationCode": "100024",
    "PaymentId": "067f73ce-62fb-4d76-871d-0bcbb88fbd22",
    "Type": "CreditCard",
    "Amount": 10000,
    "ReceivedDate": "2017-05-11 16:56:46",
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Simulado",
    "ReasonCode": 0,
    "ReasonMessage": "Successful",
    "Status": 1,
    "ProviderReturnCode": "4",
    "ProviderReturnMessage": "Operation Successful",
    "RecurrentPayment": {
      "RecurrentPaymentId": "808d3631-47ca-43b4-97f5-bd29ab06c271",
      "ReasonCode": 0,
      "ReasonMessage": "Successful",
      "NextRecurrency": "2017-06-11",
      "EndDate": "2019-12-31",
      "Interval": "Monthly",
      "Link": {
        "Method": "GET",
        "Rel": "recurrentPayment",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/RecurrentPayment/808d3631-47ca-43b4-97f5-bd29ab06c271"
      },
      "AuthorizeNow": true
    },
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/067f73ce-62fb-4d76-871d-0bcbb88fbd22"
      },
      {
        "Method": "PUT",
        "Rel": "capture",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/067f73ce-62fb-4d76-871d-0bcbb88fbd22/capture"
      },
      {
        "Method": "PUT",
        "Rel": "void",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/067f73ce-62fb-4d76-871d-0bcbb88fbd22/void"
      }
    ]
  }
}
```

```shell
--header "Content-Type: application/json"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
  "MerchantOrderId": "2017051113",
  "Customer": {
    "Name": "Nome do Cliente"
  },
  "Payment": {
    "ServiceTaxAmount": 0,
    "Installments": 1,
    "Interest": "ByMerchant",
    "Capture": false,
    "Authenticate": false,
    "Recurrent": false,
    "CreditCard": {
      "CardNumber": "455187******0181",
      "Holder": "Nome do Portador",
      "ExpirationDate": "12/2021",
      "SaveCard": false,
      "Brand": "Visa"
    },
    "ProofOfSale": "5646418",
    "AcquirerTransactionId": "0511045646418",
    "AuthorizationCode": "100024",
    "PaymentId": "067f73ce-62fb-4d76-871d-0bcbb88fbd22",
    "Type": "CreditCard",
    "Amount": 10000,
    "ReceivedDate": "2017-05-11 16:56:46",
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Simulado",
    "ReasonCode": 0,
    "ReasonMessage": "Successful",
    "Status": 1,
    "ProviderReturnCode": "4",
    "ProviderReturnMessage": "Operation Successful",
    "RecurrentPayment": {
      "RecurrentPaymentId": "808d3631-47ca-43b4-97f5-bd29ab06c271",
      "ReasonCode": 0,
      "ReasonMessage": "Successful",
      "NextRecurrency": "2017-06-11",
      "EndDate": "2019-12-31",
      "Interval": "Monthly",
      "Link": {
        "Method": "GET",
        "Rel": "recurrentPayment",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/RecurrentPayment/808d3631-47ca-43b4-97f5-bd29ab06c271"
      },
      "AuthorizeNow": true
    },
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/067f73ce-62fb-4d76-871d-0bcbb88fbd22"
      },
      {
        "Method": "PUT",
        "Rel": "capture",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/067f73ce-62fb-4d76-871d-0bcbb88fbd22/capture"
      },
      {
        "Method": "PUT",
        "Rel": "void",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/067f73ce-62fb-4d76-871d-0bcbb88fbd22/void"
      }
    ]
  }
}
```

|Propriedade|Descrição|Tipo|Tamanho|Formato|
|-----------|---------|----|-------|-------|
|`RecurrentPaymentId`|ID que representa a recorrência, utilizada para consultas e alterações futuras |Guid |36 |xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx |
|`NextRecurrency`|Data de quando acontecerá a próxima recorrência |Texto |7 |05/2019 (MM/YYYY) |
|`EndDate`|Data do fim da recorrência |Texto |7 |05/2019 (MM/YYYY) |
|`Interval`|Intervalo entre as recorrência. |Texto |10 |<ul><li>Monthly</li><li>Bimonthly </li><li>Quarterly </li><li>SemiAnnual </li><li>Annual</li></ul> |
|`AuthorizeNow`|Booleano para saber se a primeira recorrencia já vai ser Autorizada ou não. |Booleano |--- |true ou false |

## Agendamento de uma Recorrência

Diferentementemente da recorrência anterior, este exemplo não autoriza imediatamente, mas apenas agenda uma autorização futura. Quando esta autorização tiver sucesso, então o agendamento das próximas recorrências será efetivada.

### Requisição

<aside class="request"><span class="method post">POST</span> <span class="endpoint">/v2/sales/</span></aside>

```json
{
   "MerchantOrderId":"2017051114",
   "Customer":{
      "Name":"Nome do Cliente"
   },
   "Payment":{
     "Provider":"Simulado",
     "Type":"CreditCard",
     "Amount":10000,
     "Installments":1,
     "CreditCard":{
         "CardNumber":"4551870000000181",
         "Holder":"Nome do Portador",
         "ExpirationDate":"12/2021",
         "SecurityCode":"123",
         "Brand":"Visa"
     },
     "RecurrentPayment":{
       "AuthorizeNow":"false",
       "StartDate":"2017-12-31",
       "EndDate":"2019-12-31",
       "Interval":"Monthly"
     }
   }
}
```

```shell
curl
--request POST "https://apisandbox.braspag.com.br/v2/sales/"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
   "MerchantOrderId":"2017051114",
   "Customer":{
      "Name":"Nome do Cliente"
   },
   "Payment":{
     "Provider":"Simulado",
     "Type":"CreditCard",
     "Amount":10000,
     "Installments":1,
     "CreditCard":{
         "CardNumber":"4551870000000181",
         "Holder":"Nome do Portador",
         "ExpirationDate":"12/2021",
         "SecurityCode":"123",
         "Brand":"Visa"
     },
     "RecurrentPayment":{
       "AuthorizeNow":"false",
       "StartDate":"2017-12-31",
       "EndDate":"2019-12-31",
       "Interval":"Monthly"
     }
   }
}
--verbose
```

|Propriedade|Tipo|Tamanho|Obrigatório|Descrição|
|-----------|----|-------|-----------|---------|
|`MerchantId`|Guid|36|Sim|Identificador da loja na Braspag|
|`MerchantKey`|Texto|40|Sim|Chave Publica para Autenticação Dupla na Braspag|
|`RequestId`|Guid|36|Não|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT|
|`MerchantOrderId`|Texto|50|Sim|Numero de identificação do Pedido|
|`Customer.Name`|Texto|255|Sim|Nome do comprador|
|`Payment.Provider`|Texto|15|Sim|Nome da provedora de Meio de Pagamento|
|`Payment.Type`|Texto|100|Sim|Tipo do Meio de Pagamento|
|`Payment.Amount`|Número|15|Sim|Valor do Pedido (ser enviado em centavos)|
|`Payment.Installments`|Número|2|Sim|Número de Parcelas|
|`Payment.RecurrentPayment.StartDate`|Texto |10 |Não|Data para início da recorrência|
|`Payment.RecurrentPayment.EndDate`|Texto |10 |Não|Data para termino da recorrência|
|`Payment.RecurrentPayment.Interval`|Texto |10 |Não|Intervalo da recorrência.<br /><ul><li>Monthly (Default) </li><li>Bimonthly </li><li>Quarterly </li><li>SemiAnnual </li><li>Annual</li></ul> |
|`Payment.RecurrentPayment.AuthorizeNow`|Booleano |--- |Sim|Se true, autoriza no momento da requisição. false para agendamento futuro|
|`CreditCard.CardNumber`|Texto|16|Sim|Número do Cartão do comprador|
|`CreditCard.Holder`|Texto|25|Sim|Nome do Comprador impresso no cartão|
|`CreditCard.ExpirationDate`|Texto|7|Sim|Data de validade impresso no cartão, no formato MM/AAAA|
|`CreditCard.SecurityCode`|Texto|4|Sim|Código de segurança impresso no verso do cartão|
|`CreditCard.Brand`|Texto|10|Sim |Bandeira do cartão|

### Resposta

```json
{
  "MerchantOrderId": "2017051114",
  "Customer": {
    "Name": "Nome do Cliente"
  },
  "Payment": {
    "ServiceTaxAmount": 0,
    "Installments": 1,
    "Interest": "ByMerchant",
    "Capture": false,
    "Authenticate": false,
    "Recurrent": false,
    "CreditCard": {
      "CardNumber": "455187******0181",
      "Holder": "Nome do Portador",
      "ExpirationDate": "12/2021",
      "SaveCard": false,
      "Brand": "Undefined"
    },
    "Type": "CreditCard",
    "Amount": 10000,
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Simulado",
    "Status": 20,
    "RecurrentPayment": {
      "RecurrentPaymentId": "32703035-7dfb-4369-ac53-34c7ff7b84e8",
      "ReasonCode": 0,
      "ReasonMessage": "Successful",
      "NextRecurrency": "2017-12-31",
      "StartDate": "2017-12-31",
      "EndDate": "2019-12-31",
      "Interval": "Monthly",
      "Link": {
        "Method": "GET",
        "Rel": "recurrentPayment",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/RecurrentPayment/32703035-7dfb-4369-ac53-34c7ff7b84e8"
      },
      "AuthorizeNow": false
    }
  }
}
```

```shell
--header "Content-Type: application/json"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
  "MerchantOrderId": "2017051114",
  "Customer": {
    "Name": "Nome do Cliente"
  },
  "Payment": {
    "ServiceTaxAmount": 0,
    "Installments": 1,
    "Interest": "ByMerchant",
    "Capture": false,
    "Authenticate": false,
    "Recurrent": false,
    "CreditCard": {
      "CardNumber": "455187******0181",
      "Holder": "Nome do Portador",
      "ExpirationDate": "12/2021",
      "SaveCard": false,
      "Brand": "Undefined"
    },
    "Type": "CreditCard",
    "Amount": 10000,
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Simulado",
    "Status": 20,
    "RecurrentPayment": {
      "RecurrentPaymentId": "32703035-7dfb-4369-ac53-34c7ff7b84e8",
      "ReasonCode": 0,
      "ReasonMessage": "Successful",
      "NextRecurrency": "2017-12-31",
      "StartDate": "2017-12-31",
      "EndDate": "2019-12-31",
      "Interval": "Monthly",
      "Link": {
        "Method": "GET",
        "Rel": "recurrentPayment",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/RecurrentPayment/32703035-7dfb-4369-ac53-34c7ff7b84e8"
      },
      "AuthorizeNow": false
    }
  }
}
```

|Propriedade|Descrição|Tipo|Tamanho|Formato|
|-----------|---------|----|-------|-------|
|`RecurrentPaymentId`|Campo Identificador da próxima recorrência. |Guid |36 |xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx |
|`NextRecurrency`|Data da próxima recorrência. |Texto |7 |05/2019 (MM/YYYY) |
|`StartDate`|Data do inicio da recorrência. |Texto |7 |05/2019 (MM/YYYY) |
|`EndDate`|Data do fim da recorrência. |Texto |7 |05/2019 (MM/YYYY) |
|`Interval`|Intervalo entre as recorrência. |Texto |10 |<ul><li>Monthly</li><li>Bimonthly </li><li>Quarterly </li><li>SemiAnnual </li><li>Annual</li></ul> |
|`AuthorizeNow`|Booleano para saber se a primeira recorrencia já vai ser Autorizada ou não. |Booleano |--- |true ou false |

## Alterar dados do comprador

Para alterar os dados do comprador em uma recorrência, basta fazer um PUT conforme o exemplo.

### Requisição

<aside class="request"><span class="method put">PUT</span> <span class="endpoint">/v2/RecurrentPayment/{RecurrentPaymentId}/Customer</span></aside>

```json
{  
	"Name":"Outro nome do Comprador",
	"Email":"outrocomprador@braspag.com.br",
	"Birthdate":"1999-12-12",
	"Identity":"0987654321",
	"IdentityType":"CPF",
	"Address":{  
		"Street":"Avenida Brigadeiro Faria Lima",
		"Number":"1500",
		"Complement":"AP 201",
		"ZipCode":"05426200",
		"City":"São Paulo",
		"State":"SP",
		"Country":"BRA",
		"District":"Pinheiros"
	},
	"DeliveryAddress":{  
		"Street":"Avenida Brigadeiro Faria Lima",
		"Number":"1500",
		"Complement":"AP 201",
		"ZipCode":"05426200",
		"City":"São Paulo",
		"State":"SP",
		"Country":"BRA",
		"District":"Pinheiros"
	}
}
```

```shell
curl
--request PUT "https://apisandbox.braspag.com.br/v2/RecurrentPayment/{RecurrentPaymentId}/Customer"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{  
	"Name":"Outro nome do Comprador",
	"Email":"outrocomprador@braspag.com.br",
	"Birthdate":"1999-12-12",
	"Identity":"0987654321",
	"IdentityType":"CPF",
	"Address":{  
		"Street":"Avenida Brigadeiro Faria Lima",
		"Number":"1500",
		"Complement":"AP 201",
		"ZipCode":"05426200",
		"City":"São Paulo",
		"State":"SP",
		"Country":"BRA",
		"District":"Pinheiros"
	},
	"DeliveryAddress":{  
		"Street":"Avenida Brigadeiro Faria Lima",
		"Number":"1500",
		"Complement":"AP 201",
		"ZipCode":"05426200",
		"City":"São Paulo",
		"State":"SP",
		"Country":"BRA",
		"District":"Pinheiros"
	}
}
--verbose
```

|Propriedade|Descrição|Tipo|Tamanho|Obrigatório|
|-----------|---------|----|-------|-----------|
|`MerchantId`|Identificador da loja na API |Guid |36 |Sim|
|`MerchantKey`|Chave Publica para Autenticação Dupla na API|Texto |40 |Sim|
|`RequestId`|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT | Guid | 36 |Não|
|`RecurrentPaymentId`|Numero de identificação da Recorrência. |Texto |50 |Sim|
|`Customer.Name`|Nome do Comprador. |Texto |255|Sim|
|`Customer.Email`|Email do Comprador. |Texto |255|Não|
|`Customer.Birthdate`|Data de nascimento do Comprador. |Date |10 |Não|
|`Customer.Identity`|Número do RG, CPF ou CNPJ do Cliente. |Texto |14 |Não|
|`Customer.IdentityType`|Texto|255|Não|Tipo de documento de identificação do comprador (CFP/CNPJ)|
|`Customer.Address.Street`|Endereço do Comprador. |Texto |255 |Não|
|`Customer.Address.Number`|Número do endereço do Comprador. |Texto |15 |Não|
|`Customer.Address.Complement`|Complemento do endereço do comprador|Texto |50 |Não|
|`Customer.Address.ZipCode`|CEP do endereço do Comprador. |Texto |9 |Não|
|`Customer.Address.City`|Cidade do endereço do Comprador. |Texto |50 |Não|
|`Customer.Address.State`|Estado do endereço do Comprador. |Texto |2 |Não|
|`Customer.Address.Country`|Pais do endereço do Comprador. |Texto |35 |Não|
|`Customer.Address.District`|Bairro do Comprador. |Texto |50 |Não|
|`Customer.DeliveryAddress.Street`|Endereço do Comprador. |Texto |255 |Não|
|`Customer.DeliveryAddress.Number`|Número do endereço do Comprador. |Texto |15 |Não|
|`Customer.DeliveryAddress.Complement`|Complemento do endereço do Comprador. |Texto |50 |Não|
|`Customer.DeliveryAddress.ZipCode`|CEP do endereço do Comprador. |Texto |9 |Não|
|`Customer.DeliveryAddress.City`|Cidade do endereço do Comprador. |Texto |50 |Não|
|`Customer.DeliveryAddress.State`|Estado do endereço do Comprador. |Texto |2 |Não|
|`Customer.DeliveryAddress.Country`|Pais do endereço do Comprador. |Texto |35 |Não|
|`Customer.DeliveryAddress.District`|Bairro do Comprador. |Texto |50 |Não|

### Resposta

```shell
HTTP Status 200
```

Veja o Anexo [HTTP Status Code](#http-status-code) para a lista com todos os códigos de status HTTP possivelmente retornados pela API.

## Alterar a data final da Recorrência

Para alterar a data final da Recorrência, basta fazer um PUT conforme o exemplo.

### Requisição

<aside class="request"><span class="method put">PUT</span> <span class="endpoint">/v2/RecurrentPayment/{RecurrentPaymentId}/EndDate</span></aside>

```json
"2021-01-09"
```

```shell
curl
--request PUT "https://apisandbox.braspag.com.br/v2/RecurrentPayment/{RecurrentPaymentId}/EndDate"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
"2021-01-09"
--verbose
```

|Propriedade|Descrição|Tipo|Tamanho|Obrigatório|
|-----------|---------|----|-------|-----------|
|`MerchantId`|Identificador da loja na API |Guid |36 |Sim|
|`MerchantKey`|Chave Publica para Autenticação Dupla na API|Texto |40 |Sim|
|`RequestId`|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT | Guid | 36 |Não|
|`RecurrentPaymentId`|Numero de identificação da Recorrência. |Texto |50 |Sim|
|`EndDate`|Data para termino da recorrência|Texto |10 |Sim|

### Resposta

```shell
HTTP Status 200
```

Veja o Anexo [HTTP Status Code](#http-status-code) para a lista com todos os códigos de status HTTP possivelmente retornados pela API.

## Alterar o intevalo da Recorrência

Para alterar o Intervalo da Recorrência, basta fazer um PUT conforme o exemplo.

### Requisição

<aside class="request"><span class="method put">PUT</span> <span class="endpoint">/v2/RecurrentPayment/{RecurrentPaymentId}/Interval</span></aside>

```json
6
```

```shell
curl
--request PUT "https://apisandbox.braspag.com.br/v2/RecurrentPayment/{RecurrentPaymentId}/Interval"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
6
--verbose
```

|Propriedade|Descrição|Tipo|Tamanho|Obrigatório|
|-----------|---------|----|-------|-----------|
|`MerchantId`|Identificador da loja na API |Guid |36 |Sim|
|`MerchantKey`|Chave Publica para Autenticação Dupla na API|Texto |40 |Sim|
|`RequestId`|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT | Guid | 36 |Não|
|`RecurrentPaymentId`|Numero de identificação da Recorrência. |Texto |50 |Sim|
|`Interval`|Intervalo da recorrência. <ul><li>Monthly</li><li>Bimonthly </li><li>Quarterly </li><li>SemiAnnual </li><li>Annual</li></ul>|Número |2 |Sim|

### Resposta

```shell
HTTP Status 200
```

Veja o Anexo [HTTP Status Code](#http-status-code) para a lista com todos os códigos de status HTTP possivelmente retornados pela API.

## Alterar o dia da Recorrência

Para modificar o dia da recorrência, basta fazer um PUT conforme o exemplo.

<aside class="notice"><strong>Regra:</strong> Se o novo dia informado for depois do dia atual, iremos atualizar o dia da recorrência com efeito na próxima recorrência Ex.: Hoje é dia 5, e a próxima recorrência é dia 25/05. Quando eu atualizar para o dia 10, a data da próxima recorrência será dia10/05. Se o novo dia informado for antes do dia atual, iremos atualizar o dia da recorrência, porém este só terá efeito depois que a próxima recorrência for executada com sucesso. Ex.: Hoje é dia 5, e a próxima recorrência é dia 25/05. Quando eu atualizar para o dia 3, a data da próxima recorrência permanecerá dia 25/05, e após ela ser executada, a próxima será agendada para o dia 03/06. Se o novo dia informado for antes do dia atual, mas a próxima recorrência for em outro mês, iremos atualizar o dia da recorrência com efeito na próxima recorrência. Ex.: Hoje é dia 5, e a próxima recorrência é dia 25/09. Quando eu atualizar para o dia 3, a data da próxima recorrência será 03/09</aside>

### Requisição

<aside class="request"><span class="method put">PUT</span> <span class="endpoint">/v2/RecurrentPayment/{RecurrentPaymentId}/RecurrencyDay</span></aside>

```json
16
```

```shell
curl
--request PUT "https://apisandbox.braspag.com.br/v2/RecurrentPayment/{RecurrentPaymentId}/RecurrencyDay"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
16
--verbose
```

|Propriedade|Descrição|Tipo|Tamanho|Obrigatório|
|-----------|---------|----|-------|-----------|
|`MerchantId`|Identificador da loja na API |Guid |36 |Sim|
|`MerchantKey`|Chave Publica para Autenticação Dupla na API|Texto |40 |Sim|
|`RequestId`|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT | Guid | 36 |Não|
|`RecurrentPaymentId`|Numero de identificação da Recorrência. |Texto |50 |Sim|
|`RecurrencyDay`|Dia da Recorrência|Número |2 |Sim|

### Resposta

```shell
HTTP Status 200
```

Veja o Anexo [HTTP Status Code](#http-status-code) para a lista com todos os códigos de status HTTP possivelmente retornados pela API.

## Alterar o valor da transação da Recorrência

Para modificar o valor da transação da recorrência, basta fazer um PUT conforme o exemplo.

### Requsição

<aside class="request"><span class="method put">PUT</span> <span class="endpoint">/v2/RecurrentPayment/{RecurrentPaymentId}/Amount</span></aside>

```json
156
```

```shell
curl
--request PUT "https://apisandbox.braspag.com.br/v2/RecurrentPayment/{RecurrentPaymentId}/Amount"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
156
--verbose
```

|Propriedade|Descrição|Tipo|Tamanho|Obrigatório|
|-----------|---------|----|-------|-----------|
|`MerchantId`|Identificador da loja na API |Guid |36 |Sim|
|`MerchantKey`|Chave Publica para Autenticação Dupla na API|Texto |40 |Sim|
|`RequestId`|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT | Guid | 36 |Não|
|`RecurrentPaymentId`|Numero de identificação da Recorrência. |Texto |50 |Sim|
|`Payment.Amount`|Valor do Pedido em centavos: 156 equivale a R$ 1,56|Número|15|Sim|

<aside class="warning">Essa alteração só afeta a data de pagamento da próxima recorrência.</aside>

### Resposta

```shell
HTTP Status 200
```

Veja o Anexo [HTTP Status Code](#http-status-code) para a lista com todos os códigos de status HTTP possivelmente retornados pela API.

## Alterar a data do próximo Pagamento

Para alterar a data do próximo Pagamento, basta fazer um PUT conforme o exemplo. Esta operação modifica somente a data do próximo pagamento, ou seja, a recorrências futuras a esta permanecerão com as carcaterísticas originais.

### Requisição

<aside class="request"><span class="method put">PUT</span> <span class="endpoint">/v2/RecurrentPayment/{RecurrentPaymentId}/NextPaymentDate</span></aside>

```json
"2017-06-15"
```

```shell
curl
--request PUT "https://apisandbox.braspag.com.br/v2/RecurrentPayment/{RecurrentPaymentId}/NextPaymentDate"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
"2016-06-15"
--verbose
```

|Propriedade|Descrição|Tipo|Tamanho|Obrigatório|
|-----------|---------|----|-------|-----------|
|`MerchantId`|Identificador da loja na API |Guid |36 |Sim|
|`MerchantKey`|Chave Publica para Autenticação Dupla na API|Texto |40 |Sim|
|`RequestId`|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT | Guid | 36 |Não|
|`RecurrentPaymentId`|Numero de identificação da Recorrência. |Texto |50 |Sim|
|`NextPaymentDate`|Data de pagamento da próxima recorrência|Texto |10 |Sim|

### Resposta

```shell
HTTP Status 200
```

Veja o Anexo [HTTP Status Code](#http-status-code) para a lista com todos os códigos de status HTTP possivelmente retornados pela API.

## Alterar os dados do Pagamento da Recorrência

Para alterar os dados de pagamento da Recorrência, basta fazer um PUT conforme o exemplo.

<aside class="notice"><strong>Atenção:</strong> Essa alteração afeta a todos os dados do nó Payment. Então para manter os dados anteriores você deve informar os campos que não vão sofre alterações com os mesmos valores que já estavam salvos.</aside>

### Requisição

<aside class="request"><span class="method put">PUT</span> <span class="endpoint">/v2/RecurrentPayment/{RecurrentPaymentId}/Payment</span></aside>

```json
{  
   "Type":"CreditCard",
   "Amount":"20000",
   "Installments":3,
   "Country":"USA",
   "Currency":"USD",
   "SoftDescriptor":"Mensagem",
   "Provider":"Simulado",
   "CreditCard":{  
      "Brand":"Master",
      "Holder":"Nome do Portador",
      "CardNumber":"4111111111111111",
      "ExpirationDate":"05/2019"
   }
}
```

```shell
curl
--request PUT "https://apisandbox.braspag.com.br/v2/RecurrentPayment/{RecurrentPaymentId}/Payment"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{  
   "Provider":"Simulado",
   "Type":"CreditCard",
   "Amount":"20000",
   "Installments":3,
   "Country":"USA",
   "Currency":"USD",
   "SoftDescriptor":"Mensagem",
   "CreditCard":{  
      "Brand":"Master",
      "Holder":"Nome do Portador",
      "CardNumber":"4111111111111111",
      "ExpirationDate":"05/2019"
   }
}
--verbose
```

|Propriedade|Descrição|Tipo|Tamanho|Obrigatório|
|-----------|---------|----|-------|-----------|
|`MerchantId`|Identificador da loja na API |Guid |36 |Sim|
|`MerchantKey`|Chave Publica para Autenticação Dupla na API|Texto |40 |Sim|
|`RequestId`|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT | Guid | 36 |Não|
|`RecurrentPaymentId`|Numero de identificação da Recorrência. |Texto |50 |Sim|
|`Payment.Provider`|Nome da provedora de Meio de Pagamento|Texto|15|Sim|
|`Payment.Type`|Tipo do Meio de Pagamento. |Texto |100|Sim|
|`Payment.Amount`|Valor do Pedido (ser enviado em centavos)|Número |15 |Sim|
|`Payment.Installments`|Número de Parcelas|Número |2 |Sim|
|`Payment.SoftDescriptor`|Texto que será impresso na fatura do portador|Texto |13|Não|
|`CreditCard.CardNumber`|Número do Cartão do comprador|Texto |16|Sim|
|`CreditCard.Holder`|Nome do Comprador impresso no cartão|Texto |25|Sim|
|`CreditCard.ExpirationDate`|Data de validade impresso no cartão|Texto |7 |Sim|
|`CreditCard.SecurityCode`|Código de segurança impresso no verso do cartão|Texto |4 |Sim|
|`CreditCard.Brand`|Bandeira do cartão|Texto|10|Sim|

### Resposta

```shell
HTTP Status 200
```

Veja o Anexo [HTTP Status Code](#http-status-code) para a lista com todos os códigos de status HTTP possivelmente retornados pela API.

## Desabilitando um Pedido Recorrente

Para desabilitar um pedido recorrente, basta fazer um PUT conforme o exemplo.

### Requisição

<aside class="request"><span class="method put">PUT</span> <span class="endpoint">/v2/RecurrentPayment/{RecurrentPaymentId}/Deactivate</span></aside>

```shell
curl
--request PUT "https://apisandbox.braspag.com.br/v2/RecurrentPayment/{RecurrentPaymentId}/Deactivate"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
--verbose
```

|Propriedade|Descrição|Tipo|Tamanho|Obrigatório|
|-----------|---------|----|-------|-----------|
|`MerchantId`|Identificador da loja na API |Guid |36 |Sim|
|`MerchantKey`|Chave Publica para Autenticação Dupla na API|Texto |40 |Sim|
|`RequestId`|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT | Guid | 36 |Não|
|`RecurrentPaymentId`|Numero de identificação da Recorrência. |Texto |50 |Sim|

### Resposta

```shell
HTTP Status 200
```

Veja o Anexo [HTTP Status Code](#http-status-code) para a lista com todos os códigos de status HTTP possivelmente retornados pela API.

## Reabilitando um Pedido Recorrente

Para Reabilitar um pedido recorrente, basta fazer um Put conforme o exemplo.

### Requisição

<aside class="request"><span class="method put">PUT</span> <span class="endpoint">/v2/RecurrentPayment/{RecurrentPaymentId}/Reactivate</span></aside>

```shell
curl
--request PUT "https://apisandbox.braspag.com.br/v2/RecurrentPayment/{RecurrentPaymentId}/Reactivate"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
--verbose
```

|Propriedade|Descrição|Tipo|Tamanho|Obrigatório|
|-----------|---------|----|-------|-----------|
|`MerchantId`|Identificador da loja na API |Guid |36 |Sim|
|`MerchantKey`|Chave Publica para Autenticação Dupla na API|Texto |40 |Sim|
|`RequestId`|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT | Guid | 36 |Não|
|`RecurrentPaymentId`|Numero de identificação da Recorrência. |Texto |50 |Sim|

### Resposta

```shell
HTTP Status 200
```

Veja o Anexo [HTTP Status Code](#http-status-code) para a lista com todos os códigos de status HTTP possivelmente retornados pela API.

# Consultas

## Consultando uma transação via PaymentID

Para consultar uma transação de cartão de crédito, é necessário fazer um GET para o recurso Payment conforme o exemplo.

### Requisição

<aside class="request"><span class="method get">GET</span> <span class="endpoint">/v2/sales/{PaymentId}</span></aside>

```shell
curl
--request GET "https://apiquerysandbox.braspag.com.br/v2/sales/{PaymentId}"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
--verbose
```

|Propriedade|Descrição|Tipo|Tamanho|Obrigatório|
|-----------|---------|----|-------|-----------|
|`MerchantId`|Identificador da loja na API |Guid |36 |Sim|
|`MerchantKey`|Chave Publica para Autenticação Dupla na API|Texto |40 |Sim|
|`RequestId`|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT | Guid | 36 |Não|
|`PaymentId`|Numero de identificação do Pagamento. |Texto |36 |Sim|

### Resposta

```json
{
  "MerchantOrderId": "2017051001",
  "Customer": {
    "Name": "Nome do Cliente",
    "Address": {}
  },
  "Payment": {
    "ServiceTaxAmount": 0,
    "Installments": 1,
    "Interest": "ByMerchant",
    "Capture": false,
    "Authenticate": false,
    "CreditCard": {
      "CardNumber": "455187******0181",
      "Holder": "Nome do Portador",
      "ExpirationDate": "12/2021",
      "Brand": "Visa"
    },
    "ProofOfSale": "2539492",
    "AcquirerTransactionId": "0510042539492",
    "AuthorizationCode": "759497",
    "VelocityAnalysis": {
      "Id": "f8078b32-be17-4c35-b164-ad74c3cd0725",
      "ResultMessage": "Accept",
      "Score": 0
    },
    "PaymentId": "f8078b32-be17-4c35-b164-ad74c3cd0725",
    "Type": "CreditCard",
    "Amount": 10000,
    "ReceivedDate": "2017-05-10 16:25:38",
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Simulado",
    "ReasonCode": 0,
    "Status": 1,
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/f8078b32-be17-4c35-b164-ad74c3cd0725"
      },
      {
        "Method": "PUT",
        "Rel": "capture",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/f8078b32-be17-4c35-b164-ad74c3cd0725/capture"
      },
      {
        "Method": "PUT",
        "Rel": "void",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/f8078b32-be17-4c35-b164-ad74c3cd0725/void"
      }
    ]
  }
}
```

```shell
--header "Content-Type: application/json"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
  "MerchantOrderId": "2017051001",
  "Customer": {
    "Name": "Nome do Cliente",
    "Address": {}
  },
  "Payment": {
    "ServiceTaxAmount": 0,
    "Installments": 1,
    "Interest": "ByMerchant",
    "Capture": false,
    "Authenticate": false,
    "CreditCard": {
      "CardNumber": "455187******0181",
      "Holder": "Nome do Portador",
      "ExpirationDate": "12/2021",
      "Brand": "Visa"
    },
    "ProofOfSale": "2539492",
    "AcquirerTransactionId": "0510042539492",
    "AuthorizationCode": "759497",
    "PaymentId": "f8078b32-be17-4c35-b164-ad74c3cd0725",
    "Type": "CreditCard",
    "Amount": 10000,
    "ReceivedDate": "2017-05-10 16:25:38",
    "Currency": "BRL",
    "Country": "BRA",
    "Provider": "Simulado",
    "ReasonCode": 0,
    "Status": 1,
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/f8078b32-be17-4c35-b164-ad74c3cd0725"
      },
      {
        "Method": "PUT",
        "Rel": "capture",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/f8078b32-be17-4c35-b164-ad74c3cd0725/capture"
      },
      {
        "Method": "PUT",
        "Rel": "void",
        "Href": "https://apihomolog.braspag.com.br/v2/sales/f8078b32-be17-4c35-b164-ad74c3cd0725/void"
      }
    ]
  }
}
```

|Propriedade|Descrição|Tipo|Tamanho|Formato|
|-----------|---------|----|-------|-------|
|`MerchantOrderId`|Numero de identificação do Pedido|Texto|50|Texto alfanumérico|
|`Customer.Name`|Nome do comprador|Texto|255|Texto alfanumérico|
|`Customer.Identity`|Número do RG, CPF ou CNPJ do Cliente|Texto |14 |Texto alfanumérico|
|`Customer.IdentityType`|Tipo de documento de identificação do comprador (CPF ou CNPJ)|Texto|255|CPF ou CNPJ|
|`Customer.Email`|Email do comprador|Texto|255|Texto alfanumérico|
|`Customer.Birthdate`|Data de nascimento do Comprador|Date|10|formato AAAA-MM-DD|
|`Customer.Address.Street`|Endereço de contato do comprador|Texto|255|Texto alfanumérico|
|`Customer.Address.Number`|Número endereço de contato do comprador|Texto|15|Texto alfanumérico|
|`Customer.Address.Complement`|Complemento do endereço de contato do Comprador|Texto|50|Texto alfanumérico|
|`Customer.Address.ZipCode`|CEP do endereço de contato do comprador|Texto|9|Texto alfanumérico|
|`Customer.Address.City`|Cidade do endereço de contato do comprador|Texto|50|Texto alfanumérico|
|`Customer.Address.State`|Estado do endereço de contato do comprador|Texto|2|Texto alfanumérico|
|`Customer.Address.Country`|Pais do endereço de contato do comprador|Texto|35|Texto alfanumérico|
|`Customer.Address.District`|Bairro do Comprador|Texto |50 |Texto alfanumérico|
|`Customer.DeliveryAddress.Street`|Endereço do comprador|Texto|255|Texto alfanumérico|
|`Customer.DeliveryAddress.Number`|Número do endereço de entrega do pedido|Texto|15|Texto alfanumérico|
|`Customer.DeliveryAddress.Complement`|Complemento do endereço de entrega do pedido|Texto|50|Texto alfanumérico|
|`Customer.DeliveryAddress.ZipCode`|CEP do endereço de entrega do pedido|Texto|9|Texto alfanumérico|
|`Customer.DeliveryAddress.City`|Cidade do endereço de entrega do pedido|Texto|50|Texto alfanumérico|
|`Customer.DeliveryAddress.State`|Estado do endereço de entrega do pedido|Texto|2|Texto alfanumérico|
|`Customer.DeliveryAddress.Country`|Pais do endereço de entrega do pedido|Texto|35|Texto alfanumérico|
|`Customer.DeliveryAddress.District`|Bairro do Comprador. |Texto |50 |Texto alfanumérico|
|`Payment.Provider`|Nome da provedora de Meio de Pagamento|Texto|15| Consulta os provedores disponíveis nos anexos|
|`Payment.Type`|Tipo do Meio de Pagamento|Texto|100|Ex. CreditCard|
|`Payment.Amount`|Valor do Pedido (ser enviado em centavos)|Número|15|10000|
|`Payment.ServiceTaxAmount`|Montante do valor da autorização que deve ser destinado à taxa de serviço. Obs.: Esse valor não é adicionado ao valor da autorização|Número|15|10000|
|`Payment.Currency`|Moeda na qual o pagamento será feito|Texto|3|BRL / USD / MXN / COP / CLP / ARS / PEN / EUR / PYN / UYU / VEB / VEF / GBP|
|`Payment.Country`|País na qual o pagamento será feito|Texto|3|BRA|
|`Payment.Installments`|Número de Parcelas|Número|2|6|
|`Payment.Interest`|Tipo de parcelamento|Texto|10|Loja (ByMerchant) ou Cartão (ByIssuer)|
|`Payment.Capture`|Booleano que indica se a autorização deve ser com captura automática (true) ou não (false). Deverá verificar junto à adquirente a disponibilidade desta funcionalidade|Booleano|--- (Default false)|Booleano|
|`Payment.Authenticate`|Booleano que indica se a transação deve ser autenticada (true) ou não (false). Deverá verificar junto à adquirente a disponibilidade desta funcionalidade|Booleano|--- (Default false)|Booleano|
|`Payment.Recurrent`|Booleano que indica se a transação é do tipo recorrente (true) ou não (false). Este com valor true não originará uma nova Recorrência, apenas permitirá a realização de uma transação sem a necessidade de envio do CVV. Somente para transações Cielo. Authenticate deve ser false quando Recurrent é true|Booleano|--- (Default false)|Booleano|
|`Payment.SoftDescriptor`|Texto que será impresso na fatura do portador|Texto|13|Texto alfanumérico|
|`Payment.ExtraDataCollection.Name`|Nome do campo que será gravado o Dado Extra|Texto|50|Texto alfanumérico|
|`Payment.ExtraDataCollection.Value`|Valor do campo que será gravado o Dado Extra|Texto|1024|Texto alfanumérico|
|`Payment.AcquirerTransactionId`|Id da transação no provedor de meio de pagamento|Texto|40|Texto alfanumérico|
|`Payment.ProofOfSale`|Número do Comprovante de Venda|Texto|20|Texto alfanumérico|
|`Payment.AuthorizationCode`|Código de autorização|Texto|300|Texto alfanumérico|
|`Payment.PaymentId`|Campo Identificador do Pedido|Guid|36|xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx|
|`Payment.ReceivedDate`|Data em que a transação foi recebida pela Brapag|Texto|19|AAAA-MM-DD HH:mm:SS|
|`Payment.ReasonCode`|Código de retorno da Adquirência|Texto|32|Texto alfanumérico|
|`Payment.ReasonMessage`|Mensagem de retorno da Adquirência|Texto|512|Texto alfanumérico|
|`Payment.Status`|Status da Transação|Byte|2|1|
|`Payment.ProviderReturnCode`|Código retornado pelo provedor do meio de pagamento (adquirente e bancos)|Texto|32|57|
|`Payment.ProviderReturnMessage`|Mensagem retornada pelo provedor do meio de pagamento (adquirente e bancos)|Texto|512|Transação Aprovada|
|`CreditCard.CardNumber`|Número do Cartão do comprador|Texto|16|
|`CreditCard.Holder`|Nome do portador impresso no cartão|Texto|25|
|`CreditCard.ExpirationDate`|Data de validade impresso no cartão|Texto|7|
|`CreditCard.SecurityCode`|Código de segurança impresso no verso do cartão|Texto|4|
|`CreditCard.Brand`|Bandeira do cartão|Texto|10 |
|`CreditCard.SaveCard`|Booleano que identifica se o cartão será salvo para gerar o token (CardToken)|Booleano|--- (Default false)|


## Consultando uma venda pelo identificador da loja

Não é possível consultar diretamente uma pagamento pelo identificador enviado pela loja (MerchantOrderId), mas é possível obter todos os PaymentIds associados ao identificador.

Para consultar uma venda pelo identificador da loja, é necessário fazer um GET para o recuso sales conforme o exemplo.

### Requisição

<aside class="request"><span class="method get">GET</span> <span class="endpoint">/v2/sales?merchantOrderId={merchantOrderId}</span></aside>

```shell
curls
--request GET "https://apiquerysandbox.braspag.com.brv2/sales?merchantOrderId={merchantOrderId}"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
--verbose
```

|Propriedade|Descrição|Tipo|Tamanho|Obrigatório|
|-----------|---------|----|-------|-----------|
|`MerchantId`|Identificador da loja na API |Guid |36 |Sim|
|`MerchantKey`|Chave Publica para Autenticação Dupla na API|Texto |40 |Sim|
|`RequestId`|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT | Guid | 36 |Não|
|`MerchantOrderId`|Campo Identificador do Pedido na Loja. |Texto |36 |Sim|

### Resposta

```json
{
    "Payment": [
        {
            "PaymentId": "5fb4d606-bb63-4423-a683-c966e15399e8",
            "ReceveidDate": "2015-04-06T10:13:39.42"
        },
        {
            "PaymentId": "6c1d45c3-a95f-49c1-a626-1e9373feecc2",
            "ReceveidDate": "2014-12-19T20:23:28.847"
        }
    ]
}
```

```shell
--header "Content-Type: application/json"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
    "ReasonCode": 0,
    "ReasonMessage": "Successful",
    "Payments": [
        {
            "PaymentId": "5fb4d606-bb63-4423-a683-c966e15399e8",
            "ReceveidDate": "2015-04-06T10:13:39.42"
        },
        {
            "PaymentId": "6c1d45c3-a95f-49c1-a626-1e9373feecc2",
            "ReceveidDate": "2014-12-19T20:23:28.847"
        }
    ]
}
```

|Propriedade|Descrição|Tipo|Tamanho|Formato|
|-----------|---------|----|-------|-------|
|`PaymentId`|Campo Identificador do Pedido|Guid|36|xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx|

## Consultando um pedido Recorrente

Para consultar um pedido de Recorrência, é necessário fazer um GET conforme o exemplo.

### Requisição

<aside class="request"><span class="method get">GET</span> <span class="endpoint">/v2/RecurrentPayment/{RecurrentPaymentId}</span></aside>

```shell
curl
--request GET "https://apiquerysandbox.braspag.com.br/v2/RecurrentPayment/{RecurrentPaymentId}"
--header "Content-Type: application/json"
--header "MerchantId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--header "MerchantKey: 0123456789012345678901234567890123456789"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
--verbose
```

|Propriedade|Descrição|Tipo|Tamanho|Obrigatório|
|-----------|---------|----|-------|-----------|
|`MerchantId`|Identificador da loja na API |Guid |36 |Sim|
|`MerchantKey`|Chave Publica para Autenticação Dupla na API|Texto |40 |Sim|
|`RequestId`|Identificador do Request definido pela loja, utilizado quando o lojista usa diferentes servidores para cada GET/POST/PUT | Guid | 36 |Não|
|`RecurrentPaymentId`|Campo Identificador da Recorrência. |Texto |36 |Sim|

### Resposta

```json
{
  "Customer": {
    "Name": "Nome do Cliente"
  },
  "RecurrentPayment": {
    "Installments": 1,
    "RecurrentPaymentId": "f5a83c14-0254-4e73-bdd3-9afba1007266",
    "NextRecurrency": "2017-06-11",
    "StartDate": "2017-05-11",
    "EndDate": "2019-12-31",
    "Interval": "Monthly",
    "Amount": 10000,
    "Country": "BRA",
    "CreateDate": "2017-05-11T00:00:00",
    "Currency": "BRL",
    "CurrentRecurrencyTry": 1,
    "OrderNumber": "2017051120",
    "Provider": "Simulado",
    "RecurrencyDay": 11,
    "SuccessfulRecurrences": 1,
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/RecurrentPayment/f5a83c14-0254-4e73-bdd3-9afba1007266"
      }
    ],
    "RecurrentTransactions": [
      {
        "PaymentNumber": 0,
        "RecurrentPaymentId": "f5a83c14-0254-4e73-bdd3-9afba1007266",
        "TransactionId": "cd694ffb-c0c4-47db-9390-737df70a2012",
        "TryNumber": 1,
        "Links": [
          {
            "Method": "GET",
            "Rel": "self",
            "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/cd694ffb-c0c4-47db-9390-737df70a2012"
          }
        ]
      }
    ],
    "Status": 1
  }
}
```

```shell
--header "Content-Type: application/json"
--header "RequestId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
--data-binary
{
  "Customer": {
    "Name": "Nome do Cliente"
  },
  "RecurrentPayment": {
    "Installments": 1,
    "RecurrentPaymentId": "f5a83c14-0254-4e73-bdd3-9afba1007266",
    "NextRecurrency": "2017-06-11",
    "StartDate": "2017-05-11",
    "EndDate": "2019-12-31",
    "Interval": "Monthly",
    "Amount": 10000,
    "Country": "BRA",
    "CreateDate": "2017-05-11T00:00:00",
    "Currency": "BRL",
    "CurrentRecurrencyTry": 1,
    "OrderNumber": "2017051120",
    "Provider": "Simulado",
    "RecurrencyDay": 11,
    "SuccessfulRecurrences": 1,
    "Links": [
      {
        "Method": "GET",
        "Rel": "self",
        "Href": "https://apiqueryhomolog.braspag.com.br/v2/RecurrentPayment/f5a83c14-0254-4e73-bdd3-9afba1007266"
      }
    ],
    "RecurrentTransactions": [
      {
        "PaymentNumber": 0,
        "RecurrentPaymentId": "f5a83c14-0254-4e73-bdd3-9afba1007266",
        "TransactionId": "cd694ffb-c0c4-47db-9390-737df70a2012",
        "TryNumber": 1,
        "Links": [
          {
            "Method": "GET",
            "Rel": "self",
            "Href": "https://apiqueryhomolog.braspag.com.br/v2/sales/cd694ffb-c0c4-47db-9390-737df70a2012"
          }
        ]
      }
    ],
    "Status": 1
  }
}
```

|Propriedade|Descrição|Tipo|Tamanho|Formato|
|-----------|---------|----|-------|-------|
|`RecurrentPaymentId`|Campo Identificador da próxima recorrência. |Guid |36 |xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx |
|`NextRecurrency`|Data da próxima recorrência. |Texto |7 |05/2019 (MM/YYYY) |
|`StartDate`|Data do inicio da recorrência. |Texto |7 |05/2019 (MM/YYYY) |
|`EndDate`|Data do fim da recorrência. |Texto |7 |05/2019 (MM/YYYY) |
|`Interval`|Intervalo entre as recorrência. |Texto |10 |<ul><li>Monthly</li><li>Bimonthly </li><li>Quarterly </li><li>SemiAnnual </li><li>Annual</li></ul> |
|`CurrentRecurrencyTry`|Indica o número de tentativa da recorrência atual |Número|1|1|
|`OrderNumber`|Identificado do Pedido na loja |Texto|50 |2017051101|
|`Status`|Status do pedido recorrente |Número|1 |<UL><LI>1 - Ativo</LI><LI>3 - Inativo</LI></UL> |
|`RecurrencyDay`|O dia da recorrência |Número|2 |22 |
|`SuccessfulRecurrences`|Quantidade de recorrência realizada com sucesso|Número|2 |5|
|`RecurrentTransactions.RecurrentPaymentId`|Id da Recorrência|Guid |36 |xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx |
|`RecurrentTransactions.TransactionId`|Payment ID da transação gerada na recorrência|Guid |36 |xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx |
|`RecurrentTransactions.PaymentNumber`|Número da Recorrência. A primeira é zero |Número|2 |3 |
|`RecurrentTransactions.TryNumber`|Número da tentativa atual na recorrência específica |Número|2 |1 |

# Post de Notificação

Para receber a notificação de alteração de status deve-se ter configurado no cadastro de sua loja na Braspag, o campo URL Status Pagamento para receber os parametros conforme o exemplo ao lado.

Resposta esperada da Loja: HTTP Status Code 200 OK

Caso não seja retornado o HTTP Status Code 200 OK será tentado mais duas vezes enviar o Post de Notificação.

```json
{
   "PaymentId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
   "ChangeType": "1"
}
```
```json
{
   "RecurrentPaymentId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
   "PaymentId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
   "ChangeType": "2"
}
```

|ChangeType|Descrição|
|----------|---------|
|1|Mudança de status do pagamento|
|2|Recorrência criada|
|3|Mudança de status do Antifraude|
|4|Mudança de status do pagamento recorrente (Ex. desativação automática)|
|5|Estorno negado (aplicável para Rede)|

# Anexos

## Lista de Providers

### Providers para Crédito

* Simulado
* Cielo
    * Visa
    * Master
    * Amex
    * Elo
    * Aura
    * Jcb
    * Diners
    * Discover
* Cielo30 (Cielo 3.0)
    * Visa
    * Master
    * Amex
    * Elo
    * Aura
    * Jcb
    * Diners
    * Discover	
* Redecard (Komerci)
    * Visa
    * Master
    * Hipercard
    * Hiper
    * Diners
* Rede (e-Rede)
    * Visa
    * Master
    * Hipercard
    * Hiper
    * Diners
* RedeSitef
    * Visa
    * Master
    * Hipercard
    * Diners
* CieloSitef
    * Visa
    * Master
    * Amex
    * Elo
    * Aura
    * Jcb
    * Diners
    * Discover
* SantanderSitef
    * Visa
    * Master

### Providers pra Débito

* Cielo

### Providers para Boleto

* Simulado
* Bradesco
* BancoDoBrasil
* CitiBank
* Itau
* Brb
* Caixa
* Santander
* HSBC

### Providers para Transferência Eletronica (Débito Online)

* Bradesco
* BancoDoBrasil
* SafetyPay
* Itau

## ReasonCode e ReasonMessage

|Reason Code|Reason Message|
|-----------|--------------|
|0|	Successful|
|1|	AffiliationNotFound|
|2|	IssuficientFunds|
|3|	CouldNotGetCreditCard|
|4|	ConnectionWithAcquirerFailed|
|5|	InvalidTransactionType|
|6|	InvalidPaymentPlan|
|7|	Denied|
|8|	Scheduled|
|9|	Waiting|
|10|	Authenticated|
|11|	NotAuthenticated|
|12|	ProblemsWithCreditCard|
|13|	CardCanceled|
|14|	BlockedCreditCard|
|15|	CardExpired|
|16|	AbortedByFraud|
|17|	CouldNotAntifraud|
|18|	TryAgain|
|19|	InvalidAmount|
|20|	ProblemsWithIssuer|
|21|	InvalidCardNumber|
|22|	TimeOut|
|23|	CartaoProtegidoIsNotEnabled|
|24|	PaymentMethodIsNotEnabled|
|98|	InvalidRequest|
|99|	InternalError|

## Lista de Status

Status retornados pela API

|Código|Status do Pagamento|Meio de pagamento|Descrição|
|------|-------------------|-----------------|---------|
|0|NotFinished|Todos|Falha ao processar o pagamento|
|1|Authorized|Todos|Meio de pagamento apto a ser capturado ou pago(Boleto|
|2|PaymentConfirmed|Todos|Pagamento confirmado e finalizado|
|3|Denied|Cartão de Crédito e Débito / Transferência eletrônica|
|10|Voided|Todos|Pagamento cancelado|
|11|Refunded|Cartão de crédito e Débito|Pagamento Cancelado/Estornado|
|12|Pending|Cartão de Crédito e Débito / Transferência eletrônica |Esperando retorno da instituição financeira|
|13|Aborted|Todos|Pagamento cancelado por falha no processamento|
|20|Scheduled|Cartão de crédito|Recorrência agendada|

## Status da Recorrência

|Código|Descrição|
|------|---------|
|1|	Active|
|2|	Finished|
|3|	DisabledByMerchant|
|4|	DisabledMaxAttempts|
|5|	DisabledExpiredCreditCard|

## HTTP Status Code

|HTTP Status Code|Descrição|
|----------------|---------|
|200|OK|
|400|Bad Request|
|404|Resource Not Found|
|500|Internal Server Error|

## Códigos de Erros da API

Códigos retornados em caso de erro, identificando o motivo do erro e suas respectivas mensagens.

|Código|Mensagem|Descrição|
|------|--------|---------|
|0|Internal error|Dado enviado excede o tamanho do campo|
|100|RequestId is required|Campo enviado está vazio ou invalido|
|101|MerchantId is required|Campo enviado está vazio ou invalido|
|102|Payment Type is required|Campo enviado está vazio ou invalido|
|103|Payment Type can only contain letters|Caracteres especiais não permitidos|
|104|Customer Identity is required|Campo enviado está vazio ou invalido|
|105|Customer Name is required|Campo enviado está vazio ou invalido|
|106|Transaction ID is required|Campo enviado está vazio ou invalido|
|107|OrderId is invalid or does not exists|Campo enviado excede o tamanho ou contem caracteres especiais |
|108|Amount must be greater or equal to zero|Valor da transação deve ser maior que "0"|
|109|Payment Currency is required|Campo enviado está vazio ou invalido|
|110|Invalid Payment Currency|Campo enviado está vazio ou invalido|
|111|Payment Country is required|Campo enviado está vazio ou invalido|
|112|Invalid Payment Country|Campo enviado está vazio ou invalido|
|113|Invalid Payment Code|Campo enviado está vazio ou invalido|
|114|The provided MerchantId is not in correct format|O MerchantId enviado não é um GUID|
|115|The provided MerchantId was not found|O MerchantID não existe ou pertence a outro ambiente (EX: Sandbox)|
|116|The provided MerchantId is blocked|Loja bloqueada, entre em contato com o suporte Braspag|
|117|Credit Card Holder is required|Campo enviado está vazio ou invalido|
|118|Credit Card Number is required|Campo enviado está vazio ou invalido|
|119|At least one Payment is required|Nó "Payment" não enviado|
|120|Request IP not allowed. Check your IP White List|IP bloqueado por questões de segurança|
|121|Customer is required|Nó "Customer" não enviado|
|122|MerchantOrderId is required|Campo enviado está vazio ou invalido|
|123|Installments must be greater or equal to one|Numero de parcelas deve ser superior a 1|
|124|Credit Card is Required|Campo enviado está vazio ou invalido|
|125|Credit Card Expiration Date is required|Campo enviado está vazio ou invalido|
|126|Credit Card Expiration Date is invalid|Campo enviado está vazio ou invalido|
|127|You must provide CreditCard Number|Numero do cartão de crédito é obrigatório|
|128|Card Number length exceeded|Numero do cartão superiro a 16 digitos|
|129|Affiliation not found|Meio de pagamento não vinculado a loja ou Provider invalido|
|130|Could not get Credit Card|---|
|131|MerchantKey is required|Campo enviado está vazio ou invalido|
|132|MerchantKey is invalid|O Merchantkey enviado não é um válido|
|133|Provider is not supported for this Payment Type|Provider enviado não existe|
|134|FingerPrint length exceeded|Dado enviado excede o tamanho do campo|
|135|MerchantDefinedFieldValue length exceeded|Dado enviado excede o tamanho do campo|
|136|ItemDataName length exceeded|Dado enviado excede o tamanho do campo|
|137|ItemDataSKU length exceeded|Dado enviado excede o tamanho do campo|
|138|PassengerDataName length exceeded|Dado enviado excede o tamanho do campo|
|139|PassengerDataStatus length exceeded|Dado enviado excede o tamanho do campo|
|140|PassengerDataEmail length exceeded|Dado enviado excede o tamanho do campo|
|141|PassengerDataPhone length exceeded|Dado enviado excede o tamanho do campo|
|142|TravelDataRoute length exceeded|Dado enviado excede o tamanho do campo|
|143|TravelDataJourneyType length exceeded|Dado enviado excede o tamanho do campo|
|144|TravelLegDataDestination length exceeded|Dado enviado excede o tamanho do campo|
|145|TravelLegDataOrigin length exceeded|Dado enviado excede o tamanho do campo|
|146|SecurityCode length exceeded|Dado enviado excede o tamanho do campo|
|147|Address Street length exceeded|Dado enviado excede o tamanho do campo|
|148|Address Number length exceeded|Dado enviado excede o tamanho do campo|
|149|Address Complement length exceeded|Dado enviado excede o tamanho do campo|
|150|Address ZipCode length exceeded|Dado enviado excede o tamanho do campo|
|151|Address City length exceeded|Dado enviado excede o tamanho do campo|
|152|Address State length exceeded|Dado enviado excede o tamanho do campo|
|153|Address Country length exceeded|Dado enviado excede o tamanho do campo|
|154|Address District length exceeded|Dado enviado excede o tamanho do campo|
|155|Customer Name length exceeded|Dado enviado excede o tamanho do campo|
|156|Customer Identity length exceeded|Dado enviado excede o tamanho do campo|
|157|Customer IdentityType length exceeded|Dado enviado excede o tamanho do campo|
|158|Customer Email length exceeded|Dado enviado excede o tamanho do campo|
|159|ExtraData Name length exceeded|Dado enviado excede o tamanho do campo|
|160|ExtraData Value length exceeded|Dado enviado excede o tamanho do campo|
|161|Boleto Instructions length exceeded|Dado enviado excede o tamanho do campo|
|162|Boleto Demostrative length exceeded|Dado enviado excede o tamanho do campo|
|163|Return Url is required|URL de retorno não é valida - Não é aceito paginação ou extenções (EX .PHP) na URL de retorno|
|166|AuthorizeNow is required|---|
|167|Antifraud not configured|Antifraude não vinculado ao cadastro do lojista|
|168|Recurrent Payment not found|Recorrencia não encontrada|
|169|Recurrent Payment is not active|Recorrencia não está ativa. Execução paralizada|
|170|Cartão Protegido not configured|Cartão protegido não vinculado ao cadastro do lojista|
|171|Affiliation data not sent|Falha no processamento do pedido - Entre em contato com o suporte Braspag|
|172|Credential Code is required|Falha na validação das credenciadas enviadas|
|173|Payment method is not enabled|Meio de pagamento não vinculado ao cadastro do lojista|
|174|Card Number is required|Campo enviado está vazio ou invalido|
|175|EAN is required|Campo enviado está vazio ou invalido|
|176|Payment Currency is not supported|Campo enviado está vazio ou invalido|
|177|Card Number is invalid|Campo enviado está vazio ou invalido|
|178|EAN is invalid|Campo enviado está vazio ou invalido|
|179|The max number of installments allowed for recurring payment is 1|Campo enviado está vazio ou invalido|
|180|The provided Card PaymentToken was not found|Token do Cartão protegido não encontrado|
|181|The MerchantIdJustClick is not configured|Token do Cartão protegido bloqueado|
|182|Brand is required|Bandeira do cartão não enviado|
|183|Invalid customer bithdate|Data de nascimento invalida ou futura|
|184|Request could not be empty|Falha no formado ta requisição. Verifique o código enviado|
|185|Brand is not supported by selected provider|Bandeira não suportada pela API Braspag|
|186|The selected provider does not support the options provided (Capture, Authenticate, Recurrent or Installments)|Meio de pagamento não suporta o comando enviado|
|187|ExtraData Collection contains one or more duplicated names|---|
|188|Avs with CPF invalid|---|
|189|Avs with length of street exceeded|Dado enviado excede o tamanho do campo|
|190|Avs with length of number exceeded|Dado enviado excede o tamanho do campo|
|190|Avs with length of complement exceeded|Dado enviado excede o tamanho do campo|
|191|Avs with length of district exceeded|Dado enviado excede o tamanho do campo|
|192|Avs with zip code invalid|CEP enviado é invalido|
|193|Split Amount must be greater than zero|Valor para realização do SPLIT deve ser superior a 0|
|194|Split Establishment is Required|SPLIT não habilitado para o cadastro da loja|
|195|The PlataformId is required|Validados de plataformas não enviado|
|196|DeliveryAddress is required|Campo obrigatório não enviado|
|197|Street is required|Campo obrigatório não enviado|
|198|Number is required|Campo obrigatório não enviado|
|199|ZipCode is required|Campo obrigatório não enviado|
|200|City is required|Campo obrigatório não enviado|
|201|State is required|Campo obrigatório não enviado|
|202|District is required|Campo obrigatório não enviado|
|203|Cart item Name is required|Campo obrigatório não enviado|
|204|Cart item Quantity is required|Campo obrigatório não enviado|
|205|Cart item type is required|Campo obrigatório não enviado|
|206|Cart item name length exceeded |Dado enviado excede o tamanho do campo|
|207|Cart item description length exceeded |Dado enviado excede o tamanho do campo|
|208|Cart item sku length exceeded |Dado enviado excede o tamanho do campo|
|209|Shipping addressee sku length exceeded |Dado enviado excede o tamanho do campo|
|210|Shipping data cannot be null|Campo obrigatório não enviado|
|211|WalletKey is invalid|Dados da Visa Checkout invalidos|
|212|Merchant Wallet Configuration not found|Visa Checkout não vinculado a conta do lojista|
|213|Credit Card Number is invalid|Cartão de crédito enviado é invalido|
|214|Credit Card Holder Must Have Only Letters|Portador do cartão não deve conter caracteres especiais|
|215|Agency is required in Boleto Credential|Campo obrigatório não enviado|
|216|Customer IP address is invalid|IP bloqueado por questões de segurança|
|300|MerchantId was not found|---|
|301|Request IP is not allowed|---|
|302|Sent MerchantOrderId is duplicated|---|
|303|Sent OrderId does not exist|---|
|304|Customer Identity is required|---|
|306|Merchant is blocked|---|
|307|Transaction not found|Transação não encontrada ou não existente no ambiente|
|308|Transaction not available to capture|Transação não pode ser capturada - Entre em contato com o suporte Braspag|
|309|Transaction not available to void|Transação não pode ser Cancelada - Entre em contato com o suporte Braspag|
|310|Payment method doest not support this operation|Comando enviado não suportado pelo meio de pagamento|
|311|Refund is not enabled for this merchant|Cancelamento após 24 horas não liberado para o lojista|
|312|Transaction not available to refund|Transação não permite cancelamento após 24 horas|
|313|Recurrent Payment not found|Transação recorrente não encontrada ou não disponivel no ambiente|
|314|Invalid Integration|---|
|315|Cannot change NextRecurrency with pending payment|---|
|316|Cannot set NextRecurrency to past date|Não é permitido alterada dada da recorrencia para uma data passada|
|317|Invalid Recurrency Day|---|
|318|No transaction found|---|
|319|Smart recurrency is not enabled|Recorrencia não vinculada ao cadastro do lojista|
|320|Can not Update Affiliation Because this Recurrency not Affiliation saved|---|
|321|Can not set EndDate to before next recurrency|---|
|322|Zero Dollar Auth is not enabled|Zero Dollar não vinculado ao cadastro do lojista|
|323|Bin Query is not enabled|Consulta de Bins não vinculada ao cadastro do lojista|

## Códigos de Retorno das Vendas

Códigos retornados pelo autorizador e que descrevem a autorização ou não da venda e, em caso negativo, os cenários onde o lojista pode retentar enviar a transação.

|Código Resposta|Definição|Significado|Ação|Permite Retentativa|
|---------------|---------|-----------|----|-------------------|
|00|Transação autorizada com sucesso|Transação autorizada com sucesso|Transação autorizada com sucesso|Não|
|000|Transação autorizada com sucesso|Transação autorizada com sucesso|Transação autorizada com sucesso|Não|
|01|Transação não autorizada. Transação referida|Transação não autorizada. Referida (suspeita de fraude) pelo banco emissor|Transação não autorizada. Entre em contato com seu banco emissor|Não|
|02|Transação não autorizada. Transação referida|Transação não autorizada. Referida (suspeita de fraude) pelo banco emissor|Transação não autorizada. Entre em contato com seu banco emissor|Não|
|03|Transação não permitida. Erro no cadastramento do código do estabelecimento no arquivo de configuração do TEF|Transação não permitida. Estabelecimento inválido. Entre com contato com a Braspag|Não foi possível processar a transação. Entre com contato com a Loja Virtual|Não|
|04|Transação não autorizada. Cartão bloqueado pelo banco emissor|Transação não autorizada. Cartão bloqueado pelo banco emissor|Transação não autorizada. Entre em contato com seu banco emissor|Não|
|05|Transação não autorizada. Cartão inadimplente (Do not honor)|Transação não autorizada. Não foi possível processar a transação. Questão relacionada a segurança, inadimplencia ou limite do portador|Transação não autorizada. Entre em contato com seu banco emissor|Apenas 4 vezes em 16 dias|
|06|Transação não autorizada. Cartão cancelado|Transação não autorizada. Não foi possível processar a transação. Cartão cancelado permanentemente pelo banco emissor|Não foi possível processar a transação. Entre em contato com seu banco emissor|Não|
|07|Transação negada. Reter cartão condição especial|Transação não autorizada por regras do banco emissor|Transação não autorizada. Entre em contato com seu banco emissor|Não|
|08|Transação não autorizada. Código de segurança inválido|Transação não autorizada. Código de segurança inválido. Oriente o portador a corrigir os dados e tentar novamente|Transação não autorizada. Dados incorretos. Reveja os dados e informe novamente|Não|
|11|Transação autorizada com sucesso para cartão emitido no exterior|Transação autorizada com sucesso|Transação autorizada com sucesso|Não|
|12|Transação inválida, erro no cartão|Não foi possível processar a transação. Solicite ao portador que verifique os dados do cartão e tente novamente|Não foi possível processar a transação. reveja os dados informados e tente novamente. Se o erro persistir, entre em contato com seu banco emissor|Não|
|13|Transação não permitida. Valor da transação Inválido|Transação não permitida. Valor inválido. Solicite ao portador que reveja os dados e novamente. Se o erro persistir, entre em contato com a Braspag|Transação não autorizada. Valor inválido. Refazer a transação confirmando os dados informados. Persistindo o erro, entrar em contato com a loja virtual|Não|
|14|Transação não autorizada. Cartão Inválido|Transação não autorizada. Cartão inválido. Pode ser bloqueio do cartão no banco emissor, dados incorretos ou tentativas de testes de cartão. Use o Algoritmo de Lhum (Mod 10) para evitar transações não autorizadas por esse motivo. Consulte www.Braspag.com.br/desenvolvedores para implantar o Algoritmo de Lhum|Não foi possível processar a transação. reveja os dados informados e tente novamente. Se o erro persistir, entre em contato com seu banco emissor|Não|
|15|Banco emissor indisponível ou inexistente|Transação não autorizada. Banco emissor indisponível|Não foi possível processar a transação. Entre em contato com seu banco emissor|Não|
|19|Refaça a transação ou tente novamente mais tarde|Não foi possível processar a transação. Refaça a transação ou tente novamente mais tarde. Se o erro persistir, entre em contato com a Braspag|Não foi possível processar a transação. Refaça a transação ou tente novamente mais tarde. Se o erro persistir entre em contato com a loja virtual|Apenas 4 vezes em 16 dias|
|21|Cancelamento não efetuado. Transação não localizada|Não foi possível processar o cancelamento. Se o erro persistir, entre em contato com a Braspag|Não foi possível processar o cancelamento. Tente novamente mais tarde. Persistindo o erro, entrar em contato com a loja virtual|Não|
|22|Parcelamento inválido. Número de parcelas inválidas|Não foi possível processar a transação. Número de parcelas inválidas. Se o erro persistir, entre em contato com a Braspag|Não foi possível processar a transação. Valor inválido. Refazer a transação confirmando os dados informados. Persistindo o erro, entrar em contato com a loja virtual|Não|
|23|Transação não autorizada. Valor da prestação inválido|Não foi possível processar a transação. Valor da prestação inválido. Se o erro persistir, entre em contato com a Braspag|Não foi possível processar a transação. Valor da prestação inválido. Refazer a transação confirmando os dados informados. Persistindo o erro, entrar em contato com a loja virtual|Não|
|24|Quantidade de parcelas inválido|Não foi possível processar a transação. Quantidade de parcelas inválido. Se o erro persistir, entre em contato com a Braspag|Não foi possível processar a transação. Quantidade de parcelas inválido. Refazer a transação confirmando os dados informados. Persistindo o erro, entrar em contato com a loja virtual|Não|
|25|Pedido de autorização não enviou número do cartão|Não foi possível processar a transação. Solicitação de autorização não enviou o número do cartão. Se o erro persistir, verifique a comunicação entre loja virtual e Braspag|Não foi possível processar a transação. reveja os dados informados e tente novamente. Persistindo o erro, entrar em contato com a loja virtual|Apenas 4 vezes em 16 dias|
|28|Arquivo temporariamente indisponível|Não foi possível processar a transação. Arquivo temporariamente indisponível. Reveja a comunicação entre Loja Virtual e Braspag. Se o erro persistir, entre em contato com a Braspag|Não foi possível processar a transação. Entre com contato com a Loja Virtual|Apenas 4 vezes em 16 dias|
|39|Transação não autorizada. Erro no banco emissor|Transação não autorizada. Erro no banco emissor|Transação não autorizada. Entre em contato com seu banco emissor|Não|
|41|Transação não autorizada. Cartão bloqueado por perda|Transação não autorizada. Cartão bloqueado por perda|Transação não autorizada. Entre em contato com seu banco emissor|Não|
|43|Transação não autorizada. Cartão bloqueado por roubo|Transação não autorizada. Cartão bloqueado por roubo|Transação não autorizada. Entre em contato com seu banco emissor|Não|
|51|Transação não autorizada. Limite excedido/sem saldo|Transação não autorizada. Limite excedido/sem saldo|Transação não autorizada. Entre em contato com seu banco emissor|A partir do dia seguinte, apenas 4 vezes em 16 dias|
|52|Cartão com dígito de controle inválido|Não foi possível processar a transação. Cartão com dígito de controle inválido|Transação não autorizada. Reveja os dados informados e tente novamente|Não|
|53|Transação não permitida. Cartão poupança inválido|Transação não permitida. Cartão poupança inválido|Não foi possível processar a transação. Entre em contato com seu banco emissor|Não|
|54|Transação não autorizada. Cartão vencido|Transação não autorizada. Cartão vencido|Transação não autorizada. Refazer a transação confirmando os dados|Não|
|55|Transação não autorizada. Senha inválida|Transação não autorizada. Senha inválida|Transação não autorizada. Entre em contato com seu banco emissor|Não|
|57|Transação não permitida para o cartão|Transação não autorizada. Transação não permitida para o cartão|Transação não autorizada. Entre em contato com seu banco emissor|Apenas 4 vezes em 16 dias|
|58|Transação não permitida. Opção de pagamento inválida|Transação não permitida. Opção de pagamento inválida. Reveja se a opção de pagamento escolhida está habilitada no cadastro|Transação não autorizada. Entre em contato com sua loja virtual|Não|
|59|Transação não autorizada. Suspeita de fraude|Transação não autorizada. Suspeita de fraude|Transação não autorizada. Entre em contato com seu banco emissor|Não|
|60|Transação não autorizada|Transação não autorizada. Tente novamente. Se o erro persistir o portador deve entrar em contato com o banco emissor|Não foi possível processar a transação. Tente novamente mais tarde. Se o erro persistir, entre em contato com seu banco emissor|Apenas 4 vezes em 16 dias|
|61|Banco emissor Visa indisponível|Transação não autorizada. Banco emissor Visa indisponível|Transação não autorizada. Tente novamente. Se o erro persistir, entre em contato com seu banco emissor|Apenas 4 vezes em 16 dias|
|62|Transação não autorizada. Cartão restrito para uso doméstico|Transação não autorizada. Cartão restrito para uso doméstico|Transação não autorizada. Entre em contato com seu banco emissor|A partir do dia seguinte, apenas 4 vezes em 16 dias|
|63|Transação não autorizada. Violação de segurança|Transação não autorizada. Violação de segurança|Transação não autorizada. Entre em contato com seu banco emissor|Não|
|64|Transação não autorizada. Valor abaixo do mínimo exigido pelo banco emissor|Transação não autorizada. Entre em contato com seu banco emissor|Transação não autorizada. Valor abaixo do mínimo exigido pelo banco emissor|Não|
|65|Transação não autorizada. Excedida a quantidade de transações para o cartão|Transação não autorizada. Excedida a quantidade de transações para o cartão|Transação não autorizada. Entre em contato com seu banco emissor|Apenas 4 vezes em 16 dias|
|67|Transação não autorizada. Cartão bloqueado para compras hoje|Transação não autorizada. Cartão bloqueado para compras hoje. Bloqueio pode ter ocorrido por excesso de tentativas inválidas. O cartão será desbloqueado automaticamente à meia noite|Transação não autorizada. Cartão bloqueado temporariamente. Entre em contato com seu banco emissor|A partir do dia seguinte, apenas 4 vezes em 16 dias|
|70|Transação não autorizada. Limite excedido/sem saldo|Transação não autorizada. Limite excedido/sem saldo|Transação não autorizada. Entre em contato com seu banco emissor|A partir do dia seguinte, apenas 4 vezes em 16 dias|
|72|Cancelamento não efetuado. Saldo disponível para cancelamento insuficiente|Cancelamento não efetuado. Saldo disponível para cancelamento insuficiente. Se o erro persistir, entre em contato com a Braspag|Cancelamento não efetuado. Tente novamente mais tarde. Se o erro persistir, entre em contato com a loja virtual|Não|
|74|Transação não autorizada. A senha está vencida|Transação não autorizada. A senha está vencida|Transação não autorizada. Entre em contato com seu banco emissor|Não|
|75|Senha bloqueada. Excedeu tentativas de cartão|Transação não autorizada|Sua Transação não pode ser processada. Entre em contato com o Emissor do seu cartão|Não|
|76|Cancelamento não efetuado. Banco emissor não localizou a transação original|Cancelamento não efetuado. Banco emissor não localizou a transação original|Cancelamento não efetuado. Entre em contato com a loja virtual|Não|
|77|Cancelamento não efetuado. Não foi localizado a transação original|Cancelamento não efetuado. Não foi localizado a transação original|Cancelamento não efetuado. Entre em contato com a loja virtual|Não|
|78|Transação não autorizada. Cartão bloqueado primeiro uso|Transação não autorizada. Cartão bloqueado primeiro uso. Solicite ao portador que desbloqueie o cartão diretamente com seu banco emissor|Transação não autorizada. Entre em contato com seu banco emissor e solicite o desbloqueio do cartão|Não|
|80|Transação não autorizada. Divergencia na data de transação/pagamento|Transação não autorizada. Data da transação ou data do primeiro pagamento inválida|Transação não autorizada. Refazer a transação confirmando os dados|Não|
|82|Transação não autorizada. Cartão inválido|Transação não autorizada. Cartão Inválido. Solicite ao portador que reveja os dados e tente novamente|Transação não autorizada. Refazer a transação confirmando os dados. Se o erro persistir, entre em contato com seu banco emissor|Não|
|83|Transação não autorizada. Erro no controle de senhas|Transação não autorizada. Erro no controle de senhas|Transação não autorizada. Refazer a transação confirmando os dados. Se o erro persistir, entre em contato com seu banco emissor|Não|
|85|Transação não permitida. Falha da operação|Transação não permitida. Houve um erro no processamento.Solicite ao portador que digite novamente os dados do cartão, se o erro persistir pode haver um problema no terminal do lojista, nesse caso o lojista deve entrar em contato com a Braspag|Transação não permitida. Informe os dados do cartão novamente. Se o erro persistir, entre em contato com a loja virtual|Não|
|86|Transação não permitida. Falha da operação|Transação não permitida. Houve um erro no processamento.Solicite ao portador que digite novamente os dados do cartão, se o erro persistir pode haver um problema no terminal do lojista, nesse caso o lojista deve entrar em contato com a Braspag|Transação não permitida. Informe os dados do cartão novamente. Se o erro persistir, entre em contato com a loja virtual|Não|
|89|Erro na transação|Transação não autorizada. Erro na transação. O portador deve tentar novamente e se o erro persistir, entrar em contato com o banco emissor|Transação não autorizada. Erro na transação. Tente novamente e se o erro persistir, entre em contato com seu banco emissor|Apenas 4 vezes em 16 dias|
|90|Transação não permitida. Falha da operação|Transação não permitida. Houve um erro no processamento.Solicite ao portador que digite novamente os dados do cartão, se o erro persistir pode haver um problema no terminal do lojista, nesse caso o lojista deve entrar em contato com a Braspag|Transação não permitida. Informe os dados do cartão novamente. Se o erro persistir, entre em contato com a loja virtual|Não|
|91|Transação não autorizada. Banco emissor temporariamente indisponível|Transação não autorizada. Banco emissor temporariamente indisponível|Transação não autorizada. Banco emissor temporariamente indisponível. Entre em contato com seu banco emissor|Apenas 4 vezes em 16 dias|
|92|Transação não autorizada. Tempo de comunicação excedido|Transação não autorizada. Tempo de comunicação excedido|Transação não autorizada. Comunicação temporariamente indisponível. Entre em contato com a loja virtual|Apenas 4 vezes em 16 dias|
|93|Transação não autorizada. Violação de regra - Possível erro no cadastro|Transação não autorizada. Violação de regra - Possível erro no cadastro|Sua transação não pode ser processada. Entre em contato com a loja virtual|Não|
|96|Falha no processamento|Não foi possível processar a transação. Falha no sistema da Braspag. Se o erro persistir, entre em contato com a Braspag|Sua Transação não pode ser processada, Tente novamente mais tarde. Se o erro persistir, entre em contato com a loja virtual|Apenas 4 vezes em 16 dias|
|97|Valor não permitido para essa transação|Transação não autorizada. Valor não permitido para essa transação|Transação não autorizada. Valor não permitido para essa transação|Não|
|98|Sistema/comunicação indisponível|Transação não autorizada. Sistema do emissor sem comunicação. Se for geral, verificar SITEF, GATEWAY e/ou Conectividade|Sua Transação não pode ser processada, Tente novamente mais tarde. Se o erro persistir, entre em contato com a loja virtual|Apenas 4 vezes em 16 dias|
|99|Sistema/comunicação indisponível|Transação não autorizada. Sistema do emissor sem comunicação. Tente mais tarde.  Pode ser erro no SITEF, favor verificar !|Sua Transação não pode ser processada, Tente novamente mais tarde. Se o erro persistir, entre em contato com a loja virtual|A partir do dia seguinte, apenas 4 vezes em 16 dias|
|999|Sistema/comunicação indisponível|Transação não autorizada. Sistema do emissor sem comunicação. Tente mais tarde.  Pode ser erro no SITEF, favor verificar !|Sua Transação não pode ser processada, Tente novamente mais tarde. Se o erro persistir, entre em contato com a loja virtual|A partir do dia seguinte, apenas 4 vezes em 16 dias|
|AA|Tempo Excedido|Tempo excedido na comunicação com o banco emissor. Oriente o portador a tentar novamente, se o erro persistir será necessário que o portador contate seu banco emissor|Tempo excedido na sua comunicação com o banco emissor, tente novamente mais tarde. Se o erro persistir, entre em contato com seu banco|Apenas 4 vezes em 16 dias|
|AC|Transação não permitida. Cartão de débito sendo usado com crédito. Use a função débito|Transação não permitida. Cartão de débito sendo usado com crédito. Solicite ao portador que selecione a opção de pagamento Cartão de Débito|Transação não autorizada. Tente novamente selecionando a opção de pagamento cartão de débito|Não|
|AE|Tente Mais Tarde|Tempo excedido na comunicação com o banco emissor. Oriente o portador a tentar novamente, se o erro persistir será necessário que o portador contate seu banco emissor|Tempo excedido na sua comunicação com o banco emissor, tente novamente mais tarde. Se o erro persistir, entre em contato com seu banco|Apenas 4 vezes em 16 dias|
|AF|Transação não permitida. Falha da operação|Transação não permitida. Houve um erro no processamento.Solicite ao portador que digite novamente os dados do cartão, se o erro persistir pode haver um problema no terminal do lojista, nesse caso o lojista deve entrar em contato com a Braspag|Transação não permitida. Informe os dados do cartão novamente. Se o erro persistir, entre em contato com a loja virtual|Não|
|AG|Transação não permitida. Falha da operação|Transação não permitida. Houve um erro no processamento.Solicite ao portador que digite novamente os dados do cartão, se o erro persistir pode haver um problema no terminal do lojista, nesse caso o lojista deve entrar em contato com a Braspag|Transação não permitida. Informe os dados do cartão novamente. Se o erro persistir, entre em contato com a loja virtual|Não|
|AH|Transação não permitida. Cartão de crédito sendo usado com débito. Use a função crédito|Transação não permitida. Cartão de crédito sendo usado com débito. Solicite ao portador que selecione a opção de pagamento Cartão de Crédito|Transação não autorizada. Tente novamente selecionando a opção de pagamento cartão de crédito|Não|
|AI|Transação não autorizada. Autenticação não foi realizada|Transação não autorizada. Autenticação não foi realizada. O portador não concluiu a autenticação. Solicite ao portador que reveja os dados e tente novamente. Se o erro persistir, entre em contato com a Braspag informando o BIN (6 primeiros dígitos do cartão)|Transação não autorizada. Autenticação não foi realizada com sucesso. Tente novamente e informe corretamente os dados solicitado. Se o erro persistir, entre em contato com o lojista|Não|
|AJ|Transação não permitida. Transação de crédito ou débito em uma operação que permite apenas Private Label. Tente novamente selecionando a opção Private Label|Transação não permitida. Transação de crédito ou débito em uma operação que permite apenas Private Label. Solicite ao portador que tente novamente selecionando a opção Private Label. Caso não disponibilize a opção Private Label verifique na Braspag se o seu estabelecimento permite essa operação|Transação não permitida. Transação de crédito ou débito em uma operação que permite apenas Private Label. Tente novamente e selecione a opção Private Label. Em caso de um novo erro entre em contato com a loja virtual|Não|
|AV|Transação não autorizada. Dados Inválidos|Falha na validação dos dados da transação. Oriente o portador a rever os dados e tentar novamente|Falha na validação dos dados. Reveja os dados informados e tente novamente|Apenas 4 vezes em 16 dias|
|BD|Transação não permitida. Falha da operação|Transação não permitida. Houve um erro no processamento.Solicite ao portador que digite novamente os dados do cartão, se o erro persistir pode haver um problema no terminal do lojista, nesse caso o lojista deve entrar em contato com a Braspag|Transação não permitida. Informe os dados do cartão novamente. Se o erro persistir, entre em contato com a loja virtual|Não|
|BL|Transação não autorizada. Limite diário excedido|Transação não autorizada. Limite diário excedido. Solicite ao portador que entre em contato com seu banco emissor|Transação não autorizada. Limite diário excedido. Entre em contato com seu banco emissor|A partir do dia seguinte, apenas 4 vezes em 16 dias|
|BM|Transação não autorizada. Cartão Inválido|Transação não autorizada. Cartão inválido. Pode ser bloqueio do cartão no banco emissor ou dados incorretos. Tente usar o Algoritmo de Lhum (Mod 10) para evitar transações não autorizadas por esse motivo|Transação não autorizada. Cartão inválido.  Refaça a transação confirmando os dados informados|Não|
|BN|Transação não autorizada. Cartão ou conta bloqueado|Transação não autorizada. O cartão ou a conta do portador está bloqueada. Solicite ao portador que entre em contato com  seu banco emissor|Transação não autorizada. O cartão ou a conta do portador está bloqueada. Entre em contato com  seu banco emissor|Não|
|BO|Transação não permitida. Falha da operação|Transação não permitida. Houve um erro no processamento. Solicite ao portador que digite novamente os dados do cartão, se o erro persistir, entre em contato com o banco emissor|Transação não permitida. Houve um erro no processamento. Digite novamente os dados do cartão, se o erro persistir, entre em contato com o banco emissor|Apenas 4 vezes em 16 dias|
|BP|Transação não autorizada. Conta corrente inexistente|Transação não autorizada. Não possível processar a transação por um erro relacionado ao cartão ou conta do portador. Solicite ao portador que entre em contato com o banco emissor|Transação não autorizada. Não possível processar a transação por um erro relacionado ao cartão ou conta do portador. Entre em contato com o banco emissor|Não|
|BV|Transação não autorizada. Cartão vencido|Transação não autorizada. Cartão vencido|Transação não autorizada. Refazer a transação confirmando os dados|Não|
|CF|Transação não autorizada.C79:J79 Falha na validação dos dados|Transação não autorizada. Falha na validação dos dados. Solicite ao portador que entre em contato com o banco emissor|Transação não autorizada. Falha na validação dos dados. Entre em contato com o banco emissor|Não|
|CG|Transação não autorizada. Falha na validação dos dados|Transação não autorizada. Falha na validação dos dados. Solicite ao portador que entre em contato com o banco emissor|Transação não autorizada. Falha na validação dos dados. Entre em contato com o banco emissor|Não|
|DA|Transação não autorizada. Falha na validação dos dados|Transação não autorizada. Falha na validação dos dados. Solicite ao portador que entre em contato com o banco emissor|Transação não autorizada. Falha na validação dos dados. Entre em contato com o banco emissor|Não|
|DF|Transação não permitida. Falha no cartão ou cartão inválido|Transação não permitida. Falha no cartão ou cartão inválido. Solicite ao portador que digite novamente os dados do cartão, se o erro persistir, entre em contato com o banco |Transação não permitida. Falha no cartão ou cartão inválido. Digite novamente os dados do cartão, se o erro persistir, entre em contato com o banco |Apenas 4 vezes em 16 dias|
|DM|Transação não autorizada. Limite excedido/sem saldo|Transação não autorizada. Limite excedido/sem saldo|Transação não autorizada. Entre em contato com seu banco emissor|A partir do dia seguinte, apenas 4 vezes em 16 dias|
|DQ|Transação não autorizada. Falha na validação dos dados|Transação não autorizada. Falha na validação dos dados. Solicite ao portador que entre em contato com o banco emissor|Transação não autorizada. Falha na validação dos dados. Entre em contato com o banco emissor|Não|
|DS|Transação não permitida para o cartão|Transação não autorizada. Transação não permitida para o cartão|Transação não autorizada. Entre em contato com seu banco emissor|Apenas 4 vezes em 16 dias|
|EB|Transação não autorizada. Limite diário excedido|Transação não autorizada. Limite diário excedido. Solicite ao portador que entre em contato com seu banco emissor|Transação não autorizada. Limite diário excedido. Entre em contato com seu banco emissor|A partir do dia seguinte, apenas 4 vezes em 16 dias|
|EE|Transação não permitida. Valor da parcela inferior ao mínimo permitido|Transação não permitida. Valor da parcela inferior ao mínimo permitido. Não é permitido parcelas inferiores a R$ 5,00. Necessário rever calculo para parcelas|Transação não permitida. O valor da parcela está abaixo do mínimo permitido. Entre em contato com a loja virtual|Não|
|EK|Transação não permitida para o cartão|Transação não autorizada. Transação não permitida para o cartão|Transação não autorizada. Entre em contato com seu banco emissor|Apenas 4 vezes em 16 dias|
|FA|Transação não autorizada. |Transação não autorizada AmEx|Transação não autorizada. Entre em contato com seu banco emissor|Não|
|FC|Transação não autorizada. Ligue Emissor|Transação não autorizada. Oriente o portador a entrar em contato com o banco emissor|Transação não autorizada. Entre em contato com seu banco emissor|Não|
|FD|Transação negada. Reter cartão condição especial|Transação não autorizada por regras do banco emissor|Transação não autorizada. Entre em contato com seu banco emissor|Não|
|FE|Transação não autorizada. Divergencia na data de transação/pagamento|Transação não autorizada. Data da transação ou data do primeiro pagamento inválida|Transação não autorizada. Refazer a transação confirmando os dados|Não|
|FF|Cancelamento OK|Transação de cancelamento autorizada com sucesso. ATENÇÂO: Esse retorno é para casos de cancelamentos e não para casos de autorizações|Transação de cancelamento autorizada com sucesso|Não|
|FG|Transação não autorizada. Ligue AmEx|Transação não autorizada. Oriente o portador a entrar em contato com a Central de Atendimento AmEx|Transação não autorizada. Entre em contato com a Central de Atendimento AmEx no telefone 08007285090|Não|
|FG|Ligue 08007285090|Transação não autorizada. Oriente o portador a entrar em contato com a Central de Atendimento AmEx|Transação não autorizada. Entre em contato com a Central de Atendimento AmEx no telefone 08007285090|Não|
|GA|Aguarde Contato|Transação não autorizada. Referida pelo Lynx Online de forma preventiva. A Braspag entrará em contato com o lojista sobre esse caso|Transação não autorizada. Entre em contato com o lojista|Não|
|HJ|Transação não permitida. Código da operação inválido|Transação não permitida. Código da operação Coban inválido|Transação não permitida. Código da operação Coban inválido. Entre em contato com o lojista|Não|
|IA|Transação não permitida. Indicador da operação inválido|Transação não permitida. Indicador da operação Coban inválido|Transação não permitida. Indicador da operação Coban inválido. Entre em contato com o lojista|Não|
|JB|Transação não permitida. Valor da operação inválido|Transação não permitida. Valor da operação Coban inválido|Transação não permitida. Valor da operação Coban inválido. Entre em contato com o lojista|Não|
|KA|Transação não permitida. Falha na validação dos dados|Transação não permitida. Houve uma falha na validação dos dados. Solicite ao portador que reveja os dados e tente novamente. Se o erro persistir verifique a comunicação entre loja virtual e Braspag|Transação não permitida. Houve uma falha na validação dos dados. reveja os dados informados e tente novamente. Se o erro persistir entre em contato com a Loja Virtual|Não|
|KB|Transação não permitida. Selecionado a opção incorrente|Transação não permitida. Selecionado a opção incorreta. Solicite ao portador que reveja os dados e tente novamente. Se o erro persistir deve ser verificado a comunicação entre loja virtual e Braspag|Transação não permitida. Selecionado a opção incorreta. Tente novamente. Se o erro persistir entre em contato com a Loja Virtual|Não|
|KE|Transação não autorizada. Falha na validação dos dados|Transação não autorizada. Falha na validação dos dados. Opção selecionada não está habilitada. Verifique as opções disponíveis para o portador|Transação não autorizada. Falha na validação dos dados. Opção selecionada não está habilitada. Entre em contato com a loja virtual|Não|
|N7|Transação não autorizada. Código de segurança inválido|Transação não autorizada. Código de segurança inválido. Oriente o portador corrigir os dados e tentar novamente|Transação não autorizada. Reveja os dados e informe novamente|Não|
|R1|Transação não autorizada. Cartão inadimplente (Do not honor)|Transação não autorizada. Não foi possível processar a transação. Questão relacionada a segurança, inadimplencia ou limite do portador|Transação não autorizada. Entre em contato com seu banco emissor|Apenas 4 vezes em 16 dias|
|U3|Transação não permitida. Falha na validação dos dados|Transação não permitida. Houve uma falha na validação dos dados. Solicite ao portador que reveja os dados e tente novamente. Se o erro persistir verifique a comunicação entre loja virtual e Braspag|Transação não permitida. Houve uma falha na validação dos dados. reveja os dados informados e tente novamente. Se o erro persistir entre em contato com a Loja Virtual|Não|

## Glossário

Para facilitar o entendimento, listamos abaixo um pequeno glossário com os principais termos relacionados ao eCommerce, ao mercado de cartões e adquirencia:

* **AUTORIZAÇÃO**: A autorização é a principal operação no e-commerce, é através dela que uma venda pode ser concretizada. Existe a possibilidade de realizar uma pré-autorização que apenas sensibiliza o limite do cliente, mas ainda não gera cobrança para o consumidor.
* **CAPTURA**: Ao realizar uma pré-autorização, é necessário a confirmação desta para que a cobrança seja efetivada ao portador do cartão. Através da operação de captura que se efetiva uma pré-autorização, podendo esta ser executada, em média, em até 5 dias após a data da pré-autorização.
* **CANCELAMENTO** / **ESTORNO**: O cancelamento é necessário quando, por algum motivo, não se quer mais efetivar uma venda. No caso de uma pré-autorização, o cancelamento irá liberar o limite do cartão que foi sensibilizado em uma pré-autorização. Quando a transação já estiver sido capturada ou for uma Autorização, o cancelamento irá desfazer a venda, mas deve ser executado até às 23:59:59 da data da autorização/captura. *É considerado estorno ao realizar a chamada após as 23:59:59 da data da autorização/captura.*
* **AUTENTICAÇÃO**: O processo de autenticação possibilita realizar uma venda a qual passará pelo processo de autenticação do banco emissor do cartão, assim trazendo mais segurança para a venda e transferindo para o banco o risco de fraude.
* **CARTÃO PROTEGIDO**: É uma plataforma que permite o armazenamento seguro de dados sensíveis de cartão de crédito. Estes dados são transformados em um código criptografrado chamado de “token”, que poderá ser armazenado em banco de dados. Com a plataforma, a loja poderá oferecer recursos como “Compra com 1 clique” e “Retentativa de envio de transação”, sempre preservando a integridade e a confidencialidade das informações.
* **ANTIFRAUDE**: É uma plataforma de prevenção à fraude que fornece uma análise de risco detalhada das compras on-line. Cada transação é submetida a mais de 260 regras, além das regras específicas de cada segmento, e geram uma recomendação de risco em aproximadamente dois segundos. Este processo é totalmente transparente para o portador do cartão. De acordo com os critérios preestabelecidos, o pedido pode ser automaticamente aceito, recusado ou encaminhado para análise manual.
* **RECORRENTE**: A Recorrência Inteligente é um recurso indispensável para estabelicimentos que precisam cobrar regularmente por seus produtos/serviços.
É muito utilizado para assinaturas de revistas, mensalidades, licenças de software, entre outros. Os lojistas contarão com recursos diferenciados para modelar sua cobrança de acordo com o seu negócio, pois toda parametrização é configurável, tais como: periodicidade, data de início e fim, quantidade de tentativas, intervalo entre elas, entre outros.
* **RENOVA FÁCIL**: O serviço Renova Fácil traz garantia e tranquilidade à estabelecimentos que utilizam a aquirencia da Cielo e que efetuam vendas recorrentes.
Ele possibilita a atualização do número do cartão do cliente, cuja numeração ou validade tenha sido alterada, eliminando a necessidade de contato com o cliente no momento da renovação do serviço e reduzindo o número de transações negadas. Com o Renova Fácil você garante as transações e oferece mais comodidade a seus clientes. A funcionalidade do Renova Fácil é transparente para o lojista e para o cliente final. Toda a integração fica do nosso lado, onde conseguimos atualizar os novos dados do cartão e submeter a transação com a informação atualizada.

## Cartões para Teste (Simulado)

O Simulado é um meio de pagamento que emula a utilizaçao de pagamentos com Cartão de Crétido. Com esse meio de pagamento é possivel simular todos os fluxos de Autorização, Captura e Cancelamento.

Para melhor utilização do Meio de Pagamento Simulado, estamos disponibilizando cartões de testes na tabela abaixo.

Os status das transações serão conforme a utilização de cada cartão.

|Status da Transação|Cartões para realização dos testes|Código de Retorno|Mensagem de Retorno|
|-------------------|----------------------------------|-----------------|-------------------|
|Autorizado|0000.0000.0000.0001 / 0000.0000.0000.0004|4|Operação realizada com sucesso|
|Não Autorizado|0000.0000.0000.0002|2|Não Autorizada|
|Autorização Aleatória|0000.0000.0000.0009|4 / 99|Operation Successful / Time Out|
|Não Autorizado|0000.0000.0000.0007|77|Cartão Cancelado|
|Não Autorizado|0000.0000.0000.0008|70|Problemas com o Cartão de Crédito|
|Não Autorizado|0000.0000.0000.0005|78|Cartão Bloqueado|
|Não Autorizado|0000.0000.0000.0003|57|Cartão Expirado|
|Não Autorizado|0000.0000.0000.0006|99|Time Out|

As informações de Cód.Segurança (CVV) e validade podem ser aleatórias, mantendo o formato - CVV (3 dígitos) Validade (MM/YYYY).

