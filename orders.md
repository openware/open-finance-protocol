---
sort: 4
---

# Orders

In our standard, the parameters order plays an important role as the position of the parameter represents a key. All utilized parameters must be present in request and response even if they are not utilized. To skip optional parameter use a `null` value.

**Order types** supported with `create_order` method:

| Order type | Description                                                     |
| :--------: | --------------------------------------------------------------- |
|     m      | Market order                                                    |
|     l      | Limit order                                                     |
|     p      | Post only (Must not fill at all or is canceled)                 |
|     f      | Fill or kill (Must fully match at a given price or is canceled) |
|     s      | Stop                                                            |
|     sl     | Stop-limit (available flags: OCO)                               |

**All arguments that used for order creation order**:

| №   | Name          | Data type | Description                                   |
| --- | ------------- | --------- | --------------------------------------------- |
| 1   | instrument    | string    | Used to specify instrument for order "btcusd" |
| 2   | order_type    | string    | Used to specify order type                    |
| 3   | side          | string    | Used to specify order side, "buy" or "sell"   |
| 4   | quantity      | string    | An amount that placed within the order        |
| 5   | price         | string    | Main (limit) price of the order               |
| 6   | trigger_price | string    | Trigger (Stop) price of the order             |
| 7   | tif           | string    | Time in force instruction                     |
| 9   | flags         | integer   | Used to set custom order instructions         |
| 10  | timestamp     | integer   | UTC timestamp                                 |
| 11  | status        | string    | Status of the order                           |
| 12  | message       | string    | Message related to the status of the order    |
| 13  | order_id      | string    | Send by exchange with response for orders     |

Supported **TIF** instructions:

| Code | Name | Description                                        |
| ---- | ---- | -------------------------------------------------- |
| 1    | IOC  | Allows partial execution, unfilled part canceling. |
| 2    | GTC  | Remain active until cancellation on stop.          |
| 3    | FOK  | Allows only full execution.                        |

Supported order **flags** instructions:

| Code | Flag         | Type    | Description                                                                                                                                                                     |
| ---- | ------------ | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1    | Hidden       | integer | The hidden order option ensures an order does not appear in the order book; thus does not influence other market participants.                                                  |
| 2    | Close        | integer | Close position if position present.                                                                                                                                             |
| 4    | Reduce Only  | integer | Ensures that the executed order does not flip the opened position.                                                                                                              |
| 8    | Post Only    | integer | The post-only limit order option ensures the limit order will be added to the order book and not match with a pre-existing order.                                               |
| 16   | OCO          | integer | The one cancels other order option allows you to place a pair of orders stipulating that if one order is executed fully or partially, then the other is automatically canceled. |
| 32   | No Var Rates | integer | Excludes variable rate funding offers from matching against this order, if on margin                                                                                            |

For **timestamps** use UTC time that expressed as milliseconds (i.e. 1588678924349)

**Order statuses table**:

| Code | Status   | Description                                                                  |
| ---- | -------- | ---------------------------------------------------------------------------- |
| 1    | Pending  | Order received by the exchange but haven't been placed in the order book yet |
| 2    | Wait     | Order placed in the order book and waits for execution                       |
| 3    | Done     | Order fully executed                                                         |
| 4    | Rejected | Order rejected due too some errors                                           |
| 5    | Canceled | Order canceled by user                                                       |

**Bellow you can find examples of different order types:**

## Market order

_Method name: create_order_

Arguments with corresponding numeration of **market** order **request**:

|  №  | Name       | Data type |  Example   |
| :-: | ---------- | :-------: | :--------: |
|  1  | instrument |  string   |  "btcusd"  |
|  2  | order_type |  string   |    "m"     |
|  3  | side       |  string   |   "buy"    |
|  4  | quantity   |  string   | "0.100000" |

Arguments with corresponding numeration of **market** order **response**:

|  №  | Name | Data type |                Example                 |
| :-: | ---- | :-------: | :------------------------------------: |
|  1  | uuid |  string   | "26eba5be-a0f9-11ea-afe8-0242ac190008" |

Arguments with corresponding numeration of **market** order **update notifications**:

|  №  | Name              | Data type |                Example                 |
| :-: | ----------------- | :-------: | :------------------------------------: |
|  1  | instrument        |  string   |                "btcusd"                |
|  2  | id                |  integer  |                8745635                 |
|  3  | uuid              |  string   | "12d9ff0a-9b7b-11ea-ac47-0242ac170004" |
|  4  | side              |  string   |                 "buy"                  |
|  5  | status            |  string   |                  "d"                   |
|  6  | order_type        |  string   |                  "m"                   |
|  7  | price             |  string   |                  "0"                   |
|  8  | avg_price         |  string   |                 "3.5"                  |
|  9  | quantity          |  string   |                  "0"                   |
| 10  | origin_quantity   |  string   |                  "10"                  |
| 11  | executed_quantity |  string   |                  "10"                  |
| 12  | trades_count      |  integer  |                   2                    |
| 13  | timestamp         |  integer  |             1588678924349              |
| 14  | reject_reason     |  string   |        "rejected by orderbook"         |

Example of the messages

Request:

```json
[1, 42, "create_order", ["btcusd", "m", "sell", "0.100000", "1234567"]]
```

Response:

```json
[2, 42, "create_order", ["5b2c0ea2-9363-11eb-adb6-1831bf9834b0"]][
[4,"on",["btcusd",4,"5b2c0ea2-9363-11eb-adb6-1831bf9834b0","sell","d","m","0","3.5","0","10","10",2,1617334050]]
```

## Limit order

Limit order can be customized with flags and TIF instructions.

Arguments with corresponding numeration of **limit** order **request**:

|  №  | Name       | Data type |  Example   |
| :-: | ---------- | :-------: | :--------: |
|  1  | instrument |  string   |  "btcusd"  |
|  2  | order_type |  string   |    "m"     |
|  3  | side       |  string   |   "buy"    |
|  4  | quantity   |  string   | "0.100000" |
|  5  | price      |  string   | "0.100000" |

Arguments with corresponding numeration of **limit** order **response**:

|  №  | Name | Data type |                Example                 |
| :-: | ---- | :-------: | :------------------------------------: |
|  1  | uuid |  string   | "26eba5be-a0f9-11ea-afe8-0242ac190008" |

Arguments with corresponding numeration of **market** order **update notifications**:

|  №  | Name              | Data type |                Example                 |
| :-: | ----------------- | :-------: | :------------------------------------: |
|  1  | instrument        |  string   |                "btcusd"                |
|  2  | id                |  integer  |                8745635                 |
|  3  | uuid              |  string   | "12d9ff0a-9b7b-11ea-ac47-0242ac170004" |
|  4  | side              |  string   |                 "buy"                  |
|  5  | status            |  string   |                  "d"                   |
|  6  | order_type        |  string   |                  "m"                   |
|  7  | price             |  string   |                  "0"                   |
|  8  | avg_price         |  string   |                 "3.5"                  |
|  9  | quantity          |  string   |                  "0"                   |
| 10  | origin_quantity   |  string   |                  "10"                  |
| 11  | executed_quantity |  string   |                  "10"                  |
| 12  | trades_count      |  integer  |                   2                    |
| 13  | timestamp         |  integer  |             1588678924349              |
| 14  | reject_reason     |  string   |        "rejected by orderbook"         |

Example of the messages

Request:

```json
[1,42,"create_order",["btcusd", "l", "buy", "0.250000", "9120.00", 2, "1234568", 0]
```

Response:

```json
[2,42,"create_order",["30154236-9364-11eb-b9b2-1831bf9834b0"]
[4,"or",["btcusd",2,"30154236-9364-11eb-b9b2-1831bf9834b0","buy","r","l","10","0","0.5","0.5","0",0,1617334407,"rejected by orderbook"]]
```

## Stop order

Arguments with corresponding numeration of **stop** order **request**:

|  №  | Name          | Data type |  Example   |
| :-: | ------------- | :-------: | :--------: |
|  1  | instrument    |  string   |  "btcusd"  |
|  2  | order_type    |  string   |    "s"     |
|  3  | side          |  string   |   "sell"   |
|  4  | quantity      |  string   | "0.250000" |
|  5  | trigger_price |  string   | "9120.00"  |
|  6  | tif           |  integer  |     2      |
|  7  | cid           |  string   | "1234568"  |
|  8  | flags         |  integer  |     0      |

Arguments with corresponding numeration of **stop** order **response**:

|  №  | Name          | Data type |    Example    |
| :-: | ------------- | :-------: | :-----------: |
|  1  | instrument    |  string   |   "btcusd"    |
|  2  | order_type    |  string   |      "s"      |
|  3  | side          |  string   |    "sell"     |
|  4  | quantity      |  string   |  "0.250000"   |
|  5  | trigger_price |  string   |   "9120.00"   |
|  6  | tif           |  integer  |       2       |
|  7  | cid           |  string   |   "1234568"   |
|  8  | flags         |  integer  |       0       |
|  9  | timestamp     |  integer  | 1588678984376 |
| 10  | status        |  string   |   "active"    |
| 11  | message       |  string   |      ""       |
| 12  | order_id      |  string   |   "8745985"   |

Example of the messages

Request:

```
[1,42,"create_order",["btcusd", "s", "sell", "0.250000", "9120.00", 2, "1234568", 0]
```

Response:

```
[2,42,"create_order",["btcusd", "s", "sell", "0.250000", "9120.00", 2, "1234568", 0, 1588678984376, "active", "", "8745985"]
```

## Stop-limit order

Arguments with corresponding numeration of **stop-limit** order **request**:

|  №  | Name          | Data type |  Example   |
| :-: | ------------- | :-------: | :--------: |
|  1  | instrument    |  string   |  "btcusd"  |
|  2  | order_type    |  string   |    "sl"    |
|  3  | side          |  string   |   "buy"    |
|  4  | quantity      |  string   | "0.250000" |
|  5  | trigger_price |  string   | "9120.00"  |
|  6  | price         |  string   | "9118.00"  |
|  7  | tif           |  integer  |     2      |
|  8  | cid           |  string   | "1234568"  |
|  9  | flags         |  integer  |     0      |

Arguments with corresponding numeration of **stop-limit** order **response**:

|  №  | Name          | Data type |    Example    |
| :-: | ------------- | :-------: | :-----------: |
|  1  | instrument    |  string   |   "btcusd"    |
|  2  | order_type    |  string   |     "sl"      |
|  3  | side          |  string   |     "buy"     |
|  4  | quantity      |  string   |  "0.250000"   |
|  5  | price         |  string   |   "9118.00"   |
|  6  | trigger_price |  string   |   "9120.00"   |
|  7  | tif           |  integer  |       2       |
|  8  | cid           |  string   |   "1234568"   |
|  9  | flags         |  integer  |       0       |
| 10  | timestamp     |  integer  | 1588678984376 |
| 11  | status        |  string   |   "active"    |
| 12  | message       |  string   |      ""       |
| 13  | order_id      |  string   |   "8745985"   |

Example of the messages

Request:

```
[1,42,"create_order",["btcusd", "sl", "buy", "0.250000", "9120.00", "9118.00", 2, "1234568", 0]
```

Response:

```
[2,42,"create_order",["btcusd", "sl", "buy", "0.250000", "9120.00", "9118.00", 2, "1234568", 0, 1588678984376, "active", "", "8745985"]
```

## Bulk order

Bulk order uses `create_bulk` method to send multiple orders within one request. Also, bulk order can be used to send different order types with one request. _We need to specify parsing order of orders inside the bulk order._

Example of the messages

Request:

```
[1,42,"create_bulk",[[order_1_request], [order_2_request], [order_3_request], [order_4_request]]
```

Response:

```
[2,42,"create_bulk",[[order_1_response], [order_2_response], [order_3_response], [order_4_response]]
```
