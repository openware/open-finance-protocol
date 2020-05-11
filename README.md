# RFC Open Finance Protocol

## Summary

One paragraph explanation of the feature.

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
#### Market order
#### Limit order

## Public events streams
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




