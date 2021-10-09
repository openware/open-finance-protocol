---
sort: 9
---

## Private events streams

### Order notifications

```json
[
  4,
  "on",
  [
      "btcusd",
      97,
      "6dcc2c8e-c295-11ea-b7ad-1831bf9834b0",
      "sell",
      "w",
      "l",
      "9120",
      "0",
      "0.25",
      "0.25",
      "0",
      0,
      1594386563
   ]
]
```

**Topics**

| Topic | Description    |
| ----- | -------------- |
| on    | Order New      |
| oc    | Order Canceled |
| ou    | Order Updated  |
| or    | Order Rejected |

**Payload**

| Field           | Example                                |
| --------------- | -------------------------------------- |
| Market          | "btcusd"                               |
| ID              | 97                                     |
| UUID            | "6dcc2c8e-c295-11ea-b7ad-1831bf9834b0" |
| Side            | "sell"                                 |
| State           | "w"                                    |
| Type            | "l"                                    |
| Price           | "9120"                                 |
| Avg. Price      | "0"                                    |
| Volume          | "0.25"                                 |
| Orig. Volume    | "0.25"                                 |
| Executed Volume | "0"                                    |
| Trades Count    | 0                                      |
| Timestamp       | 1594386563                             |

**States**

| State | Description |
| ----- | ----------- |
| p     | Pending     |
| w     | Wait        |
| d     | Done        |
| r     | Rejected    |
| c     | Canceled    |

**Types**

| Type | Description  |
| ---- | ------------ |
| l    | Limit        |
| m    | Market       |
| p    | Post Only    |
| f    | Fill or Kill |

### Trade event

```json
[
  4,
  "tr",
  [
    "btcusd",
    2,
    "3",
    "5",
    "15",
    3,
    "5b295fc5-9363-11eb-adb6-1831bf9834b0",
    "buy",
    "sell",
    "0.005",
    "btc",
    1617334050
  ]
]
```

**Payload**

| Argument     | Description                  |
| ------------ | ---------------------------- |
| Market ID    | Market unique identifier     |
| ID           | Unique trade ID              |
| Price        | Trade execution price        |
| Amount       | Trade execution amount       |
| Funds        | Trade funds, amount \* price |
| OrderID      | User's trade order ID        |
| OrderUUID    | User's trade order UUID      |
| Side         | User's trade order side      |
| Taker side   | Trade taker side             |
| Fee          | Fee amount                   |
| Fee Currency | Fee currency                 |
| Timestamp    | Trade timestamp              |

### Balance update

```json
[
  4,
  "bu",
  [
    ["eth", "1000000000", "0"],
    ["trst", "1000000000", "0"],
    ["usd", "999999999", "0"],
    ["btc", "0.998", "0"]
  ]
]
```

**Payload**

Payload is an array of elements:

| Argument | Description                 |
| -------- | --------------------------- |
| Currency | Market currency identifier  |
| Balance  | User's balance for currency |
| Locked   | User's locked for currency  |
