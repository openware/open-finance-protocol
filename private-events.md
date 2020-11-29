---
sort: 9
---


## Private events streams

### Order update

```json
[
  4,
  "order",
  [
    "btcusd",
    2,
    "7acbbc84-939d-11ea-a827-1831bf9834b0",
    "buy",
    "bid",
    "done",
    "limit",
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

**Payload**

| Argument        | Description                |
| --------------- | -------------------------- |
| Market ID       | Market unique identifier   |
| ID              | Unique order ID            |
| UUID            | Unique order uuid          |
| Side            | "buy" or "sell"            |
| Kind            | "bid" or "ask"             |
| State           | Current state of the order |
| Type            | Order type                 |
| Price           | Order price                |
| Average Price   | Average execution price    |
| Volume          | Current order volume       |
| Origin Volume   | Origin volume              |
| Executed Volume | Executed volume            |
| Trades Count    | Order trades count         |
| Timestamp       | Order creation timestamp   |

### Trade event

```json
[
  4,
  "trade",
  [
    "btcusd",
    1,
    "1",
    "1",
    "1",
    2,
    "55d78eee-939e-11ea-945f-1831bf9834b0",
    "buy",
    "buy",
    1589211884
  ]
]
```

**Payload**

| Argument   | Description                  |
| ---------- | ---------------------------- |
| Market ID  | Market unique identifier     |
| ID         | Unique trade ID              |
| Price      | Trade execution price        |
| Amount     | Trade execution amount       |
| Funds      | Trade funds, amount \* price |
| OrderID    | User's trade order ID        |
| OrderUUID  | User's trade order UUID      |
| Side       | User's trade order side      |
| Taker side | Trade taker side             |
| Timestamp  | Trade timestamp              |

### Balance change

```json
[
  4,
  "balanceUpdate",
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
