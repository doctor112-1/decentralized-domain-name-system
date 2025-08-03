# Decentralized DNS

## Introduction

At the time of this writing, billions of people use the internet today. One of the critical points of this internet that billions of people use is DNS. Yet, such a critical point of infrastructure suffers major flaws. One of these flaws is that it is centralized. ICANN and other institutions are responsible for the distribution and overall maintenance of domains. While this system provides stability, and allows a monitored way to register domains (which might not be the best for some people) it comes at a cost. That cost is a quite literal cost. For anyone to get a domain, they need to lease it for a price. There are no free domains in this system. The only free "domain" you could get is if it is a subdomain under someone else's domain. Even then, that domain your subdomain is under needs to be payed for. This creates a barrier to entry for anyone looking to get into the world of the internet and host something on it. In contrast, a decentralized DNS would allow anyone to register a domain for no price at all. Another problem that arises from this centralized system, is the problem of security. If ever a domain registry gets hacked, the hacker would get full access to every domain. Also, a decentralized DNS could help prevent domain hijacking.

## Rough Specification

For easier adoption, the DNS protocol is not changed. Instead, you run a DNS server that you can access and send DNS requests to. The data that each DNS server provides and how it gets it is changed.

### Abuse

A pro of centralized DNS and domains costing a price, is that it helps reduce abuse by locking it behind a paywall. In a decentralized DNS, since there is no cost for domains anyone could abuse and register multiple domains. A way to combat this would be by implementing a PoW (Proof-of-Work) system. A PoW system would make it so that registering a domain would cost significant computing power and stop the registering of domains over a short amount of time.

### Nodes

In the network there are 3 types of nodes, a full node, a miner node, and a client node. A full node keeps a copy of the whole database, validates and then adds or changes records, and also acts as a DNS server. A full node will also periodically read the database and check timestamps for any blocks more than a year old. If the block is more than a year old it will be deleted. This is to prevent someone from holding a domain indefinitely. To renew a domain, one would need to re-mine the block. Miners mine domains. Client nodes are also a DNS server but instead of maintaining a full database they ask full nodes for data, and cache a request if it happens multiple times.

### Storage

The decentralized DNS will not operate on a blockchain. The reason that a blockchain will be insufficient is that it tracks all transactions that ever happen. Over time, this would cause the DNS to get unnecessarily large. We would not be able to prune these transactions since they are all linked together. So instead we implement our own database. Like a blockchain, the building blocks of the database would be a block.

#### Blocks

Blocks are not linked together with the previous hash. Instead they stand alone in the blockchain. To prevent from anyone to updating a block we store each block with a public key that corresponds to the person who mined the block and the block has a hash that is signed with a private key. Whenever someone wants to update a record in the block, the records is hashed then signed with the private key that the person who mined it has. Then, that change is sent to each computer in the network. Each computer verifies it is official with the public key stored in the database and updates if it is official. To transfer ownership, the public key of the future owner should be signed using the current owner's private key.

Each block should have block headers that consist of a timestamp, a nonce, a target, and a domain name.

The hashRecord is a signed hash from all records, such as A records. The hashRecord does not go through Proof-of-Work.

The hash is calculated from the blockheaders.

For more information on blocks read [blocks.md](/blocks.md)

A problem with such an approach is that a certain computer in a network could modify their database and just remove or change the entry of a block and it's public key. To prevent this we implement a reputation system.

### Reputation

A reputation system operates on reputation. When a node first joins the network it will connect to at least 9 other nodes. These nodes will act as the node's group in the beginning as it downloads the database. It will then start downloading each block. For each block it downloads, it will check with the other nodes if they have that block. If the majority of the nodes have that block, those nodes get a higher reputation. For the nodes that don't have the block, or reported a different block, they will get a less reputation. If no nodes can agree on a block, that block goes into a temporary storage to be checked later, and no reputation is changed. At the end of the sync, it will look at all the nodes reputation, and whichever nodes have a low reputation (in contrast to other nodes), will get removed from it's group. Once the node downloads the database, it should try and expand it's group. Whenever it adds a new node to it's group, it will check a percentage of it's database with the node and increase or decrease it's reputation based on it. It will try to check as much as it can until it's reputation matches the average of the other nodes' reputation. Nodes will only accept new blocks if they are within their group. Whenever a full node adds a new record it should broadcast it to it's whole group. Also, if another node has more blocks, it should always check for what blocks it has and validate them, and check with other nodes to see if they also have more blocks. If most nodes in a group have more blocks, and the new or changed blocks are the same, it should accept them. If there is a conflict with two blocks being mined at the same time, it should get resolved with the database that gets added to first being the correct one, since it has the most amount of blocks.
