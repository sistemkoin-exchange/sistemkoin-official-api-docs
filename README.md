# Public API Documentation

You can use our API to access public data, which can get information.

## Table of Contents
- [Usage](#usage)
- [Ticker](#market-ticker)
- [Order Book](#order-book)
- [Trade History](#trade-history)

## Usage

**The base endpoint is:**
```
https://api.sistemkoin.com
```

## Market Ticker

**Request &#x25BC;**

```
GET /ticker
```

**Response &#x25BC;**

``` javascript
{
    "EURO": {
        "BTC": {
            "symbol": "BTC",                // Symbol of the currency
            "high": "10384.00000000",       // Highest price in last 24 hours
            "low": "10019.00000000",        // Lowest price in last 24 hours
            "askPrice": "11685.00000000",   // Lowest current ask price
            "bidPrice": "8123.00000000",    // Highest current bid price
            "current": "10360.00000000",    // Last Price
            "volume": "30002.78711068",     // Total volume in last 24 hours
            "changeAmount": "225.00000000", // Change in last 24 hours
            "changePercentage": "2.22"      // Change percentage in last 24 hours
        }
    }
}
```

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
