# RFC Open Finance Protocol

## Summary

One paragraph explanation of the feature.

## Motivation

Why are we doing this? What use cases does it support? What is the expected outcome?

## Introduction to the RPC protocol

The protocol is aimed to be used through websocket.

Every request and responses are formated in a JSON array like the following:
```
[type, request_id, method, arguments]
```

- The first argument is 0 for requests and 1 for responses.

- The second argument is the request ID, the client can set the value he wants and the server will include it in the response. This helps to keep track of which response stands for which request. We use often 42 in our examples, you need to change this value by your own according to your implementation.

- Third is the method name

- Last is a list of arguments for the method

## Authentication

## RPC Methods
### Subscribe to events streams
Request example:
```
[0,42,"subscribe",["public", ["eurusd.trades", "eurusd.orderbook", "eurusd.tickers"]]]
[0,43,"subscribe",["private", ["orders", "trades"]]]
```

Response example:
```
[1,42,"subscribed",["public", ["eurusd.trades", "eurusd.orderbook", "eurusd.tickers"]]]
[1,43,"subscribed",["private", ["orders", "trades"]]]
```
The response send the full list of current public or private subscriptions for the current connection.

### Unsubscribe from events streams
Request:
```
[0,42,"unsubscribe",["public", ["eurusd.orderbook"]]
```

Response:
```
[1,42,"unsubscribe",["public", ["eurusd.trades","eurusd.tickers"]]]
```
The response send the full list of current public or private subscriptions for the current connection.

### Orders
#### Market order
#### Limit order

## Events streams
### Trades
### Tickers
### Orderbook
### Klines


