# RFC Open Finance Protocol

## Summary

That standard is aiming to create simple, fast and sufficient protocol for financial communication with/between financial institutions on WebSockets.

## Motivation

Why are we doing this? What use cases does it support? What is the expected outcome?

## Introduction to the RPC protocol

The protocol is aimed to be used through websocket.

Every events are formated in a JSON array like the following:
```
[type, request_id, method, arguments]
```
| Element    | Description                                                  |
| ---------- | ------------------------------------------------------------ |
| type       | This element identifies the event type: 1 for requests, 2 for responses, 3 for public events and 4 for private events |
| request_id | This is a request ID set by the client to identify the resquest and the associated response. It must be an unsigned integer. |
| method     | method name to be called                                     |
| arguments  | list of arguments for the method                             |

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

| Argument | Description                                                  |
| -------- | ------------------------------------------------------------ |
| Scope    | "public" or "private", the client needs to be authenticated to subscribe to a private stream |
| Streams  | Array of strings representing the streams to subscribe to    |

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
The response returns the list of *all* current subscriptions for the current connection after the subription is perfomed.

### Unsubscribe from events streams
|Argument|Description|
|--------|-----------|
|Scope|"public" or "private", the client needs to be authenticated to unsubscribe to a private stream |
|Streams|Array of strings representing the streams to unsubscribe to|

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

##### All arguments that used with order:

| №    | Name          | To discuss*                                                  | Data type | Description                                    |
| ---- | ------------- | ------------------------------------------------------------ | --------- | ---------------------------------------------- |
| 1    | instrument    | Symbol or Market pair                                        | string    | Used to specify instrument for order "BTC/USD" |
| 2    | order_type    |                                                              | integer   | Used to specify order type                     |
| 3    | side          | It can be removed in case of <br />negative amount           | integer   | Used to specify order side (buy or sell)       |
| 4    | quantity      | Amount. Negative amount<br />to indicate sell and positive for buy | string    | An amount that placed within the order         |
| 5    | price         |                                                              | string    | Main (limit) price of the order                |
| 6    | stop_price    |                                                              | string    | Stop (trigger) price of the order              |
| 7    | tif           |                                                              | string    | Time in force instruction                      |
| 8    | cid           |                                                              | string    | ID of the order that is generated on user side |
| 9    | flags         |                                                              | integer   | Used to set custom order instructions          |
| 10   | timestamp     |                                                              | string    | UTC timestamp                                  |
| 11   | status@reason |                                                              | string    | Send by exchange with response for orders      |
| 12   | order_id      |                                                              | string    | Send by exchange with response for orders      |

Use a corresponding code to define **order type** :

| Code | Order type | TIF           | Description                                            |
| ---- | ---------- | ------------- | ------------------------------------------------------ |
| 1    | Market     | IOC*          | Market order                                           |
| 2    | Limit      | GTC* IOC, FOK | Limit order                                            |
| 3    | Stop-loss  | GTC* IOC, FOK |                                                        |
| 4    | Stop-limit | GTC* IOC, FOK | *We can put OCO inside stop-limit with a special flag* |
| 5    | OCO        | GTC* IOC, FOK | One cancel other                                       |
| *    | Bulk       | Custom, Scale | Each parameter of Bulk order is a separate order       |

Use a corresponding code to define **order side** :

| Code | Side | Description                             |
| ---- | ---- | --------------------------------------- |
| 1    | Buy  | Use code "1" to mark it as a Buy order  |
| 2    | Sell | Use code "2" to mark it as a Sell order |

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

**Example of generic order creation request and response**

Request:

```
[0,42,"createOrder",["instrument", "order_type", "side", "quantity", "price", "stop_price", "tif", "cid", "flags"]
```

Response:

```
[1,42,"createOrder",["instrument", "order_type", "side", "quantity", "price", "stop_price", "tif", "cid", "flags", "timestamp", "status@reason", "order_id"]
```

**Bellow you can find examples of different order types:**

#### Market order

Arguments with corresponding numeration of **market** order **request**:

|  №   | Name       | Data type |  Example   |
| :--: | ---------- | :-------: | :--------: |
|  1   | instrument |  string   | "BTC/USD"  |
|  2   | order_type |  integer  |     1      |
|  3   | side       |  integer  |     1      |
|  4   | quantity   |  string   | "0.100000" |
|  5   | cid        |  string   | "1234567"  |

Arguments with corresponding numeration of **market** order **response**:

|  №   | Name          | Data type |     Example     |
| :--: | ------------- | :-------: | :-------------: |
|  1   | instrument    |  string   |    "BTC/USD"    |
|  2   | order_type    |  integer  |        1        |
|  3   | side          |  integer  |        1        |
|  4   | quantity      |  string   |   "0.100000"    |
|  5   | cid           |  string   |    "1234567"    |
|  6   | timestamp     |  string   | "1588678924349" |
|  7   | status@reason |  string   |  "filled@null"  |
|  8   | order_id      |  string   |    "8745635"    |

Example of the messages 

Request:

```
[0,42,"createMarketOrder",["BTC/USD", 1, 1, "0.100000", "1234567"]
```

Response:

```
[1,42,"createMarketOrder",["BTC/USD", 1, 1, "0.100000", "1234567", "1588678924349", "filled@null", "8745635"]
```

#### Limit order

Limit order can be customized with flags and TIF instructions.

Arguments with corresponding numeration of **limit** order **request**:

|  №   | Name       | Data type |  Example   |
| :--: | ---------- | :-------: | :--------: |
|  1   | instrument |  string   | "BTC/USD"  |
|  2   | order_type |  integer  |     2      |
|  3   | side       |  integer  |     1      |
|  4   | quantity   |  string   | "0.250000" |
|  5   | price      |  string   | "9120.00"  |
|  6   | tif        |  string   |     2      |
|  7   | cid        |  string   | "1234568"  |
|  8   | flags      |  integer  |     0      |

Arguments with corresponding numeration of **limit** order **response**:

|  №   | Name          | Data type |             Example             |
| :--: | ------------- | :-------: | :-----------------------------: |
|  1   | instrument    |  string   |            "BTC/USD"            |
|  2   | order_type    |  integer  |                2                |
|  3   | side          |  integer  |                1                |
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
[0,42,"createLimitOrder",["BTC/USD", 2, 1, "0.250000", "9120.00", 2, "1234568", 0]
```

Response:

```
[1,42,"createLimitOrder",["BTC/USD", 2, 1, "0.250000", "9120.00", 2, "1234568", 0, "1588678984376", "rejected@insufficient_balance", "8745985"]
```

#### Stop-loss order

Arguments with corresponding numeration of **stop-loss** order **request**:

|  №   | Name       | Data type |  Example   |
| :--: | ---------- | :-------: | :--------: |
|  1   | instrument |  string   | "BTC/USD"  |
|  2   | order_type |  integer  |     3      |
|  3   | side       |  integer  |     2      |
|  4   | quantity   |  string   | "0.250000" |
|  5   | stop_price |  string   | "9120.00"  |
|  6   | tif        |  string   |     2      |
|  7   | cid        |  string   | "1234568"  |
|  8   | flags      |  integer  |     0      |

Arguments with corresponding numeration of **stop-loss** order **response**:

|  №   | Name          | Data type |     Example     |
| :--: | ------------- | :-------: | :-------------: |
|  1   | instrument    |  string   |    "BTC/USD"    |
|  2   | order_type    |  integer  |        2        |
|  3   | side          |  integer  |        1        |
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
[0,42,"createStopLossOrder",["BTC/USD", 3, 2, "0.250000", "9120.00", 2, "1234568", 0]
```

Response:

```
[1,42,"createStopLossOrder",["BTC/USD", 3, 2, "0.250000", "9120.00", 2, "1234568", 0, "1588678984376", "active@null", "8745985"]
```

#### Stop-limit order

Arguments with corresponding numeration of **stop-limit** order **request**:

|  №   | Name       | Data type |  Example   |
| :--: | ---------- | :-------: | :--------: |
|  1   | instrument |  string   | "BTC/USD"  |
|  2   | order_type |  integer  |     4      |
|  3   | side       |  integer  |     1      |
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
|  2   | order_type    |  integer  |        4        |
|  3   | side          |  integer  |        1        |
|  4   | quantity      |  string   |   "0.250000"    |
|  5   | stop_price    |  string   |    "9120.00"    |
|  6   | price         |  string   |    "9118.00"    |
|  7   | tif           |  string   |        2        |
|  8   | cid           |  string   |    "1234568"    |
|  9   | flags         |  integer  |        0        |
|  10  | timestamp     |  string   | "1588678984376" |
|  11  | status@reason |  string   |  "active@null"  |
|  12  | order_id      |  string   |    "8745985"    |

Example of the messages 

Request:

```
[0,42,"createStopLimitOrder",["BTC/USD", 4, 1, "0.250000", "9120.00", "9118.00", 2, "1234568", 0]
```

Response:

```
[1,42,"createStopLimitOrder",["BTC/USD", 4, 1, "0.250000", "9120.00", "9118.00", 2, "1234568", 0, "1588678984376", "active@null", "8745985"]
```

#### OCO order

Arguments with corresponding numeration of **OCO** order **request**:

|  №   | Name           | Data type |  Example   |
| :--: | -------------- | :-------: | :--------: |
|  1   | instrument     |  string   | "BTC/USD"  |
|  2   | order_type     |  integer  |     5      |
|  3   | side           |  integer  |     2      |
|  4   | quantity       |  string   | "0.250000" |
|  5   | oco_stop_price |  string   | "9100.00"  |
|  6   | price          |  string   | "9118.00"  |
|  7   | tif            |  string   |     2      |
|  8   | cid            |  string   | "1234568"  |
|  9   | flags          |  integer  |   16384    |

Arguments with corresponding numeration of **OCO** order **response**:

|  №   | Name           | Data type |     Example     |
| :--: | -------------- | :-------: | :-------------: |
|  1   | instrument     |  string   |    "BTC/USD"    |
|  2   | order_type     |  integer  |        5        |
|  3   | side           |  integer  |        2        |
|  4   | quantity       |  string   |   "0.250000"    |
|  5   | oco_stop_price |  string   |    "9100.00"    |
|  6   | price          |  string   |    "9118.00"    |
|  7   | tif            |  string   |        2        |
|  8   | cid            |  string   |    "1234568"    |
|  9   | flags          |  integer  |      16384      |
|  10  | timestamp      |  string   | "1588678984376" |
|  11  | status@reason  |  string   |  "active@null"  |
|  12  | order_id       |  string   |    "8745985"    |

Example of the messages 

Request:

```
[0,42,"createStopLimitOrder",["BTC/USD", 5, 2, "0.250000", "9100.00", "9118.00", 2, "1234568", 16384]
```

Response:

```
[1,42,"createStopLimitOrder",["BTC/USD", 5, 2, "0.250000", "9100.00", "9118.00", 2, "1234568", 16384, "1588678984376", "active@null", "8745985"]
```

#### Bulk order

Bulk order is an order that instead of an array of arguments uses object format to send multiple orders within one request.  Also, bulk order can be used to send different order types with one request. *We need to specify parsing order of orders inside the bulk order.*

Example of the messages 

Request:

```
[0,42,"createBulkOrder",[[order_1_request], [order_2_request], [order_3_request], [order_4_request]]
```

Response:

```
[1,42,"createBulkOrder",[[order_1_response], [order_2_response], [order_3_response], [order_4_response]]
```

### Public events streams

### Kline (also known as OHLC)

**Arguments**

| Argument      | Description                                               |
| ------------- | --------------------------------------------------------- |
| Market ID     | market unique identifier                                  |
| Period        | Perid                                                     |
| Timestamp     | Timestamp of the period start                             |
| Open price    | Price of the first trade in the period                    |
| High price    | Highest price of the period                               |
| Low price     | Lowest price of the period                                |
| Close price   | Price of the last price of the period                     |
| Period volume | (optional) Sum of trades amount of the period             |
| Average price | (optional) Weighted average price of trades of the period |

```json
[3,0,"kline",["ethbtc","15m",1588791600,0.025353,0.025353,0.025353,0.025353,10,0.025353]]
```


### Tickers

```json
[3,0,"tikers",[
["ethbtc",1588791600,0.025353,0.025353,0.025353,0.025353,10,0.025353],
["bchbtc",1588791600,0.025353,0.025353,0.025353,0.025353,10,0.025353]]]
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


### Orderbook

## Private events streams
### Order update
### Trade event
### Balance change




