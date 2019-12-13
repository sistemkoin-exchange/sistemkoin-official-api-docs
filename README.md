## Table of Contents
- [General API Information](#general-api-information)
- [General API Information on Endpoints](#general-api-information-on-endpoints)
- [Limits](#limits)
- [Error Messages](#error-messages)
- [Ticker](#market-ticker)
- [Order Book](#order-book)
- [Trade History](#trade-history)

# Public Rest API Documentation for Sistemkoin (v1)

## General API Information
- Base endpoint is : ``https://api.sistemkoin.com/``
- All endpoints return either a **JSON object** or **Array**
- All time and  timestamp related fields are in **millisecond**

## General Information on Endpoints
- All endpoints contain the **status** field. This will tell you the success of your request, whether ``success`` or ``failure``.
- As a result of all successful requests, endpoints respond in a fixed structure.

Sample successful request payload:
```
{
    "status": "success",
    "data": {...}
}
```


Sample failure request payload:
```
{
    "status": "failure",
    "message": "{ERROR_MESSAGE_KEY}"
}
```

- For ``GET`` endpoints, parameters **must** be send as a ``query string``
- For ``POST``, ``DELETE``, ``PATCH``, ``PUT`` endpoints, the parameters may be send as a ``query string`` or in the ``request body``. You may mix parameters between both the ``request body`` and ``query string``
- Parameter orders is unimportant in **unsigned** endpoints

## Limits
- Currently request limit is : ``2 Request per second``
- When server returns ``Http 429 [Too many request]``, it's your obligation as an API to back off and not spam the API
- **Repeatedly violating rate limits and/or failing to back off after receiving 429s will result in an automated IP ban (``HTTP 418 [I'm a Teapot]``)**
- IP bans are tracked and **scale in duration** for repeat offenders, **from 3 minutes to 3 days**
- A ``Retry-After`` header is sent with a 418 responses and will give the number of seconds required to wait.
- **The limits on the API are based on the API-KEY, not the IP addresses**

## Endpoint security type
- API Keys & Secrets are **case-sensitive**
- Each endpoint requires **API Key** via ``X-STK-ApiKey``
- API Keys require ``scope permission`` for some endpoints

## Signed Endpoint Security
- ``SIGNED`` endpoints require an additional parameter, ``signature``, to be sent in the ``query string`` or ``request body``
- Endpoints use ``HMAC SHA256`` signatures. The ``HMAC SHA256`` signature is a keyed ``HMAC SHA256`` operation. Use your ``apiSecret`` as the key and totalParams as the value for the HMAC operation

## Timing Security
- A ``SIGNED`` endpoint also requires a parameter, ``timestamp``, to be sent which should be the millisecond timestamp of when the request was created and sent.
- An additional parameter, ``recvWindow``, may be sent to specify the number of milliseconds after ``timestamp`` the request is valid for. If ``recvWindow`` is not sent, **it defaults to 5000**




## Order Book

**Request &#x25BC;**

```
GET /orderbook?symbol=BTCTRY&limit=10
```

**Parameters &#x25BC;**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES | Market Pair Symbol (ex : BTCTRY)
limit | INT | YES | max 50. Valid limits : [10, 25, 50]

**Response &#x25BC;**

``` javascript
{
    "timestamp": 1562923070,
    "bids": [
        [
          "66632.00000000",    // Price
          "0.15342861"         // Amount
         ],
    ]
    "asks": [
        [
          "67303.00000000",    // Price
          "0.21174063"         // Amount
        ],
    ]
```

## Trade History

**Request &#x25BC;**

```
GET /tradehistory?symbol=BTCTRY&limit=25
```

**Parameters &#x25BC;**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES | Market Pair Symbol (ex : BTCTRY)
limit | INT | YES | max 50. Valid limits : [10, 25, 50]

**Response &#x25BC;**

``` javascript
{
    "data": [
        {
            "price": "67345.00000000",  // Price
            "volume": "0.03077852",     // Amount
            "funds": "2072.77942940",   // Total Amount
            "side": "bid",              // Transaction type (bid/ask)
            "timestamp": 1562923268     // Trade Time (local)
        },
    ]
}
```
