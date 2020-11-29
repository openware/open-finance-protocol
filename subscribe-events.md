---
sort: 7
---
# Events subscriptions

## Subscribe to events streams

*Method name: subscribe*

| Argument | Description                                                                                  |
| -------- | -------------------------------------------------------------------------------------------- |
| Scope    | "public" or "private", the client needs to be authenticated to subscribe to a private stream |
| Streams  | Array of strings representing the streams to subscribe to                                    |

Request example:

```json
[1,42,"subscribe",["public",["eurusd.trades","eurusd.orderbook","tickers","eurusd.kline-15m"]]]
[1,43,"subscribe",["private",["orders","trades"]]]
```

Response example:

```json
[2,42,"subscribe",["public",["eurusd.trades","eurusd.orderbook","tickers","eurusd.kline-15m"]]]
[2,43,"subscribe",["private",["orders","trades"]]]
```

The response returns the list of **all** current subscriptions for the current connection after the subscription is performed.

## Unsubscribe from events streams

*Method name: unsubscribe*

| Argument | Description                                                                                    |
| -------- | ---------------------------------------------------------------------------------------------- |
| Scope    | "public" or "private", the client needs to be authenticated to unsubscribe to a private stream |
| Streams  | Array of strings representing the streams to unsubscribe to                                    |

Request:

```json
[1,42,"unsubscribe",["public", ["eurusd.orderbook"]]
```

Response:

```json
[2,42,"unsubscribe",["public", ["eurusd.trades","tickers"]]]
```

The response returns the list of remaining subscriptions for the current connection after the unsubsciption is performed.
