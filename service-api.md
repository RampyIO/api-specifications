## Rampy

Here we will shortly describe how our API will work and which steps you need to integrate in order to successfully use our service and onboard new or existing customers on your service.

### Base URL

As soon as we have decided on the name of our service, the following box will be updated

> ```
> https://api.rampy.io
> ```

Every endpoint will use content type `application/json`, except where otherwise noted.

### Authentication

You will receive a `client_id` as soon as you have created an account on our service. This `client_id` is needed to authenticate with our service and therefore needs to be added in the `Authorization` header for every request.

> ```javascript
>  curl "https://api.rampy.io" -H "Authorization: client_id [CLIENT_ID]"
> ```

### Resources

| Resource  | Description |
|-----------|-------------|
| `Challenge` | A randomized string which needs to be included in a message to be signed |
| `Order` | A lifetime order representing payment and payout information |
| `Operation` | An initiated operation of a specific order |

### Pairs

| Pair | Crypto | Fiat |
|------|--------|------|
| `ada/eur` | `ada` | `eur` |
| `ada/usd` | `ada` | `usd` |

### Operation types

| Type  | Description |
|-----------|-------------|
| `buy` | Buy crypto currency `ada` with fiat currency `eur` or `usd` |
| `sell` | Sell crypto currency `ada` for fiat currency `eur` or `usd` |

### Challenge and proof of ownership

In order to verify ownership of the customers having the control over the provided address. The service needs to generate a new `Challenge` and the user needs to sign a freely chosen message which includes the token of `Challenge` with their private key. The chosen message and the result of the sign message needs to be provided in order for us to verify its correctness.

### Challenge

#### Creating a challenge

Before you can interact with a specific account on our API, you will need to create a new challenge and the retrieved token needs to be included in the message to be signed.

<details>
 <summary><code>POST</code> <code><b>/v1/challenges</b></code></summary>

##### Response

```javascript
{
  "id": "a22d7e45-a8b7-4299-a6e1-ea840ac121e6",
  "token": "0ca13mp9ud",
  "expiresAt": "2023-11-09T04:35:55.111Z",
  "completedAt": null
}
```

</details>

#### Receiving an existing challenge

Should you somehow need to receive an existing challenge again, you can retrieve it by providing its id.

<details>
 <summary><code>GET</code> <code><b>/v1/challenges/{id}</b></code></summary>

##### URL parameter

> | name      |  type     | data type               | description                                                           |
> |-----------|-----------|-------------------------|-----------------------------------------------------------------------|
> | id      |  required | guid   | id of a challenge  |


##### Response

```javascript
{
  "id": "a22d7e45-a8b7-4299-a6e1-ea840ac121e6",
  "token": "0ca13mp9ud",
  "expiresAt": "2023-11-09T04:35:55.111Z",
  "completedAt": null
}
```

</details>

### Order

#### Create an order

A lifetime order representing payment and payout information. You just need to create an order once for a specific exchange pair and that order can be use for a single operation or a standing order.

<details>
 <summary><code>POST</code> <code><b>/v1/orders</b></code></summary>

##### Request body

```javascript
{
  "active": true,
  "affiliateId": "f9c5446d-c181-4bd2-a617-7cc1577f6673",
  "payment": {
    "currency": "eur",
    "userIban": "DE43500105174745259888"
  },
  "payout": {
    "currency": "ada",
    "address": "addr1qy0pzgt024cw4wvyczqrnn26e4eu63c2apjrwlyqacf4ap8fzulm3x467tv5jq5fv8d8vnm0v0xh0rtwl29mv5f9e4csqa78s3",
  },
  "verification": {
    "message": "I confirm my address. (0ca13mp9ud)",
    "signature": "<signed message>",
  }
}
```

`affiliateId` is optional

##### Response

```javascript
{
  "id": "bd221687-0e8e-41d4-868c-e2d878e3024e",
  "active": true,
  "createdAt": "2023-11-09T04:36:55.111Z",
  "updatedAt": "2023-11-09T04:36:55.111Z",
  "affiliateId": "f9c5446d-c181-4bd2-a617-7cc1577f6673",
  "feeRate": 0.025,
  "payment": {
    "currency": "eur",
    "userIban": "DE43500105174745259888"
    "purpose": "",
    "iban": "",
    "bankName": "",
    "bankStreet": "",
    "bankPostalCode": "",
    "bankTown": "",
    "bankCountry": "",
    "bic": "",
    "name": "Rampy",
    "street": "",
    "postalCode": "",
    "town": "",
    "country": "",
  },
  "payout": {
    "currency": "ada",
    "address": "addr1qy0pzgt024cw4wvyczqrnn26e4eu63c2apjrwlyqacf4ap8fzulm3x467tv5jq5fv8d8vnm0v0xh0rtwl29mv5f9e4csqa78s3",
  }
}
```

*Info: empty properties are unknown at the moment.*

</details>

#### Update an order

As orders are lifetime for a customer, you will be able to update `affiliateId` and deactivate or reactivate an existing order

<details>
 <summary><code>PUT</code> <code><b>/v1/orders/{id}</b></code></summary>

##### URL parameter

> | name      |  type     | data type               | description                                                           |
> |-----------|-----------|-------------------------|-----------------------------------------------------------------------|
> | id      |  required | guid   | id of an order  |

##### Request body

```javascript
{
  "active": false,
  "affiliateId": "5dd6139c-18ee-4eb6-b9c5-7de82be5c7bc",
  "verification": {
    "message": "I confirm my address. (0ca13mp9ud)",
    "signature": "<signed message>",
  }
}
```

`affiliateId` is optional

##### Response

```javascript
{
  "id": "bd221687-0e8e-41d4-868c-e2d878e3024e",
  "active": false,
  "affiliateId": "5dd6139c-18ee-4eb6-b9c5-7de82be5c7bc",
  "createdAt": "2023-11-09T04:36:55.111Z",
  "updatedAt": "2023-11-10T08:12:11.111Z",
  "feeRate": 0.025,
  "payment": {
    "currency": "eur",
    "userIban": "DE43500105174745259888"
    "purpose": "",
    "iban": "",
    "bankName": "",
    "bankStreet": "",
    "bankPostalCode": "",
    "bankTown": "",
    "bankCountry": "",
    "bic": "",
    "name": "Rampy",
    "street": "",
    "postalCode": "",
    "town": "",
    "country": "",
  },
  "payout": {
    "currency": "ada",
    "address": "addr1qy0pzgt024cw4wvyczqrnn26e4eu63c2apjrwlyqacf4ap8fzulm3x467tv5jq5fv8d8vnm0v0xh0rtwl29mv5f9e4csqa78s3",
  }
}
```

*Info: empty properties are unknown at the moment.*

</details>

#### Receiving an existing order

<details>
 <summary><code>GET</code> <code><b>/v1/orders/{id}</b></code></summary>

##### URL parameter

> | name      |  type     | data type               | description                                                           |
> |-----------|-----------|-------------------------|-----------------------------------------------------------------------|
> | id      |  required | guid   | id of an order  |

##### Response

```javascript
{
  "id": "bd221687-0e8e-41d4-868c-e2d878e3024e",
  "active": false,
  "affiliateId": "5dd6139c-18ee-4eb6-b9c5-7de82be5c7bc",
  "createdAt": "2023-11-09T04:36:55.111Z",
  "updatedAt": "2023-11-10T08:12:11.111Z",
  "feeRate": 0.025,
  "payment": {
    "currency": "eur",
    "userIban": "DE43500105174745259888"
    "purpose": "",
    "iban": "",
    "bankName": "",
    "bankStreet": "",
    "bankPostalCode": "",
    "bankTown": "",
    "bankCountry": "",
    "bic": "",
    "name": "Rampy",
    "street": "",
    "postalCode": "",
    "town": "",
    "country": "",
  },
  "payout": {
    "currency": "ada",
    "address": "addr1qy0pzgt024cw4wvyczqrnn26e4eu63c2apjrwlyqacf4ap8fzulm3x467tv5jq5fv8d8vnm0v0xh0rtwl29mv5f9e4csqa78s3",
  }
}
```

*Info: empty properties are unknown at the moment.*

</details>

### Operation

An operation mirrors one execution of an order. This operation can have different states like processing, completed, refunded or interrupted.

One possible reason for interrupted state would be that it will require an additional KYC step, if certain legal and regulatory thresholds are exceeded.

You will not be able to create or update an operation via API. Operation are created based on actions by the customers. For buys it would be if a new SEPA transaction associated with the customers' orders' purpose got recognized by our bank account. Or for sells if the customer has sent the crypto currencies to his deposit address.

There are two different ways on how to retrieve operations.

1. all operations for a specific customer
2. all operations for a specific order

#### Receiving orders for a customer

In order to identify the customer and be able to retrieve all operations for a customer - address, a custom message and its signature will be needed. Due to GET those parameters need to be provided as query parameters.

<details>
 <summary><code>GET</code> <code><b>/v1/operations</b></code></summary>

##### Query parameter

> | name      |  type     | data type               | description                                                           |
> |-----------|-----------|-------------------------|-----------------------------------------------------------------------|
> | address      |  required | string   | address of the customer  |
> | message      |  required | string   | custom message containing a challenge  |
> | signature      |  required | string   | signature of the provided message  |

##### Response processing

```javascript
[{
  "id": "c2741bc9-b280-4b85-ac8f-16ec8d4b77a9",
  "orderId": "18f3b65f-4bd4-426f-8a23-dc2de1cde7e6",
  "feeRate": 0.025,
  "pair": "ada/eur",
  "type": "buy",
  "state": "processing",
  "cost": 100.00,
  "createdAt": "2023-11-12T08:11:55.111Z",
  "updatedAt": "2023-11-12T08:12:11.111Z",
  "amount": 257.73195876,
  "rate": 0.388,
  "fee": 2.50,
  "interruptInfo": null,
  "refundInfo": null,
  "payout": null,
}]
```

##### Response completed

```javascript
[{
  "id": "c2741bc9-b280-4b85-ac8f-16ec8d4b77a9",
  "orderId": "18f3b65f-4bd4-426f-8a23-dc2de1cde7e6",
  "feeRate": 0.025,
  "pair": "ada/eur",
  "type": "buy",
  "state": "completed",
  "cost": 100.00,
  "createdAt": "2023-11-12T08:11:55.111Z",
  "updatedAt": "2023-11-12T08:14:11.111Z",
  "amount": 257.73195876,
  "rate": 0.388,
  "fee": 2.50,
  "interruptInfo": null,
  "refundInfo": null,
  "payout": {
    "txId": "09ae8be8147d47519036fff7babb350b1cf66219973f475aa3e763089b19c161",
    "address": "addr1qy0pzgt024cw4wvyczqrnn26e4eu63c2apjrwlyqacf4ap8fzulm3x467tv5jq5fv8d8vnm0v0xh0rtwl29mv5f9e4csqa78s3",
    "blockHeight": null,
    "confirmed": false,
    "fee": 0.00003123,
    "amount": 257.73195876
  }
}]
```

##### Response interrupted

```javascript
[{
  "id": "c2741bc9-b280-4b85-ac8f-16ec8d4b77a9",
  "orderId": "18f3b65f-4bd4-426f-8a23-dc2de1cde7e6",
  "feeRate": 0.025,
  "pair": "ada/eur",
  "type": "buy",
  "state": "interrupted",
  "cost": 100.00,
  "createdAt": "2023-11-12T08:11:55.111Z",
  "updatedAt": "2023-11-12T08:12:11.111Z",
  "amount": null,
  "rate": null,
  "fee": null,
  "interruptInfo": {
    "code": "kyc_required",
    "id": "22b69a5c-2585-40ec-a95b-a28518b84215"
  },
  "refundInfo": null,
  "payout": null,
}]
```

##### Response refunded

```javascript
[{
  "id": "c2741bc9-b280-4b85-ac8f-16ec8d4b77a9",
  "orderId": "18f3b65f-4bd4-426f-8a23-dc2de1cde7e6",
  "feeRate": 0.025,
  "pair": "ada/eur",
  "type": "buy",
  "state": "refunded",
  "cost": 100.00,
  "createdAt": "2023-11-12T08:11:55.111Z",
  "updatedAt": "2023-11-12T08:12:11.111Z",
  "amount": 257.73195876,
  "rate": 0.388,
  "fee": 2.50,
  "interruptInfo": null,
  "refundInfo": {
    "code": "customExceededThreshold"
  },
  "payout": null,
}]
```

</details>

#### Receiving orders for an order

In order to identify the customer and be able to retrieve operations for an order - address, a custom message and its signature will be needed. Due to GET those parameters need to be provided as query parameters.

<details>
 <summary><code>GET</code> <code><b>/v1/orders/{id}/operations</b></code></summary>

##### URL parameter

> | name      |  type     | data type               | description                                                           |
> |-----------|-----------|-------------------------|-----------------------------------------------------------------------|
> | id      |  required | guid   | id of an order  |

##### Query parameter

> | name      |  type     | data type               | description                                                           |
> |-----------|-----------|-------------------------|-----------------------------------------------------------------------|
> | address      |  required | string   | address of the customer  |
> | message      |  required | string   | custom message containing a challenge |
> | signature      |  required | string   | signature of the provided message  |

</details>

### Payment requests

You are able to create a more typical payment process. This request can be handled in crypto, in fiat or in a mix of both. To be able to create a new payment, you will need to verify that you are the owner of the receiving address and you will need a verification of the actual user, who is buying.

Those verification objects are the same as in all our other endpoints consisting of address, message and signature. This means that for this call you will need two distinct challenges.

A callback URL needs to be provided, where we will post updates via HTTP requests.

The actual payment request needs to be defined in fiat currency and can be further specified by providing a defined split with percentages. Currently we would only support 100% ADA. In future you will be able to provide your split whatever it shall be - 80/20, 60/40 and so on...

#### Creating a payment request

<details>
 <summary><code>POST</code> <code><b>/v1/payment-requests</b></code></summary>

##### Request body

```javascript
{
  "active": true,
  "callbackUrl": "https://your.callback.url.service/",
  "payment": {
    "currency": "eur",
    "userIban": "DE43500105174745259888"
  },
  "receiver": {
    "address": "<receiver address>",
    "message": "I confirm my address. (2apjrxlyqa)",
    "signature": "<signed message>",
  },
  "requests": {
    "amount": 800,
    "currency": "eur",
    "split": {
      "ada": 1,
      "fiat": 0
    }
  },
  "verification": {
    "address": "<user address",
    "message": "I confirm my address. (0ca13mp9ud)",
    "signature": "<signed message>",
  }
}
```

##### Response

```javascript
{
  "id": "bd221687-0e8e-41d4-868c-e2d878e3024e",
  "active": true,
  "createdAt": "2023-11-09T04:36:55.111Z",
  "updatedAt": "2023-11-09T04:36:55.111Z",
  "status": "initiated",
  "paymentFiat": {
    "currency": "eur",
    "userIban": "DE43500105174745259888"
    "purpose": "",
    "iban": "",
    "bankName": "",
    "bankStreet": "",
    "bankPostalCode": "",
    "bankTown": "",
    "bankCountry": "",
    "bic": "",
    "name": "Rampy",
    "street": "",
    "postalCode": "",
    "town": "",
    "country": "",
  },
  "paymentCrypto": {
    "address": "<receiver address>",
    "amount": 1333,
    "token": "ada"
  }
  "payout": {
    "address": "<user address>",
  }
}
```

*Info: empty properties are unknown at the moment.*

</details>

#### Receiving a payment request

In order to identify the client and be able to retrieve initiated payment requests - address, a custom message and its signature will be needed. Due to GET those parameters need to be provided as query parameters.

Possible status of a payment request
> | status      |  description     |
> |-----------|-----------|
> | initiated      |  created and waiting for the user to complete the payment     |
> | processing      |  payment received and callback URL called     |
> | error      |  something has gone wrong and client got informed via callback URL     |
> | completed      |  client has completed their part of the transaction     |

<details>
 <summary><code>GET</code> <code><b>/v1/payment-requests/{id}</b></code></summary>

##### URL parameter

> | name      |  type     | data type               | description                                                           |
> |-----------|-----------|-------------------------|-----------------------------------------------------------------------|
> | id      |  required | guid   | id of a payment request  |

##### Query parameter

> | name      |  type     | data type               | description                                                           |
> |-----------|-----------|-------------------------|-----------------------------------------------------------------------|
> | address      |  required | string   | address of the customer  |
> | message      |  required | string   | custom message containing a challenge |
> | signature      |  required | string   | signature of the provided message  |

##### Response

```javascript
[{
  "id": "bd221687-0e8e-41d4-868c-e2d878e3024e",
  "active": true,
  "createdAt": "2023-11-09T04:36:55.111Z",
  "updatedAt": "2023-11-09T04:36:55.111Z",
  "status": "processing",
  "paymentFiat": {
    "currency": "eur",
    "userIban": "DE43500105174745259888"
    "purpose": "",
    "iban": "",
    "bankName": "",
    "bankStreet": "",
    "bankPostalCode": "",
    "bankTown": "",
    "bankCountry": "",
    "bic": "",
    "name": "Rampy",
    "street": "",
    "postalCode": "",
    "town": "",
    "country": "",
  },
  "paymentCrypto": {
    "address": "<receiver address>",
    "amount": 1333,
    "token": "ada"
  }
  "payout": {
    "address": "<user address>",
  }
}]
```

*Info: empty properties are unknown at the moment.*

</details>

#### Completing a payment request

As soon as a payment request has its status updated to processing. We will call the associated callback URL with needed information. This information is currently TBD.

A payment request in status processing can be marked as completed, after the client has finished their process, which actually completes the payment transaction.

<details>
 <summary><code>POST</code> <code><b>/v1/payment-requests/{id}/complete</b></code></summary>

##### Request body

```javascript
{
  "receiver": {
    "address": "<receiver address>",
    "message": "I confirm my address. (2apjrxlyqa)",
    "signature": "<signed message>",
  }
}
```

##### Response

```javascript
{
  "id": "bd221687-0e8e-41d4-868c-e2d878e3024e",
  "active": true,
  "createdAt": "2023-11-09T04:36:55.111Z",
  "updatedAt": "2023-11-09T04:36:55.111Z",
  "status": "completed",
  "paymentFiat": {
    "currency": "eur",
    "userIban": "DE43500105174745259888"
    "purpose": "",
    "iban": "",
    "bankName": "",
    "bankStreet": "",
    "bankPostalCode": "",
    "bankTown": "",
    "bankCountry": "",
    "bic": "",
    "name": "Rampy",
    "street": "",
    "postalCode": "",
    "town": "",
    "country": "",
  },
  "paymentCrypto": {
    "address": "<receiver address>",
    "amount": 1333,
    "token": "ada"
  }
  "payout": {
    "address": "<user address>",
  }
}
```