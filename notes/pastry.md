# Notes: [Pastry](http://rowstron.azurewebsites.net/PAST/pastry.pdf)

**Quick Facts**
 - Like most DHTs pastry routes messages to the closest numerical node.
 - Pastry requires `O(log N)` lookups, where `N` is the amount of nodes in the network.
 - At each node in the network the application is notified and may perform various actions with the message.

Nodes are given a `128` bit identifier. Node IDs are used to indicate a node's position in a circular node space from `0` to `(2^128) - 1`.

A node routes a message to a peer who shares a prefix of `b` bits longer than the key shares with the present nodes id. If such a node is not found, the message is forwarded to a node that shares the same prefix as the present node but is numerically closer.


## Node state

Each node maintains the following:
 
 - **Routing Table (`R`)** - The routing table is divided into `n` rows, an entry at row `n` shares the first same `n` digits of the present node's id. But its `n + 1` digit can be one of the `2^b - 1` values other than the `n + 1th` digit found in the present nodes id.
 - **Neighbourhood Set (`M`)** - Contains nodes that are closest according to the proximity metric, it is not usually used for routing. But is useful in maintaining locality properties. 
 - **Leaf Set (`L`)** - Is the set of nodes with the numerically closest larger and smaller node ids from the present node. It is used in routing.

The information stored for a specific node usually contains things such as the IP Address.

## Routing

Whenever a message arrives at a node, the following procedure is executed:
 1. If messages target falls between the range of the leaf set, the message is forwarded directly to the closest node in the leafset.
 2. If not, the routing table is used and the message is forwarded to a node with a common prefix by at least one more digit.
 3. If a node cannot be found at step 2, then the message is forwarded to a node with the same common prefix as the present node that is numerically closer to the message than the present node.

**Notes**
 - If a message is forwarded using the routing table, then each step reduces the set of nodes whose ids have a longer prefix match with the key by a factor of `2^b`, meaning messages are delivered within `log2^b(N)` steps.
 - If the key is within the range of the leaf set, then the message is at most 1 step away.

In the event of many simultanious node failures, the amount of routing steps required may be linear to `N`. Routing performance degrades gradually with the amount of node failures.

## Pastry API

- **`route(msg, key)`** - routes message to the node with the numerically closest ID to key.
- **`deliver(msg, key)`** - called by pastry when a node ID is numerically closest to the key.
- **`forward(msg, key, nextId)`** - called before a message is forwarded it the node with the id `nextId`. The application may change the message contents or the nextID, changing it to `NULL` would stop the message from being forwarded.
- **`newLeafSet(leafSet)`** - called by pastry whenever the leafset is updated.

## Self Organization

Now, let's look at what happens when nodes leave or join the network.

- **Arrival** - to join a network a node most initialize its node state. To join a network, a node sends a special `join` message to another node, with yet another node as the target. Pastry routes the message to a node numerically closest to the target id, all nodes that encounter the message, from the first node to the last send their state tables to the source. Which can request more information from a specific node if it feels the need to. The joining node then informs all required nodes of its arrival.

  @TODO DISCUSS FULL ARRIVAL ALGORITHM

- **Departure** - a node may fail or depart without warning. A node is considered failed when its immediate neighbours in the neighbourhood set can no longer communicate with it. To replace a failed node in a leafset, a node contacts the node with the largest index on the leaf set side of the failed node and asks that node for its leaf table. The leaf set will partially overlap but also contain nodes not currently in the node's leafset, it will pick an appropriate node to add to its leafset verifying that the node is actually alive. To replace a failed node in the routing table, a node contacts another entry of the same row and asks for that nodes entry at the same index, if none of the entries are appropriate, the node contacts a node at the next row. Eventually it will find a node. The neighbourhood set is not used for routing but must be kept up-to-date, to do so a node periodically calls every neighbour to see if it is still alive, in case it is not. It asks all the other neighbour for their neighbours, checks the distance of those, and updates the set accordingly.

@TODO LOCALITY & ARBITRARY FAILURES
