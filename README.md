<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Public Rest API From Cryptochassis](#public-rest-api-from-cryptochassis)
  - [General API Information](#general-api-information)
  - [Reqeust Rate Limits](#reqeust-rate-limits)
  - [Public API Endpoints](#public-api-endpoints)
    - [Information](#information)
    - [Market Depth](#market-depth)
    - [Quote](#quote)
    - [Trade](#trade)
    - [OHLC](#ohlc)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Public Rest API From Cryptochassis
* This API provides snapshot data (e.g. market depth, quote), tick data (e.g. trades), and aggregated data (e.g. ohlc) for crypto-currencies.
* Supported currencies: btc, eth, ltc, xrp, bch, eos.
* Supported spot exchanges: coinbase, gemini, kraken, bitstamp, bitfinex, binance-us, binance, huobi, okex.
* Supported derivatives: bitmex (xbtusd), binance-futures (btcusdt).
* Backfill history: 5 years historical data, wherever applicable.
* Institutional quality and independently verifiable.
* To spur innovation and industry collaboration, this API is open for use by the public without cost. Follow us on https://medium.com/@cryptochassis and our publication on https://medium.com/open-crypto-market-data-initiative.
* Please contact us for general questions, issue reporting, consultative services, and/or custom engineering work. To subscribe to our mailing list, simply send us an email with subject "subscribe".

## General API Information
* The base endpoint is: **https://api.cryptochassis.com/v1**

## Reqeust Rate Limits
* The limit on the API is based on public IPs: 10 requests per second per IP.

## Public API Endpoints

### Information
```
GET /information?dataType=<dataType>&exchange=<exchange>&instrument=<instrument>
```
Information about available data types, exchanges, instruments, and the available time range.

**Parameters:**

Name | Mandatory | Description
------------ | ------------ | ------------
dataType | no | Comma seperated list. Allowed values: market-depth, quote, trade, ohlc.
exchange | no | Comma seperated list. Allowed values: coinbase, gemini, kraken, bitstamp, bitfinex, bitmex, binance-us, binance, binance-futures, huobi, okex.
instrument | no | Comma seperated list. Allowed values: btc-usd(t), eth-usd(t), ltc-usd(t), xrp-usd(t), bch-usd(t), eos-usd(t), xbtusd, btcusdt.

**Response:**
```javascript
[
  {
      "dataType": "quote",
      "exchange": "coinbase",
      "instrument": "btc-usd",
      "availability": {
          "firstAvailableTime": {
              "seconds": 1572480000,
              "iso": "2019-10-31T00:00:00.000Z"
          },
          "lastAvailableTime": {
              "seconds": 1577318400,
              "iso": "2019-12-26T00:00:00.000Z"
          }
      },
      "examples": [
          "https://api.cryptochassis.com/v1/quote/coinbase/btc-usd"
      ]
  },
  ...
]
```

**Examples:**

https://api.cryptochassis.com/v1/information

### Market Depth
```
GET /market-depth/<exchange>/<instrument>?depth=<depth>&startTime=<startTime>
```
Daily 1-second snapshot data on market depth (aka order books or Level 2 data) up to a depth of 10.

**Parameters:**

Name | Mandatory | Description
------------ | ------------ | ------------
exchange | yes | E.g. coinbase.
instrument | yes | E.g. btc-usd.
depth | no | Allowed values: 1, 10. Defaults to 10.
startTime | no | E.g. 1594166400 (seconds), 2020-07-08 (iso). Defaults to most recent.

**Response:**
```javascript
{
    "urls": [
        {
            "startTime": {
                "seconds": 1594166400, // unix time
                "iso": "2020-07-08T00:00:00.000Z"
            },
            "url": "https://marketdata-e0323a9039add2978bf5b49550572c7c.s3.amazonaws.com/market_depth/bitfinex/btc_usd/1594166400.csv.gz?AWSAccessKeyId=AKIATPNB7YZIUQR3JVNF&Expires=1594515416&Signature=T25g0YP0ALalj5jCUAQ9mtZ41M0%3D"
            // daily data, gzipped csv. Url is pre-signed and could expire.
        }
    ],
    "expiration": "300 seconds"
}
```

**Examples:**

https://api.cryptochassis.com/v1/market-depth/coinbase/btc-usd

**CSV file format:**

time_seconds,bid_price_bid_size|...,ask_price_ask_size|...  
1594512000,9234.05_2.20765974|...,9234.98_2|...

If there is a gap in "time_seconds", it means that the market depth snapshot at that moment is the same (up to depth 10) as the previous moment.

### Quote
```
GET /quote/<exchange>/<instrument>?startTime=<startTime>
```
Deprecated. All existing requests are redirected to [Market Depth](#Market-Depth).

### Trade
```
GET /trade/<exchange>/<instrument>?startTime=<startTime>
```
Daily trade ticks. Each tick represents a single trade.

**Parameters:**

Name | Mandatory | Description
------------ | ------------ | ------------
exchange | yes | E.g. coinbase.
instrument | yes | E.g. btc-usd.
startTime | no | E.g. 1577318400 (seconds), 2019-12-26 (iso). Defaults to most recent.

**Response:**
```javascript
{
    "urls": [
        {
            "startTime": {
                "seconds": 1577318400, // unix time
                "iso": "2019-12-26T00:00:00.000Z"
            },
            "url": "https://marketdata-e0323a9039add2978bf5b49550572c7c.s3.amazonaws.com/trade/coinbase/btc_usd/1577318400.csv.gzip?AWSAccessKeyId=AKIATPNB7YZIUQR3JVNF&Expires=1577486042&Signature=jQ9Vya3X9yBc7x26MyWhRKdKEdI%3D"
            // daily data, gzipped csv. Url is pre-signed and could expire.
        }
    ],
    "expiration": "300 seconds"
}
```

**Examples:**

https://api.cryptochassis.com/v1/trade/coinbase/btc-usd

**CSV file format:**

time_seconds,time_nanoseconds,price,size,is_buyer_maker,trade_id  
1594512000,140000000,9235,0.004,0,96572013

### OHLC
```
GET /ohlc/<exchange>/<instrument>?interval=<interval>&startTime=<startTime>&endTime=<endTime>
```
Aggregated metrics from raw trades: open, high, low, close, volume, vwap, number of trades, and twap.

**Parameters:**

Name | Mandatory | Description
------------ | ------------ | ------------
exchange | yes | E.g. coinbase.
instrument | yes | E.g. btc-usd.
interval | no | Allowed values: 1m, 3m, 5m, 15m, 30m, 1h, 2h, 3h, 4h, 6h, 8h, 12h, 1d. Defaults to 1m.
startTime | no | E.g. 1577318400 (seconds), 2019-12-26T00:00:00.000Z (iso). Defaults to 10 intervals before endTime.
endTime | no | E.g. 1577318400 (seconds), 2019-12-26T00:00:00.000Z (iso). Defaults to most recent.

**Response:**
```javascript
{
    "historical": {
        "urls": [
            {
                "startTime": {
                    "seconds": 1451606400, // unix time
                    "iso": "2016-01-01T00:00:00.000Z"
                },
                "endTime": {
                    "seconds": 1590969600, // unix time
                    "iso": "2020-06-01T00:00:00.000Z"
                },
                "url": "https://marketdata-e0323a9039add2978bf5b49550572c7c.s3.amazonaws.com/ohlc/1440/bitfinex/btc_usd/historical.csv.gz?AWSAccessKeyId=AKIATPNB7YZIUQR3JVNF&Expires=1593483130&Signature=KQMy29T5sVP7v2TUjXr9IOrhba0%3D"
                // historical data, gzipped csv. Url is pre-signed and could expire.
            }
        ],
        "expiration": "300 seconds"
    },
    "recent": {
        "startTime": {
            "seconds": 1590969600, // unix time
            "iso": "2020-06-01T00:00:00.000Z"
        },
        "endTime": {
            "seconds": 1591488000, // unix time
            "iso": "2020-06-07T00:00:00.000Z"
        },
        "fields": "time_seconds, open, high, low, close, volume, vwap, number_of_trades, twap",
        "data": [
            [
                1590969600, // unix time for interval start
                "9451.1", // open
                "10398", // high
                "9421.75975983", // low
                "10225.97600025", // close
                "13427.10257919", // volume
                "9887.7212102559", // volume weighted average price = sum(tick_price * tick_volume) / volume
                117344, // number of trades
                "9701.6274016032" // time weighted average price = sum(tick_price) / number_of_trades
            ],
            ...
            // Note that the last bar may have not closed yet.
        ]
    }
}
```

**Examples:**

https://api.cryptochassis.com/v1/ohlc/coinbase/btc-usd  
https://api.cryptochassis.com/v1/ohlc/coinbase/btc-usd?startTime=0

**CSV file format:**

time_seconds,open,high,low,close,volume,vwap,number_of_trades,twap  
1451606400,430.35,430.39,430.35,430.39,0.0727,430.3804,4,430.3725
