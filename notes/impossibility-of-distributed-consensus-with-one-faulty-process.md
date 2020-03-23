# Notes: Impossibility of Distributed Consensus with One Faulty Process

This paper won the [Dijkstra award](http://eatcs.org/index.php/dijkstra-prize).

Reaching agreement is one of the fundamental issues in distributed systems and is at the core of many algorithms. Reaching agreement is simple if all participating nodes are reliable as well as the network. However real-world systems can have multiple faults:
 - partitions
 - node crashes
 - lost, distorted or duplicated messages

Additionally Byzantine failures can occur where nodes conduct in completely adveserial behaviour. Ideally, we could have a protocol that is reliable in the face of such faults.

The paper shows that no completely asynchronous algorithm can tolerate even a single unannounced process death. Byzantine failures are not considered, and the message system is assumed to be reliable, all messages are delivered correctly and exactly once.

**The above means that this problem has no robust solutions, unless we make further assumptions on the enviroment and greater restrictions on the kinds of failures tolerated.**

Assumptions:
 - all processing is completely asynchronous.
 - there is no access to synchronized clocks.
 - it is impossible to tell whether a process has died or is simply slow.

We exclude algorithms that by default simply vote `NO` as this would be completely useless.

An algorithm that satisfies the below properties would solve the consensus problem:

- **Agreement**: Correct nodes `i` output the same value `o = o`.
- **Validity**: If all nodes have the same input `b`, then `o = b`.
- **Termination**: All correct nodes decide on an output and terminate.

The paper essentially states that if such an algorithm existed one could cause it to remain undecided for an arbitrary amount by delaying message delivery, which is allowed in asynchronous enviroments.

## References
 - https://www.the-paper-trail.org/post/2008-08-13-a-brief-tour-of-flp-impossibility/
 - https://www.the-paper-trail.org/post/2012-03-25-flp-and-cap-arent-the-same-thing/
 - https://groups.csail.mit.edu/tds/papers/Lynch/jacm85.pdf
 - http://resources.mpi-inf.mpg.de/departments/d1/teaching/ws14/ToDS/script/flp.pdf
 - https://www.youtube.com/watch?v=Vmlj-67aymw
 - https://hackernoon.com/connecting-the-dots-flp-bft-and-consensus-algorithms-m9r62bs1
 - http://book.mixu.net/distsys/abstractions.html

