# Protocol

This protocol uses TCP to send messages. !!!!Ports from 7000 to 7500 should be open.!!!! Each message must include a `version`. If the versions mismatch, they must drop each other.

## Messages

All messages must follow this format.

| Field Name | Field Type | Field Size | Notes |
| --------------- | --------------- | --------------- | --------------- |
| length | uint32_t | 4 bytes | length of the rest of the message in bytes |
| version | uint32_t | 4 bytes | Version of the protocol being used |
| command | char[] | depends | must be a null-terminated string  |
| payload | depends on payload | depends | payload must take up the rest of the message |

### `connectNode`

This is the handshake a node sends other nodes when it joins the network, or wants to connect to other nodes. If this is a new node it must send this message to all nodes it intends to make part of its group.

#### Format

| Field Name | Field Type | Notes |
| --------------- | --------------- | --------------- |
| connectNode | uint8_t | `0x01` to indicate it wants to connect |
| blocksAmount | uint64_t  | amount of blocks the node currently has |

### `connectNodeResponse`

This is the response nodes send to the node after the `connectNode` message. If the blocksAmount is 0, this is a new node and it should start sending all blocks back, else the other node will most likely send `getAllHashes`.

#### Format

| Field Name | Field Type | Notes |
| --------------- | --------------- | --------------- |
| connectNodeResponse | uint8_t | `0x01` to let the client know it can connect, `0x02` if it can't |
| blocksAmount | uint64_t  | amount of blocks the node currently has |

### `getAllHashes`

Get a list of all hashes that the other node has so it can see which blocks it is missing or should be updated.

#### Format

| Field Name | Field Type | Notes |
| --------------- | --------------- | --------------- |
| getAllHashes | uint8_t | `0x01` to indicate it wants all hashes |

### `allHashes`

Send a list of all hashes

#### Format

| Field Name | Field Type | Notes |
| --------------- | --------------- | --------------- |
| allHashes | char[] | a list of all the hashes |

### `getHashFromBlock`

Ask for a block from a hash

| Field Name | Field Type | Notes |
| --------------- | --------------- | --------------- |
| getHashFromBlock | char[] | hash of the block |

### `sendBlock`

This response can be uses when a new domain is registered, or when a new node joins the network. This must be sent immediately after `newNodeResponse` if it is a new node joining the network.

#### Format

| Field Name | Field Type | Notes |
| --------------- | --------------- | --------------- |
| sendBlock | char[] | serialized version of a block |

### `invalidBlock`

If a block is expired or invalid, the node must send an alert to all nodes in it's group that it is expired.

#### Format

| Field Name | Field Type | Notes |
| --------------- | --------------- | --------------- |
| invalidBlock | char[] | a hash used to query for a block and check |

### `transferOwnership`

Transfer the owner of a domain.

#### Format

| Field Name | Field Type | Notes |
| --------------- | --------------- | --------------- |
| transferOwnership | char[] | signed public key of the next owner by the current private key, must be null-terminated |

### `getNodes`

When a node first joins or wants to expand it's group it will ask for new nodes.

| Field Name | Field Type | Notes |
| --------------- | --------------- | --------------- |
| getNodes | uint8_t | `0x01` to indicate it wants new nodes |

### `sendNodes`

| Field Name | Field Type | Notes |
| --------------- | --------------- | --------------- |
| sendNodes | char[] | list of nodes and their ip addresses seperated by a comma (ex: ipaddress,ipaddress,ipaddress), if there are no nodes respond with a 0 (still in char[]) |

### `sendIP`

| Field Name | Field Type | Notes |
| --------------- | --------------- | --------------- |
| sendIP | char[] | public IP address of the node |
