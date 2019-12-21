# Discovery in Ethereum

## Bootnodes

Ethereum seemingly has two methods of bootstrapping the network, the first being bootnodes, a list of which can be found [here](https://github.com/ethereum/go-ethereum/blob/master/params/bootnodes.go). 

Secondly, PR [#20094](https://github.com/ethereum/go-ethereum/pull/20094) introduced [EIP-1459](https://eips.ethereum.org/EIPS/eip-1459) which uses node discovery via DNS. TLDR; this method stores a merkle tree in DNS `TXT` records that other nodes then traverse to get their initial peers.

## Kademlia

For further discovery once bootnodes have been discovered, ethereum uses a modified version of kademlia. It ommits the `STORE` and `FIND_VALUE` RPCs and uses the rest of the DHT to gather information about peers. 

## References
 - <https://rchain.atlassian.net/wiki/spaces/CORE/pages/15564804/Ethereum+P2P+Node+Discovery+and+Routing>
 - <https://github.com/ethereum/wiki/wiki/Kademlia-Peer-Selection>
 - <https://eips.ethereum.org/EIPS/eip-1459>