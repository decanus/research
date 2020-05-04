# Notes: [Scribe](https://people.mpi-sws.org/~druschel/publications/Scribe-jsac.pdf)

> SCRIBE is a scalable application-level multicast infrastructure.

Network-level IP multicast was never widely deployed for various reason such the difficulty of how to track groups. This is why application-level multicast has gained popularity. These however still face certain challenges, like being able to scale and tolerate the failure modes of the general internet, while achieving low delay and effective use of resources. DHT's however offer scalable, self-organizing, fault-tolerant overlay networks for decentrallized distributed applications. Scribe is built on top of the [pastry DHT](https://en.wikipedia.org/wiki/Pastry_(DHT)).

Scribe requires all participants to share equal responsibilities. It builds a multicast tree from each group member to a groups rendezvous-point. Messaging and group management rely on the robustness of Pastry itself.

**Things I found interesting:**
 - Particularly what I find interesting about Scribe is how local it is, e.g. no specific node is responsible for the entirity of the group, it is all based on local information.

## Pastry

Each Pastry node has a unique ID, the set of node IDs is uniformly distributed. Pastry routes messages to the node that is closest to given key contained in the message numerically. Pastry can route in less than `log2^b(n)` steps, assuming a network of size `n`. `b` is a configuration parameter, typically `4`. Pastry tables required by nodes only have `(2^b - 1) * log2^b(n) + l` entries. Invariants in tables can be restored through `O(log2^b N)` messages.

Pastry routing provides 2 important properties for scribe.

1. **Short routes** - Due to the message being routed to the nearest node with a longer prefix match, models show that the average distance traveled by a message is between 1.59 and 2.2 times the distance between the source and destination in the underlying internet.
2. **Route convergence** - Simulations show that the average distance traveled by two messages sent to the same key, converge approximately at the point in their route equal to the distance between their respective source nodes.

**Node failure** - To handle node failures, nodes periodically exchange keep alive messages with their closes nodes, if a node is unresponsive for a certain period it is assumed failed.

## Scribe

Any scribe node may create a group, other nodes can join a group or send messages to that group. Scribe provides best-effort delivery and specifies no order. Scribe groups can have multiple sources of messages and many members.

Scribe exposes a simple API.

- `create(credentials, groupID)` - creates a new group.
  *credentials are later used for access control*
- `join(credentials, groupID, messageHandler)` - causes the local node to join a group, all received messages by the group are then sent to the message handler.
- `leave(credentials, groupID)` - causes the local node to leave a group.
- `multicast(credentials, groupID, message)` - causes the message to be multicast within the group.

Scribe is fully decentralized, each node can act as a multicast source, a root of a multicast tree, a group member, a node within a tree or any combination of the above.

### Implementation

`forward` and `deliver` are specified by the scribe application as required by pastry. The deliver method is called when a message arrives at the node with nodeId numerically closest to the message's key or when a message was addressed to the local node using the Pastry send operation. The forward method is called whenever a scribe message is routed through a node.

Each group has a unique groupID, the node with the numerically closest ID to the groupId acts as the rendezvous-point for the group. The groupID is a hash of the groups name concatenated with the creators name.

GroupIDs are distributed uniformly, just like node IDs, ensuring equal distribution of groups across nodes.

Scribe has the following message types:
 - `JOIN` - used to join a group. 
   If a node is asked to forward this message by pastry, and it is not yet a forwarder for this group it adds it to its set of groups to become a forwarder ([see "dissemninating messages"](#dissemninating-messages)), additionally it adds the source node as a child. It must then become a forwarder by sending a `JOIN` message to the next closest node to the group ID.
 - `CREATE` - used to create a new group with a groupID as the messages key.
 - `LEAVE` - used when a node wants to leave. First a node marks itself locally as left, if a node has no children it just sends this message to its parent node in the tree. The message is passed up recrusively until a node still has children after the departing node was removed.
 - `MULTICAST`

Proper credentials are needed to `JOIN`, `CREATE` or `MULTICAST`.

#### Dissemninating messages

Scribe creates a multicast tree to disseminate multicast messages, the tree is created using a scheme similar to reverse path forwarding. The tree is formed by joining the pastry routes from each group member to the rendezvous-point. Group joining is managed in a decentralized manner to support large and dynamic membership. Scribe nodes that are part of the tree are called **forwarders** and may or may not be part of the tree, each forwarder keeps a table containing each of its children in the tree.

Nodes cache the rendezvous point when multicasting, they do so by routing the `MULTICAST` message and asking the rendezvous point to return its IP address. This can then be used for subsequent messages to avoid routing. If the rendezvous-point fails, pastry is used to find subsequent rendezvous points. Multicast messages are disseminated from the rendezvous point along the multicast tree. A single rendezvous point is used, allowing for it to perform access control.

#### Repairing the multicast tree

Parents send heartbeat messages to all their children periodically, if a child fails to receive such a message it can assume that the parent has failed. If this is the case, it simply routes a `JOIN` message again using pastry, which will route that message to a new parent, fixing the tree.

In order to handle failure of rendezvous points, the state containing the group creator and an access control list is replicated across the `k` closest nodes to the root node (a typical value of K is 5). If the root fails, its chilren detect it and call `JOIN` through Pastry, this message is then routed to a new route.
