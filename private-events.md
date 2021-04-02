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
    2,
    "7acbbc84-939d-11ea-a827-1831bf9834b0",
    "buy",
    "w",
    "l",
    "1",
    "1",
    "0",
    "1",
    "1",
    1,
    1589211516
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

| Argument        | Description                |
| --------------- | -------------------------- |
| Market ID       | Market unique identifier   |
| ID              | Unique order ID            |
| UUID            | Unique order uuid          |
| Side            | "buy" or "sell"            |
| State           | Current state of the order |
| Type            | Order type                 |
| Price           | Order price                |
| Average Price   | Average execution price    |
| Volume          | Current order volume       |
| Origin Volume   | Origin volume              |
| Executed Volume | Executed volume            |
| Trades Count    | Order trades count         |
| Timestamp       | Order creation timestamp   |

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
