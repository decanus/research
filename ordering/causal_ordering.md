# Causal Ordering

One property of Distributed Systems is that messages sent between nodes may arrive zero or more times at any point after they are sent.

This property makes it **impossible** to agree on time between multiple nodes. If we can't agree on time, we also can't fully agree on the order of events.

As a result, we may not be able to fully agree on the order of events based on a specific time, but we can order the events causally.

Causal ordering is conveyed between messages, since sending messages is the only way one node can affect another. Node `a` sends node `b` a message, and node `b` then acknowledges this message. This shows a clear causal relationship between these events which we can use as an order. 

Causal ordering is only a **partial ordering** because it is possible that events happen with no causal relationship. Time however would be a **total ordering**, meaning that every event can be placed into a specific order regardless of causal relationship by simply relying on chronology.

Since we have no notion of global time, communication is the only thing that allows us to reason about causality in a distributed system.

![](../assets/causal_ordering.svg)

**Communication bounds causality**

## References
 - [Research Issue](https://github.com/decanus/research/issues/10)
