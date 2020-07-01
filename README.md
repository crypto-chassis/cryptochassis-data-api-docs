# Public Rest API From Cryptochassis
* This API provides tick data (e.g. quotes, trades) and aggregated data (e.g. ohlc) about leading crypto-currencies on leading crypto-exchanges.
* Supported currencies: btc, eth, ltc, xrp, bch, eos.
* Supported spot exchanges: coinbase, gemini, kraken, bitstamp, bitfinex.
* Supported derivatives: bitmex (xbtusd).
* Backfill history: 5 years historical data, wherever applicable.
* Institutional quality and independently verifiable.
* To spur innovation and industry collaboration, this API is open for use by the public without cost. Follow us on https://medium.com/@cryptochassis and our publication on https://medium.com/open-crypto-market-data-initiative.
* Please contact hello@cryptochassis.com for general questions, issue reporting, consultative services, and/or custom engineering work.

## General API Information
* The base endpoint is: **https://api.cryptochassis.com/v1**

## Reqeust Rate Limits
* The limit on the API is based on public IPs: 1 request per second per IP.

## Public API Endpoints

### Information
```
GET /information?dataType=<dataType>&exchange=<exchange>&instrument=<instrument>
```
Information about available data types, exchanges, instruments, and the first/last available time points.

**Parameters:**

Name | Mandatory | Description
------------ | ------------ | ------------
dataType | no | Comma seperated list. Allowed values: quote, trade, ohlc.
exchange | no | Comma seperated list. Allowed values: coinbase, gemini, kraken, bitstamp, bitfinex, bitmex.
instrument | no | Comma seperated list. Allowed values: btc-usd, eth-usd, ltc-usd, xrp-usd, bch-usd, eos-usd, xbtusd.

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

### Quote
```
GET /quote/<exchange>/<instrument>?startTime=<startTime>
```
Daily best bid/ask ticks. Each tick represents a change in best bid/ask.

**Parameters:**

Name | Mandatory | Description
------------ | ------------ | ------------
exchange | yes | E.g. coinbase.
instrument | yes | E.g. btc-usd.
startTime | no | E.g. 1577318400 (seconds), 2019-12-26 (iso). If absent, defaults to most recent.

**Response:**
```javascript
{
    "urls": [
        {
            "startTime": {
                "seconds": 1577318400, // unix time
                "iso": "2019-12-26T00:00:00.000Z"
            },
            "url": "https://marketdata-e0323a9039add2978bf5b49550572c7c.s3.amazonaws.com/quote/gemini/btc_usd/1577318400.csv.gzip?AWSAccessKeyId=AKIATPNB7YZIUQR3JVNF&Expires=1577485954&Signature=JOwv%2FoenoIhHVx6nyuGY1R%2FHbHM%3D"
            // daily data, gzipped csv. Url is pre-signed and could expire.
        }
    ],
    "expiration": "30 seconds"
}
```

**Examples:**

https://api.cryptochassis.com/v1/quote/coinbase/btc-usd

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
startTime | no | E.g. 1577318400 (seconds), 2019-12-26 (iso). If absent then defaults to most recent.

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
    "expiration": "30 seconds"
}
```

**Examples:**
https://api.cryptochassis.com/v1/trade/coinbase/btc-usd

### OHLC (BETA)
```
GET /ohlc/<exchange>/<instrument>?interval=<interval>&startTime=<startTime>&endTime=<endTime>
```
Aggregated metrics from raw trades: open, high, low, close, volume, vwap, number of trades, and twap.

**Parameters:**

Name | Mandatory | Description
------------ | ------------ | ------------
exchange | yes | E.g. coinbase.
instrument | yes | E.g. btc-usd.
interval | no | Allowed values: 1m, 3m, 5m, 15m, 30m, 1h, 2h, 3h, 4h, 6h, 8h, 12h, 1d.
startTime | no | E.g. 1577318400 (seconds), 2019-12-26T00:00:00.000Z (iso). If absent then defaults to 10 intervals before endTime.
endTime | no | E.g. 1577318400 (seconds), 2019-12-26T00:00:00.000Z (iso). If absent then defaults to most recent.

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
        "expiration": "30 seconds"
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
        ]
    }
}
```

**Examples:**
https://api.cryptochassis.com/v1/ohlc/coinbase/btc-usd
