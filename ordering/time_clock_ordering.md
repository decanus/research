# [Time, clocks, and the ordering of events in a distributed system](https://lamport.azurewebsites.net/pubs/time-clocks.pdf)

> **Note:** It is worth reading [causal ordering](./causal_ordering.md) before this.

Time is generally how we order events. We need to change our thinking from "when" something happened, to "before" what it happened. This is one of the main changes in thinking, it allows ordering without time. The "happened before" relation is only a partial ordering of the system however.

<!--
First here are 2 important definitions:
 - **Total Ordering:** When every event can be placed into a specific order. A single node can have total ordering.
 - **Partial Ordering:** When we can be sure that dependent events are ordered. But we do not know the exact order for every event.


For ordering in our system, our distributed system has these rules:
 - **Single Process:** In a single process we have a total ordering, old events happened before new events that we see.
 - **Message Transmission:** All communication is asynchronous. Sending a message happens before a receiving a message.
 - **Transitivity:** If event `A` happened before `B` and `B` happened before `C` then `A` happened before `C`

We can say that `a` happened before `b` if a causally affected `b`, we can say this without needing a notion of time within a system. Events are concurrent if neither causally affected the other.
-->

