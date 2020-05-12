# RFC Open Finance Protocol

## Summary

That standard is aiming to create simple, fast and sufficient protocol for financial communication with/between financial institutions on WebSockets.

## Motivation

Why are we doing this? What use cases does it support? What is the expected outcome?

## Introduction to the RPC protocol

The protocol is aimed to be used through websocket.

Every events are formatted in a JSON array like the following:

```
[type, request_id, method, arguments]
```

| Element    | Description                                                                                                                  |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------- |
| type       | This element identifies the event type: 1 for requests, 2 for responses, 3 for public events and 4 for private events        |
| request_id | This is a request ID set by the client to identify the resquest and the associated response. It must be an unsigned integer. It's absent for events. |
| method     | method name to be called                                                                                                     |
| arguments  | list of arguments for the method                                                                                             |

## General definitions

### Units of time definition

|   unit | symbol |
| -----: | :----- |
| second | s      |
| minute | m      |
|   hour | h      |
|    day | d      |
|   week | w      |
|  month | M      |
|   year | y      |

The biggest unit of time possible must be chosen to represent a time period.

For example `5m` must be chosen instead of `300s`.

## Authentication

### Client to server authentication (C2S)

### Server to server authentication (S2S)

## RPC Methods

### Subscribe to events streams

| Argument | Description                                                                                  |
| -------- | -------------------------------------------------------------------------------------------- |
| Scope    | "public" or "private", the client needs to be authenticated to subscribe to a private stream |
| Streams  | Array of strings representing the streams to subscribe to                                    |

Request example:

```
[1,42,"subscribe",["public",["eurusd.trades","eurusd.orderbook","tickers","eurusd.kline-15m"]]]
[1,43,"subscribe",["private",["orders","trades"]]]
```

Response example:

```
[2,42,"subscribe",["public",["eurusd.trades","eurusd.orderbook","tickers","eurusd.kline-15m"]]]
[2,43,"subscribe",["private",["orders","trades"]]]
```

The response returns the list of _all_ current subscriptions for the current connection after the subription is perfomed.

### Unsubscribe from events streams

| Argument | Description                                                                                    |
| -------- | ---------------------------------------------------------------------------------------------- |
| Scope    | "public" or "private", the client needs to be authenticated to unsubscribe to a private stream |
| Streams  | Array of strings representing the streams to unsubscribe to                                    |

Request:

```
[1,42,"unsubscribe",["public", ["eurusd.orderbook"]]
```

Response:

```
[2,42,"unsubscribe",["public", ["eurusd.trades","tickers"]]]
```

The response returns the list of remaining subscriptions for the current connection after the unsubsciption is performed.

### Orders

In our standard, the parameters order plays an important role as the position of the parameter represents a key. All utilized parameters must be present in request and response even if they not utilized. To skip optional parameter use `null` value. 

**Order types** supported with `create_order` method:

| Order type | TIF           | Description                                      |
| :--------: | ------------- | ------------------------------------------------ |
|     M      | IOC*          | Market order                                     |
|     L      | GTC* IOC, FOK | Limit order (available flags: hidden, post-only) |
|     S      | GTC* IOC, FOK | Stop                                             |
|     SL     | GTC* IOC, FOK | Stop-limit (available flags: OCO)                |

**All arguments that used for order creation order**:

| №    | Name          | To discuss*                                                  | Data type | Description                                    |
| ---- | ------------- | ------------------------------------------------------------ | --------- | ---------------------------------------------- |
| 1    | instrument    | Symbol or Market pair                                        | string    | Used to specify instrument for order "BTC/USD" |
| 2    | order_type    |                                                              | integer   | Used to specify order type                     |
| 3    | side          | It can be removed in case of <br />negative amount           | integer   | Used to specify order side, buy or sell        |
| 4    | quantity      | Amount. Negative amount<br />to indicate sell and positive for buy | string    | An amount that placed within the order         |
| 5    | price         |                                                              | string    | Main (limit) price of the order                |
| 6    | stop_price    |                                                              | string    | Stop (trigger) price of the order              |
| 7    | tif           |                                                              | string    | Time in force instruction                      |
| 8    | cid           |                                                              | string    | ID of the order that is generated on user side |
| 9    | flags         |                                                              | integer   | Used to set custom order instructions          |
| 10   | timestamp     |                                                              | string    | UTC timestamp                                  |
| 11   | status@reason |                                                              | string    | Send by exchange with response for orders      |
| 12   | order_id      |                                                              | string    | Send by exchange with response for orders      |

Supported **TIF** instructions:

| Code | Name | Description                                        |
| ---- | ---- | -------------------------------------------------- |
| 1    | IOC  | Allows partial execution, unfilled part canceling. |
| 2    | GTC  | Remain active until cancellation on stop.          |
| 3    | FOK  | Allows only full execution.                        |

Supported order **flags** instructions:

| Code   | Flag         | Type    | Description                                                  |
| ------ | ------------ | ------- | ------------------------------------------------------------ |
| 64     | Hidden       | integer | The hidden order option ensures an order does not appear in the order book; thus does not influence other market participants. |
| 512    | Close        | integer | Close position if position present.                          |
| 1024   | Reduce Only  | integer | Ensures that the executed order does not flip the opened position. |
| 4096   | Post Only    | integer | The post-only limit order option ensures the limit order will be added to the order book and not match with a pre-existing order. |
| 16384  | OCO          | integer | The one cancels other order option allows you to place a pair of orders stipulating that if one order is executed fully or partially, then the other is automatically canceled. |
| 524288 | No Var Rates | integer | Excludes variable rate funding offers from matching against this order, if on margin |

For **timestamps** use UTC time that expressed as milliseconds (i.e. 1588678924349)

**Order statuses table**:

| Code | Status           | Description                                                  |
| ---- | ---------------- | ------------------------------------------------------------ |
| 1    | Pending          | Order received by the exchange but haven't been placed in the order book yet |
| 2    | Active           | Order placed in the order book and waits for execution       |
| 3    | Partially filled | Order partially executed and wait for remaining execution    |
| 4    | Filled           | Order fully executed                                         |
| 5    | Rejected         | Order rejected due too some errors                           |
| 6    | Canceled         | Order canceled by user                                       |
| 7    | Stopped          | Order stopped by exchange                                    |


**Bellow you can find examples of different order types:**

#### Market order

Arguments with corresponding numeration of **market** order **request**:

|  №   | Name       | Data type |  Example   |
| :--: | ---------- | :-------: | :--------: |
|  1   | instrument |  string   | "BTC/USD"  |
|  2   | order_type |  integer  |    "M"     |
|  3   | side       |  integer  |   "buy"    |
|  4   | quantity   |  string   | "0.100000" |
|  5   | cid        |  string   | "1234567"  |

Arguments with corresponding numeration of **market** order **response**:

|  №   | Name          | Data type |     Example     |
| :--: | ------------- | :-------: | :-------------: |
|  1   | instrument    |  string   |    "BTC/USD"    |
|  2   | order_type    |  integer  |       "M"       |
|  3   | side          |  integer  |      "buy"      |
|  4   | quantity      |  string   |   "0.100000"    |
|  5   | cid           |  string   |    "1234567"    |
|  6   | timestamp     |  string   | "1588678924349" |
|  7   | status@reason |  string   |  "filled@null"  |
|  8   | order_id      |  string   |    "8745635"    |

Example of the messages 

Request:

```
[,42,"create_order",["BTC/USD", "M", "buy", "0.100000", "1234567"]
```

Response:

```
[2,42,"create_order",["BTC/USD", "M", "buy", "0.100000", "1234567", "1588678924349", "filled@null", "8745635"]
```

#### Limit order

Limit order can be customized with flags and TIF instructions.

Arguments with corresponding numeration of **limit** order **request**:

|  №   | Name       | Data type |  Example   |
| :--: | ---------- | :-------: | :--------: |
|  1   | instrument |  string   | "BTC/USD"  |
|  2   | order_type |  integer  |    "L"     |
|  3   | side       |  integer  |   "buy"    |
|  4   | quantity   |  string   | "0.250000" |
|  5   | price      |  string   | "9120.00"  |
|  6   | tif        |  string   |     2      |
|  7   | cid        |  string   | "1234568"  |
|  8   | flags      |  integer  |     0      |

Arguments with corresponding numeration of **limit** order **response**:

|  №   | Name          | Data type |             Example             |
| :--: | ------------- | :-------: | :-----------------------------: |
|  1   | instrument    |  string   |            "BTC/USD"            |
|  2   | order_type    |  integer  |               "L"               |
|  3   | side          |  integer  |              "buy"              |
|  4   | quantity      |  string   |           "0.250000"            |
|  5   | price         |  string   |            "9120.00"            |
|  6   | tif           |  string   |                2                |
|  7   | cid           |  string   |            "1234568"            |
|  8   | flags         |  integer  |                0                |
|  9   | timestamp     |  string   |         "1588678984376"         |
|  10  | status@reason |  string   | "rejected@insufficient balance" |
|  11  | order_id      |  string   |            "8745985"            |

Example of the messages 

Request:

```
[1,42,"create_order",["BTC/USD", "L", "buy", "0.250000", "9120.00", 2, "1234568", 0]
```

Response:

```
[2,42,"create_order",["BTC/USD", "L", "buy", "0.250000", "9120.00", 2, "1234568", 0, "1588678984376", "rejected@insufficient_balance", "8745985"]
```

#### Stop order

Arguments with corresponding numeration of **stop** order **request**:

|  №   | Name       | Data type |  Example   |
| :--: | ---------- | :-------: | :--------: |
|  1   | instrument |  string   | "BTC/USD"  |
|  2   | order_type |  integer  |    "S"     |
|  3   | side       |  integer  |   "sell"   |
|  4   | quantity   |  string   | "0.250000" |
|  5   | stop_price |  string   | "9120.00"  |
|  6   | tif        |  string   |     2      |
|  7   | cid        |  string   | "1234568"  |
|  8   | flags      |  integer  |     0      |

Arguments with corresponding numeration of **stop** order **response**:

|  №   | Name          | Data type |     Example     |
| :--: | ------------- | :-------: | :-------------: |
|  1   | instrument    |  string   |    "BTC/USD"    |
|  2   | order_type    |  integer  |       "S"       |
|  3   | side          |  integer  |     "sell"      |
|  4   | quantity      |  string   |   "0.250000"    |
|  5   | stop_price    |  string   |    "9120.00"    |
|  6   | tif           |  string   |        2        |
|  7   | cid           |  string   |    "1234568"    |
|  8   | flags         |  integer  |        0        |
|  9   | timestamp     |  string   | "1588678984376" |
|  10  | status@reason |  string   |  "active@null"  |
|  11  | order_id      |  string   |    "8745985"    |

Example of the messages 

Request:

```
[1,42,"create_order",["BTC/USD", "S", "sell", "0.250000", "9120.00", 2, "1234568", 0]
```

Response:

```
[2,42,"create_order",["BTC/USD", "S", "sell", "0.250000", "9120.00", 2, "1234568", 0, "1588678984376", "active@null", "8745985"]
```

#### Stop-limit order

Arguments with corresponding numeration of **stop-limit** order **request**:

|  №   | Name       | Data type |  Example   |
| :--: | ---------- | :-------: | :--------: |
|  1   | instrument |  string   | "BTC/USD"  |
|  2   | order_type |  integer  |    "SL"    |
|  3   | side       |  integer  |   "buy"    |
|  4   | quantity   |  string   | "0.250000" |
|  5   | stop_price |  string   | "9120.00"  |
|  6   | price      |  string   | "9118.00"  |
|  7   | tif        |  string   |     2      |
|  8   | cid        |  string   | "1234568"  |
|  9   | flags      |  integer  |     0      |

Arguments with corresponding numeration of **stop-limit** order **response**:

|  №   | Name          | Data type |     Example     |
| :--: | ------------- | :-------: | :-------------: |
|  1   | instrument    |  string   |    "BTC/USD"    |
|  2   | order_type    |  integer  |      "SL"       |
|  3   | side          |  integer  |      "buy"      |
|  4   | quantity      |  string   |   "0.250000"    |
|  5   | price         |  string   |    "9118.00"    |
|  6   | stop_price    |  string   |    "9120.00"    |
|  7   | tif           |  string   |        2        |
|  8   | cid           |  string   |    "1234568"    |
|  9   | flags         |  integer  |        0        |
|  10  | timestamp     |  string   | "1588678984376" |
|  11  | status@reason |  string   |  "active@null"  |
|  12  | order_id      |  string   |    "8745985"    |

Example of the messages 

Request:

```
[1,42,"create_order",["BTC/USD", "SL", "buy", "0.250000", "9120.00", "9118.00", 2, "1234568", 0]
```

Response:

```
[2,42,"create_order",["BTC/USD", "SL", "buy", "0.250000", "9120.00", "9118.00", 2, "1234568", 0, "1588678984376", "active@null", "8745985"]
```

#### Bulk order

Bulk order uses `create_bulk` method to send multiple orders within one request.  Also, bulk order can be used to send different order types with one request. *We need to specify parsing order of orders inside the bulk order.*

Example of the messages 

Request:

```
[1,42,"create_bulk",[[order_1_request], [order_2_request], [order_3_request], [order_4_request]]
```

Response:

```
[2,42,"create_bulk",[[order_1_response], [order_2_response], [order_3_response], [order_4_response]]
```

## Public events streams

### Kline (also known as OHLC)

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

### Tickers

```json
[
  3,
  "tikers",
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

### Trade

```json
[3, "trade", ["btcusd", 1, "1", "1", 1589211189, "buy"]]
```

**Payload**

Array of tickers

| Argument   | Description                          |
| ---------- | ------------------------------------ |
| Market ID  | Market unique identifier             |
| Trade ID   | Trade unique identifier              |
| Price      | Trade Price                          |
| Amount     | Trade amount                         |
| Timestamp  | Trade created_at timestamp           |
| Taker type | Side of taker order, "buy" or "sell" |

### Orderbook

#### Snapshot

Orderbook Snapshot

```json
[3, "obSnap", [0, [["10", "1"]], [["5", "1"]]]]
```

| Argument | Description                     |
| -------- | ------------------------------- |
| Sequence | Sequentially increasing number  |
| Sells    | Array of sell side price levels |
| Buys     | Array of buy side price levels  |

#### Increment

Orderbook update

```json
[3, "obInc", [1, "asks", ["10", "1"]]]
```

**Payload**

| Argument   | Description                                             |
| ---------- | ------------------------------------------------------- |
| Sequence   | Sequentially increasing number                          |
| Side       | Side updated, "asks" or "bids"                          |
| PriceLevel | Price and amount, empty amount means pricelevel deleted |

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

Payload is Array of elements:

| Argument | Description                 |
| -------- | --------------------------- |
| Currency | Market currency identifier  |
| Balance  | User's balance for currency |
| Locked   | User's locked for currency  |
