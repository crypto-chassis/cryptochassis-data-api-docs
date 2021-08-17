<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Public Data API From Cryptochassis](#public-data-api-from-cryptochassis)
  - [General API Information](#general-api-information)
  - [Reqeust Rate Limits](#reqeust-rate-limits)
  - [Public API Endpoints](#public-api-endpoints)
    - [Information](#information)
    - [Market Depth](#market-depth)
    - [Quote](#quote)
    - [Trade](#trade)
    - [OHLC](#ohlc)
  - [Troubleshoot](#troubleshoot)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

**Small breaking change**: `Information` endpoint now needs mandatory query parameter `dataType` and `exchange`.

# Public Data API From Cryptochassis
* This REST API provides snapshot data (e.g. market depth, quote), tick data (e.g. trades), and aggregated data (e.g. ohlc) for crypto-currencies.
* Supported exchanges: coinbase, gemini, kraken, bitstamp, bitfinex, bitmex, binance, binance-us, binance-usds-futures, binance-coin-futures, huobi, huobi-usdt-swap, huobi-coin-swap okex, kucoin, ftx, ftx-us, deribit.
* Backfill history: historical data since 2016-01-01, wherever applicable.
* Institutional quality and independently verifiable.
* To spur innovation and industry collaboration, this API is open for use by the public without cost.
* Please contact us for consultative services and custom engineering work. We specialize in market data collection, high speed trading system, infrastructure optimization, and proprietary market making.
* Join us on Discord https://discord.gg/b5EKcp9s8T and Medium https://cryptochassis.medium.com.

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
dataType | yes | Comma seperated list. Allowed values: market-depth, quote, trade, ohlc.
exchange | yes | Comma seperated list. Allowed values: coinbase, gemini, kraken, bitstamp, bitfinex, bitmex, binance, binance-us, binance-usds-futures, binance-coin-futures, huobi, huobi-usdt-swap, huobi-coin-swap okex, kucoin, ftx, ftx-us.
instrument | no | Comma seperated list.

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
depth | no | Allowed values: 1, 10. Defaults to 1.
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

If there is a gap in "time_seconds", it means that the market depth snapshot at that moment is the same as the previous moment.

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
startTime | no | E.g. 1577318400 (seconds), 2019-12-26T00:00:00.000Z (iso). Defaults to 30 intervals before endTime.
endTime | no | E.g. 1577318400 (seconds), 2019-12-26T00:00:00.000Z (iso). Defaults to most recent.
includeRealTime | no | Allowed values: 0, 1. Defaults to 0. If set to 1, request rate limit on this endpoint is 1 request per second per public IP.

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

## Troubleshoot
* macOS Archive Utility can't unzip gzipped files (Error 79 - Inappropriate file type or format). This is a known bug on some versions of macOS (see https://apple.stackexchange.com/questions/388759/archive-utility-cant-open-some-gzipped-text-files-based-on-their-contents). Try to unzip the files by other means (e.g. https://stackoverflow.com/questions/50993318/uncompress-a-txt-gz-file-in-mac).
