# comet and clients protocols
comet supports two protocols to communicate with client: WebSocket, TCP

## websocket                                                                   
**Request URL**

ws://DOMAIN/sub

**HTTP Request Method**

WebSocket (JSON Frame). Response is same as the request.

**Response Result**

```json
{
    "ver": 102,
    "op": 10,
    "seq": 10,
    "body": {"data": "xxx"}
}
```

**Request and Response Parameters**

| parameter     | is required  | type | comment|
| :-----     | :---  | :--- | :---       |
| ver        | true  | int | Protocol version |
| op         | true  | int    | Operation |
| seq        | true  | int    | Sequence number (Server returned number maps to client sent) |
| body        | json          | The JSON message pushed |

## tcp                                                                         
**Request URL**

tcp://DOMAIN

**Protocol**

Binary. Response is same as the request.

**Request and Response Parameters**

| parameter     | is required  | type | comment|
| :-----     | :---  | :--- | :---       |
| package length        | true  | int32 bigendian | package length |
| header Length         | true  | int16 bigendian    | header length |
| ver        | true  | int16 bigendian    | Protocol version |
| operation          | true | int32 bigendian | Operation |
| seq         | true | int32 bigendian | jsonp callback |
| body         | false | binary | $(package lenth) - $(header length) |

## Operations
| operation     | comment | 
| :-----     | :---  |
| 2 | Client send heartbeat|
| 3 | Server reply heartbeat|
| 7 | authentication request |
| 8 | authentication response |
| 9 | receive raw message (batch messages). sub message operation 18 and 20 |
| 18 | send message to other request |
| 19 | send message to other  response |
| 20 | send message to group request |
| 21 | send message to group response |

### operation=2

```$xslt
    header 
```

### operation=7 

```json
    {"mid":123, "mkey":"xxoo"}
```

### operation=9

| field | type | commet|
| :-----     | :---  | :---     |
| header(1) | header | message header 1 |
| body(1)| body | message body 1|
| header(2) | header | message header 2 |
| body(2)| body | message body 2 |
| ... | ... | ... |
| header(n) | header | message header n |
| body(n)| body | message body n |

    
### operation=18 

- send message

| field | type | commet|
| :-----     | :---  | :---     |
| from | int32 | sender mid |
| to | int32 | receiver mid |
| msg | []byte | message body |

- receive message:

| field | type | commet|
| :-----     | :---  | :---     |
| chatId | int64 | chat seq id |
| from | int32 | sender mid |
| to | int32 | receiver mid |
| msg | []byte | message body |

### operation=19

| field | type | commet|
| :-----     | :---  | :---     |
| ret | int8 | result 0:succ -1:failed |
| chatId | int64 | chat index |

### operation=20

- send message

| field | type | commet|
| :-----     | :---  | :---     |
| from | int32 | sender mid |
| to | int32 | receiver group id |
| msg | []byte | message body |

- receive message

| field | type | commet|
| :-----     | :---  | :---     |
| chatId | int64 | chat seq id |
| from | int32 | sender mid |
| to | int32 | receiver group id |
| msg | []byte | message body |

### operation=21

| field | type | commet|
| :-----     | :---  | :---     |
| ret | int8 | result 0:succ -1:failed |
| chatId | int64 | chat index |
