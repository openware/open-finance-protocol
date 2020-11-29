---
sort: 1
---
# Introduction to the RPC protocol

The protocol is aimed to be used through web-socket but is not limited to it, any bi-directional communication protocol can be used to implement this standard.

Every event is formatted in a JSON array like the following:

```
[type, request_id, method, arguments]
```

| Element    | Description                                                  |
| ---------- | ------------------------------------------------------------ |
| type       | This element identifies the type of the event (see the table below) |
| request_id | This is a request ID set by the client to identify the request and the corresponding response. It must be an unsigned integer. It's absent for events. |
| method     | method name to be called                                     |
| arguments  | list of arguments for the method                             |

## Types of messages

| Type ID | Short description |
| ------- | ----------------- |
| 1       | Request           |
| 2       | Response          |
| 3       | Public event      |
| 4       | Private event     |
