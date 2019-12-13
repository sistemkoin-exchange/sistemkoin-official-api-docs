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

## Http Status Codes
- HTTP ``4XX`` return codes are used for malformed requests; the issue is on the sender's side
- HTTP ``401`` return code is used when **wrong api key**
- HTTP ``418`` return code is when breaking a request rate limit
- HTTP ``429`` return code is used when an API Key has been auto-banned for continuing to send requests after receiving 429 codes.
- HTTP ``5XX`` return codes are used for internal errors; the issue is on Sistemkoin's side

## Error Messages
- ``request_timeout`` - This message is given when time security is not exceeded
- ``incorrect_data_format`` - This message is given when ``testSignature`` endpoint failure result
- ``this_area_not_allowed`` - This message is given when API Key is not have required scope permission
- ``market_not_found`` - This message is given when market not found
- ``cant_receive_fee_information`` - This error is given if an unexpected error is encountered during the calculation of user fee rates
- ``socket_is_busy`` - This error is given when the service is too busy when going through the validation check to join the socket connection

## Permission Scopes
- ``trade`` - Required scope for create, delete order
- ``show_balances`` - Required scope for get user balances
- ``show_account_info`` - Required scope for get user & profile information
- ``show_history`` - Required scope for get order history & trade history
