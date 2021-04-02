---
sort: 8
---
# Public events streams

## Kline (also known as OHLC)

**Arguments**

| Argument      | Description                                               |
| ------------- | --------------------------------------------------------- |
| Market ID     | market unique identifier                                  |
| Period        | Period                                                    |
| Timestamp     | Timestamp of the period start                             |
| Open price    | Price of the first trade in the period                    |
| High price    | Highest price of the period                               |
| Low price     | Lowest price of the period                                |
| Close price   | Price of the last price of the period                     |
| Period volume | (optional) Sum of trades amount of the period             |
| Average price | (optional) Weighted average price of trades of the period |

```json
[
  3,
  "kline",
  [
    "ethbtc",
    "15m",
    1588791600,
    0.025353,
    0.025353,
    0.025353,
    0.025353,
    10,
    0.025353
  ]
]
```

## Tickers

```json
[
  3,
  "tickers",
  [
    [
      "ethbtc",
      1588791600,
      0.025353,
      0.025353,
      0.025353,
      0.025353,
      10,
      0.025353
    ],
    ["bchbtc", 1588791600, 0.025353, 0.025353, 0.025353, 0.025353, 10, 0.025353]
  ]
]
```

**Payload**

Array of tickers

| Argument      | Description                                               |
| ------------- | --------------------------------------------------------- |
| Market ID     | market unique identifier                                  |
| Timestamp     | Timestamp of the period start                             |
| Open price    | Price of the first trade in the period                    |
| High price    | Highest price of the period                               |
| Low price     | Lowest price of the period                                |
| Close price   | Price of the last price of the period                     |
| Period volume | (optional) Sum of trades amount of the period             |
| Average price | (optional) Weighted average price of trades of the period |

## Trade

```json
[3, "trade", ["btcusd", 1, "1", "1", 1589211189, "buy"]]
```

**Payload**

| Argument   | Description                          |
| ---------- | ------------------------------------ |
| Market ID  | Market unique identifier             |
| Trade ID   | Trade unique identifier              |
| Price      | Trade Price                          |
| Amount     | Trade amount                         |
| Timestamp  | Trade created_at timestamp           |
| Taker type | Side of taker order, "buy" or "sell" |

## Orderbook

### Snapshot

Orderbook Snapshot

```json
[3, "obs", [0, [["10", "1"]], [["5", "1"]]]]
```

| Argument | Description                     |
| -------- | ------------------------------- |
| Sequence | Sequentially increasing number  |
| Sells    | Array of sell side price levels |
| Buys     | Array of buy side price levels  |

### Increment

Orderbook update

```json
[3, "obi", [1, "asks", ["10", "1"]]]
```

**Payload**

| Argument   | Description                                             |
| ---------- | ------------------------------------------------------- |
| Sequence   | Sequentially increasing number                          |
| Side       | Side updated, "asks" or "bids"                          |
| PriceLevel | Price and amount, empty amount means pricelevel deleted |
