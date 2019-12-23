# Rendezvous

> TLDR; Rendezvous is a lightweight protocol for peer discovery.

Peers connect to a "rendezvous point", and register themselves. Other peers can then query that rendezvous point and then discover previously registered peers. When registering peers are assigned to one or more namespaces, this means they are found when querying for a specific topic.

**Interaction**

(Taken from docs)

```
A -> R: REGISTER{my-app, {QmA, Addr}}
R -> A: {OK}
D -> R: DISCOVER{ns: my-app}
R -> D: {[REGISTER{my-app, {QmA, Addr}}]}
```

**Problem:** The protocol is susceptible to spam attacks so it suggests using proof of work, however to make it useful for nodes that need lightweight discovery the proof of work would have to be so low that its usefulness as a spam prevention mechanism is questionable. 

## Attacks

Some of these are related more to the [status.im](https://status.im) application more than rendezvous itself.

- [Eclipse attacks](https://github.com/status-im/rendezvous/issues/6)
- [DDoS](https://github.com/status-im/rendezvous/issues/7)
- [Adversary Blockade](https://github.com/status-im/rendezvous/issues/8)

## References
- <https://github.com/libp2p/specs/tree/master/rendezvous>