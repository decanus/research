# Notes: [Effective Manycast Messaging for Kademlia Network](https://cs.baylor.edu/~donahoo/papers/MCD15.pdf)

Kademlia only allows contacting a single network peer. This paper descirbes an extension to Kademlia providing group communication.

Every group has an identifer the same way everyone node has one. Nodes can contact a members of the group in three ways.
 - **Anycast** - a message delivered to a single member.
 - **Multicast** - delivers to all memebers.
 - **Manycast** - delivers to `N` group memebers where `N` is defined by the sender.

> Interesting note: The benefit of Kademlia over Pastry is that Kademlia only uses one algorithm to find a specific node. Pastry instead uses one algorithm for half the distance and then switches to another. The first using the routing table, and the second using the leaf set.
> 
> The nodes found in the first stage may be geographically farther away, so Kademlia may have faster delivery times.
> 
> Kademlia however requires far more lookup messages.

For this Kademlia Group extension a tree architecture is used, similar to scribe. All messages are sent to the root node. As Kademlia does not use forwarding, but instead lookups. When a node receives a lookup for a group that it is a part of, it does not continue with the routing algorithm but instead sends back a message saying that it is the recepient.

When a peer wants to join a group, they send a lookup message to find the specific group. If a group memeber is found during the lookup, the join message can be sent that group memeber making them the join point.

An anycast message is sent the same way as a join message. The first node to receive this message is the anycast receiver.

A multicast message is received by all group memebers. The first phase is the same as sending an anycast message, then the message is distributed inside the group tree. Every group memeber forwards the message to its desecendants and parents ignoring the node from which the message came. This has a complexity of `O(log(n) + m)` where `n` is the number of peers in the network and `m` is the number of tree memebers.

Sending a manycast message is divided into two phases, the first being the same as the anycast. The second phase finds the an amount of manycast receivers which is done using Kademlia lookups and the message is then sent to those. This has a complexity of `O(log(n) + m)` where `n` is the number of peers in the network and `m` is the desired number of receivers.

The tree must be fault-tolerant, every memeber periodically sends a control message to its parent. If the parent cannot be reached the node finds another group member who is not its descendant. Upon receiving a join, a group member must verify the sender is not its ancestor.

## Test Results

Scribe has better time results for fewer manycast receivers, and Kademlia for more many cast receivers. Kademlia has a lower network load, however requires more traffic. The difference in traffic is far higher than in delivery time. Kademlia is better for clients with bigger connection limits.
