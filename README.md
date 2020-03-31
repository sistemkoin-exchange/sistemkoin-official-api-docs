## Table of Contents
- [General API Information](#general-API-information)
- [General API Information on Endpoints](#general-information-endpoints)
- [Limits](#limits)
- [Endpoint security type](#endpoint-security-type)
- [Signed Endpoint Security](#signed-endpoint-security)
- [Timing Security](#timing-security)
- [Http Status Codes](#http-status-codes)
- [Error Messages](#error-messages)
- [Permission Scopes](#permission-scopes)

# Public Rest API Documentation for Sistemkoin (v1)

## General API Information
- Base endpoint is : ``https://api.sistemkoin.com/api/``
- All endpoints return either a **JSON object** or **Array**
- All time and  timestamp related fields are in **millisecond**

## General Information Endpoints
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
- **Repeatedly violating rate limits and/or failing to back off after receiving 429s will result in an automated API Key ban (``HTTP 418 [I'm a Teapot]``)**
- API Key bans are tracked and **scale in duration** for repeat offenders, **from 3 minutes to 3 days**
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

## Http Status Codes
- HTTP ``4XX`` return codes are used for malformed requests; the issue is on the sender's side
- HTTP ``401`` return code is used when **wrong api key**
- HTTP ``418`` return code is when breaking a request rate limit
- HTTP ``429`` return code is used when an API Key has been auto-banned for continuing to send requests after receiving 429 codes.
- HTTP ``5XX`` return codes are used for internal errors; the issue is on Sistemkoin's side

## Error Messages
- ``request_timeout`` - This message is given when time security could not be passed
- ``incorrect_data_format`` - This message is given when ``testSignature`` endpoint fails
- ``this_area_not_allowed`` - This message is given when API Key does not have required scope permission
- ``market_not_found`` - This message is given when market not found
- ``cant_receive_fee_information`` - This error is given if an unexpected error is encountered during the calculation of user fee rates
- ``socket_is_busy`` - This error is given when the service is too busy when going through the validation check to join the socket connection
- ``amount_must_be_above_zero`` - This error is given when the trade amount is equal to or below zero
- ``socket_is_busy`` - This error is given when the service is too busy when going through the validation check to join the socket connection
- ``there_is_no_pair_value`` - This error is given when given tade pair not found
- ``you_can_not_trade_below_minimum_trade_amount`` - This error is given when trade amount is minimum permitted trade amount
- ``transaction_is_not_available_right_now_for_this_coin`` - This error is given when one of the coins is not available for trading
- ``trade_limits_exceeded`` - This error is given when trade price is above or below current rate
- ``transaction_is_not_available_right_now_for_this_coin`` - This error is given when one of the coins is not available for trading
- ``do_not_have_currency`` - This error is given when user does not have the coin to be sold
- ``do_not_have_enough_balance`` - This error is given when user does not have enough balance for the coin to be sold
- ``do_not_have_currency`` - This error is given when user does not have the coin to be sold
- ``trade_failed`` - This error is given when trade failed due to an error on the api side

## Permission Scopes
- ``trade`` - Required scope to create, delete order
- ``show_balances`` - Required scope to get user balances
- ``show_account_info`` - Required scope to get user & profile information
- ``show_history`` - Required scope to get order history & trade history


## SIGNED Endpoint Examples for POST /api/v1/market
Here is a step-by-step example of how to send a valid signed payload from the Linux command line using echo, openssl, and curl.

This is also the example ofg making an order
```
POST /api/v1/market
```
| Parameter  | Value |
| ------------- | ------------- |
| symbol  | BTCTRY  |
| type  | BUY  |
| amount| 1  |
| price		  | 0.1  |
| recvWindow		  | 5000  |
| timestamp		  | 1499827319559  |


## Example 1: As a request body

- ##### requestBody:

```
symbol=BTCTRY&type=BUY&amount=1&price=0.1&recvWindow=5000&timestamp=1499827319559
```

- ##### HMAC SHA256 signature:

```
[linux]$ echo -n "symbol=BTCTRY&type=SELL&amount=1&price=49000&recvWindow=5000&timestamp=2573117658 | openssl dgst -sha256 -hmac "9d3fcd92dfc8590773cb38ade681f8ec9cfe1b511f6508c49681da88167df422"
(stdin)= 6616471dd1bd11caa6363fbc52efc1d37e82ce7fa678328d3c7168e5c6a7ace8
```

-  #####  curl command:

> (HMAC SHA256)
```
[linux]$ curl -H "X-STK-ApiKey: [API_KEY]" -X POST 'https://api.sistemkoin.com/api/v1/market' -d 'symbol=BTCTRY&type=SELL&amount=1&price=49000&recvWindow=5000&timestamp=2573117658&signature=6616471dd1bd11caa6363fbc52efc1d37e82ce7fa678328d3c7168e5c6a7ace8'
```

## Example 2: As a query string
- ##### queryString:
```
symbol=BTCTRY&type=SELL&amount=1&price=49000&recvWindow=5000&timestamp=2573117658&signature=6616471dd1bd11caa6363fbc52efc1d37e82ce7fa678328d3c7168e5c6a7ace8
```

- ##### HMAC SHA256 signature:

```
[linux]$ echo -n "symbol=BTCTRY&type=SELL&amount=1&price=49000&recvWindow=5000&timestamp=2573117658" | openssl dgst -sha256 -hmac "9d3fcd92dfc8590773cb38ade681f8ec9cfe1b511f6508c49681da88167df422"
(stdin)= 6616471dd1bd11caa6363fbc52efc1d37e82ce7fa678328d3c7168e5c6a7ace8
```

- ##### curl command:

> (HMAC SHA256)
```
[linux]$ curl -H "X-STK-ApiKey: [API_KEY]" -X POST 'https://api.sistemkoin.com/api/v1/market?symbol=BTCTRY&type=SELL&amount=1&price=49000&recvWindow=5000&timestamp=2573117658&signature=6616471dd1bd11caa6363fbc52efc1d37e82ce7fa678328d3c7168e5c6a7ace8'
```

- Terminology base asset refers to the asset that is the quantity of a symbol. quote asset refers to the asset that is the price of a symbol. ENUM definitions Symbol status (status):

### Status
* PARTIALLY_FILLED
* FILLED
* CANCELED

### Market Types
* BUY
* SELL


### Cancel Open Order (Require Signature)


```
DELETE /api/v1/market
```

- Users can delete their orders from delete endpoint.

| Parameter  | Value |
| ------------- | ------------- |
| orderID | {order_id} |
| signature | [SIGNATURE]

- #### Request:

```
{
    "orderID": "{order_id}",
    "signature": "{SIGNATURE}"
}
```

- #### Response:
```
{
    "status": "success|failure"
}
```

### Account Order History

```
GET /api/v1/account/orders
```

Request filters;
```
symbol  : STRING     : Pair symbol
status  : ENUM       : PENDING, PARTIALLY_FILLED, FILLED
side    : ENUM       : asks, bids
```

- Endpoint will give user's order history

#### Response Details

| Name | Type |  Description |
| ------------- | ------------- | ------------- |
|id             | INT  | ID of the trade |
|coin           | INT  | Traded Coin |
|pairCoin       | INT  | Traded Pair Coin|
|pair           | INT  | Symbol |
|amount         | FLOAT  | Traded Amount |
|price          | INT  | Traded Price|
|coinPrecision  | INT  | Traded Coin's Decimal Number|
|pairCoinPrecision | INT  | Traded Pair Coin's Decimal Number|
|isActive       | INT  | Pair's status|


#### Response Example

```
{
    "status": "success",
    "data" : [
         {
             "id": 10000
             "coin": "BTC"
             "pairCoin": "TRY"
             "pair": "BTCTRY"
             "average": "70010.00000000"
             "triggerPrice": "70010.00000000"
             "remainingAmount": "0.24583160"
             "amount": "1.00000000"
             "orderStatus": "PENDING"
             "coinPrecision": 8
             "pairCoinPrecision": 2
             "isActive": true
         },
         {
             "id": 10001
             "coin": "BTC"
             "pairCoin": "TRY"
             "pair": "BTCTRY"
             "average": "70010.00000000"
             "triggerPrice": "70010.00000000"
             "remainingAmount": "0.1"
             "amount": "1.00000000"
             "orderStatus": "FILLED"
             "coinPrecision": 8
             "pairCoinPrecision": 2
             "isActive": true
         }
    ]
}
```



### Account Trade History

```
GET /api/v1/account/trades
```

- Endpoint will give user's trade history

#### Response Details

| Name | Type |  Description |
| ------------- | ------------- | ------------- |
|id             | INT  | ID of the trade |
|coin           | INT  | Traded Coin |
|pairCoin       | INT  | Traded Pair Coin|
|pair           | INT  | Symbol |
|amount         | INT  | Traded Amount |
|price          | INT  | Traded Price|
|coinPrecision  | INT  | Traded Coin's Decimal Number|
|pairCoinPrecision | INT  | Traded Pair Coin's Decimal Number|
|isActive       | INT  | Pair's status|


#### Response Example

```
{
    "status": "success",
    "data": [
      {
        "id": 2627
        "coin": "XVG"
        "pairCoin": "EUR"
        "pair": "XVGEUR"
        "amount": "1000.00000000"
        "price": "0.12300000"
        "coinPrecision": 2
        "pairCoinPrecision": 6
        "isActive": true
      },
      {
        "id": 2628
        "coin": "XVG"
        "pairCoin": "EUR"
        "pair": "XVGEUR"
        "amount": "1000.00000000"
        "price": "0.12300000"
        "coinPrecision": 2
        "pairCoinPrecision": 6
        "isActive": true
      }
    ]
}
```

### Account Balances (Require Signature)

```
GET /api/v1/account/balance
```

| Parameter  | Value |
| ------------- | ------------- |
| symbol | BTC |

- Endpoint will give user's wallet balances

**Request**

```
{
    "symbol": "BTC"
}
```

#### Response Example

```
 {
    "status": "success",
    "data": [
      {
        "symbol": "BTC",
        "amount": "2116.28330195",
        "reservedAmount": "2.89975867",
        "lendingAmount": "0.00000000",
      }
    ]
 }
```

### Get Open Orders

```
GET /api/v1/market
```

- Get open Market Orders by pair and limit

| Name | Type | Mandator | Description |
| ------------- | ------------- | ------------- | ------------- |
| symbol | STRING  | YES | SYMBOL OF THE PAIR |
| limit  | INT  | YES | LIMIT OF THE ORDERS API |

- You can get symbols from .... endpoint
- Limits are 10, 25, 50

#### Response Details

| Name | Type |  Description |
| ------------- | ------------- | ------------- |
|AMOUNT       | STRING | Pair's status|
|PRICE      | STRING  | Pair's status|

#### Response Example

```
{
  "status": "success",
  "timestamp": 1576506570,
  "bids": [
    {
      "price": "72010.00000000",
      "amount": "2.42000000"
    },
    {
      "price": "71010.00000000",
      "amount": "0.24583160"
    },
    {
      "price": "70010.00000000",
      "amount": "0.24583160"
    }
  ],
  "asks": [
    {
      "price": "77000.00000000",
      "amount": "3.10000000"
    },
    {
      "price": "78000.00000000",
      "amount": "9.80000000"
    },
    {
      "price": "79000.00000000",
      "amount": "4.90000000"
    },
    {
      "price": "80000.00000000",
      "amount": "4.90000000"
    }
  ]
}
```
### Get Last Trades

```
GET /api/v1/trade
```

- Endpoint gives pair's trade history

| Name | Type | Mandator | Description |
| ------------- | ------------- | ------------- | ------------- |
| symbol | STRING  | YES | SYMBOL OF THE PAIR |
| limit  | INT  | YES | LIMIT OF THE ORDERS API |

- You can get symbols from .... endpoint
- Limits are 10, 25, 50

#### Response Details

| Name | Type |  Description |
| ------------- | ------------- | ------------- |
|PRICE       | STRING | Price of Trade |
|VOLUME      | STRING  | Amount of the traded coin|
|FUNDS      | STRING  | Total amount of Pair Coin's Trade |
|SIDE      | STRING  | BID OR ASK|
|TIMESTAMP      | INTEGER  | Time of the Trade|

#### Response Example

```
{
  "status": "success",
  {
    "data": [
      {
        "price": "49000.00000000",
        "volume": "1.00000000",
        "funds": "49000.00000000",
        "side": "bid",
        "timestamp": 1577099182
      },
      {
        "price": "48000.00000000",
        "volume": "2.00000000",
        "funds": "96000.00000000",
        "side": "bid",
        "timestamp": 1577099181
      },
    ]
```



### Connecting To Socket

```
POST /api/broadcast/channel
```

User can get open orders, trades and wallet balance updates from socket connection.

To get an authentication for the socket connection you must send a post request to the above endpoint to get  your keys

#### Response Example

```
{
    "status":"success",
    "data":{
        "auth":"{SOCKET_AUTH}",
        "channel":"{CHANNEL}",
        "key":"{SOCKET_AUTH_KEY}"
    }
}
```


With key and auth you can subscribe to the channel.

- **Domain** : services.sistemkoin.com
- **Port** : 17032

User can join public channel whenever he/she wants.

### Test Signature (Require Signature)

```
POST /api/testSignature
```

Endpoint is for checking the validation of signature.

- ##### Request Example:

```
{
    "name": "Foo",
    "surname": "Bar",
    "location": "Milkyway Galaxy, Planet Earth"
}
```

- ##### Response Example

```
 {
     "status": "success"
     "data": {
         "payload": {
             "name": "Foo",
             "surname": "Bar",
             "location": "Milkyway Galaxy, Planet Earth",
             "timestamp": "1576500171",
             "recvWindow": "5000",
             "signature": "db9b4ab53f23d8559675f478b3ff5ad84ac4dea9961ac577d96c90b310e0433a"
         },
         "payloadString": "name=Foo&surname=Bar&location=Milkyway+Galaxy%2C+Planet+Earth&timestamp=1576500171&recvWindow=5000&signature=db9b4ab53f23d8559675f478b3ff5ad84ac4dea9961ac577d96c90b310e0433a",
         "isAccepted": true
     }
 }
```

### Ping The System

```
GET /api/ping
```

Endpoint is for checking the if the user can communicate with the api.

```
 {
    "status": "success"
    "data": {
      "message": "pong"
    }
 }
```

### Get System Time

```
GET /api/systime
```

Endpoint is for checking the if the user can communicate with the api.

```
 {
    "status": "success"
    "data": {
      'timestamp' : 1573397426,
      'dateTimeString' => 2019-10-11 14:51:42,
    }
 }
```

### MARKET PAIRS

```
GET /api/v1/market/pairs
```

- This endpoint returns pair infos

```
 {
    "status": "success"
    "data": {
      'id'                     : 1,
      'change'                 : 2,
      'changeDirection'        : 1,
      'changeValue'            : "1000",
      'coinName'               : "Bitcoin",
      'coinSymbol'             : "BTC",
      'numberOfDigitsCoin'     : 8,
      'numberOfDigitsPairCoin' : 2,
      'pairCoinSymbol'         : "TRY",
    }
 }
```

### TICKER DATA

```
GET /api/v1/market/ticker
```

- This endpoint returns pairs' market data

```
 {
    "status": "success"
    "data": {
        "TRY": {
            "DEEX": {
                "symbol": "DEEX",
                "high": "0.076173",
                "low": "0.070125",
                "askPrice": "0.09000000",
                "bidPrice": "0.08100000",
                "current": "0.076173",
                "volume": "4097.997093",
                "changeAmount": "0.00531600",
                "changePercentage": "7.50"
                 },
            "IGG": {
                "symbol": "IGG",
                "high": "0.000503",
                "low": "0.000500",
                "askPrice": "0.00060000",
                "bidPrice": "0.00022600",
                "current": "0.000503",
                "volume": "8361.999966",
                "changeAmount": "0.00000200",
                "changePercentage": "0.40"
    }
  }
}
```

### PROFILE DETAILS

```
GET /api/v1/account/details
```

- This endpoint returns profile info

```
 {
    "status": "success"
    "data": {
        email                : "fo@bar.com"
        name                 : "Foo"
        surname              : "Bar"
        locale               : "{LOCALE}"
        refCode              : "3432432"
        phone                : "+905*********"
        verified             : 1
        usePinCode           : 1
        lastSessionIP        : "170.**.***.**"
        useStkCoinForPayFees : 1
   }
 }
```

### GET USER FEES

```
GET /api/v1/coins/fee
```

- This endpoint returns user's fee details for specific coin in percentage

```
 {
    "status": "success"
    "data": {
        buyFee  : 1
        sellFee : 1
   }
 }
```

