#   [The Log: What every software engineer should know about real-time data's unifying abstraction](https://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying)

Logs are a **data structure**, where **records** are appended to the end. Logs are read from top-to-bottom (left-to-right).

This post is not about application logging, rather about logging that is used by software. Like logs that are written to before database changes are made for purposes of atomicity, replication and restoration.

**Logs solve 2 of the main issues in distributed systems, ordering changes & distributing data.**

> If two identical, deterministic processes begin in the same state and get the same inputs in the same order, they will produce the same output and end in the same state.

As long as 2 nodes process the log in the same way, they will reach the same end state. It doesn't really matter what is put in the log however, as long as it is **enough** for nodes to remain consistent.

In databases we have **physical logging**, which is logging the contents of each row that was changed. We also have **logical logging**, which is not what was changed but which command was executed to change the row.

Similarly in other distributed systems we have logical and physical logging, where all nodes either process the full request or one node does and then distributes the results to other nodes.

Logs can serve as a sort of backup of the entire state. Version control is essentially a log. Additionally logs provide logical clocks in systems where multiple nodes synchronize a single log. This means we can say for example, don't read from any process that has not synchronized up to logical clock `x`.

Logs act as a buffer making data production asynchronous from data consumption.

The author uses log because they more specific about semantics, they are a messaging system with durability guarantees and strong ordering semantic, unlike more generic messaging systems or pub sub. In distributed systems this is often also called an [atomic broadcast](https://en.wikipedia.org/wiki/Atomic_broadcast).

Post pretty much ends with an explanation of kafka.






