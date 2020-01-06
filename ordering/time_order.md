# [Time, Clocks, and the Ordering of Events in a Distributed System](https://lamport.azurewebsites.net/pubs/time-clocks.pdf)

Time is generally how we order events. Sometimes it is hard in a distributed system to state which event happened before another, the "happened before" relation is only a partial ordering of the system.

We can say that `a` happened before `b` if `a` causally affected `b`, we can say this without needing a notion of time within a system. Events are concurrent if neither causally affected the other.

With this ordering of events, we can add a `clock` to the system. The `clock` is a way of assigning a number to an event representing at which point in time an event has occurred. 

> clock **Ci** exists for every process **Pi** and assigns a number **Ci(a)** for every event **a** in that process.
>
> **C** represents the entire system of clocks where **C(b)**  assigns a number to event **b** where **C(b)** == **Cj(b)** if **b** is an event of process **j**.

The clocks we have implemented are **logical clocks** meaning they may be implemented with counters and not actual physical time. 

If an event `a` happened before `b` then the clock value of `a` should be smaller than that of `b`. The converse does not hold as this would mean that all concurrent events must happen at the same clock value. If 2 events occur on one node, they can both be concurrent to the same event on another node without occurring at the same clock value.

Changing the value of ``Ci`` takes place between events, meaning that the changing of the value does not itself constitute an event.

When a node receives a message from another node, that message contains a clock value indicating its clock value on the sender node, the receiving node must then set its own clock value to be greater than the one received.

We can use these clocks to totally order the set of all events that occurred in our system by sorting them using their clock values. To break ties we use an arbitrary ordering of events.
