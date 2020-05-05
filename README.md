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
|Element|Description|
|-------|-----------|
|type|This element identifies the event type: 1 for requests, 2 for responses and 3 for events|
|request_id|This is a request ID set by the client to identify the resquest and the associated response. It must be an unsigned integer.|
|method|method name to be called|
|arguments|list of arguments for the method|


## Authentication

## RPC Methods

### Subscribe to events streams

|Argument|Description|
|--------|-----------|
|Scope|"public" or "private", the client needs to be authenticated to subscribe to a private stream |
|Streams|Array of strings representing the streams to subscribe to|

Request example:
```
[1,42,"subscribe",["public", ["eurusd.trades", "eurusd.orderbook", "eurusd.tickers"]]]
[1,43,"subscribe",["private", ["orders", "trades"]]]
```

Response example:
```
[2,42,"subscribe",["public", ["eurusd.trades", "eurusd.orderbook", "eurusd.tickers"]]]
[2,43,"subscribe",["private", ["orders", "trades"]]]
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
[2,42,"unsubscribe",["public", ["eurusd.trades","eurusd.tickers"]]]
```

The response returns the list of remaining subscriptions for the current connection after the unsubsciption is performed.

### Orders
#### Market order
#### Limit order

## Events streams
### Trades
### Tickers
### Orderbook
### Klines


