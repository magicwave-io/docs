# Technology

MagicWave is an opensource collection of services that allow users and publishers to consume and publish content, while avoiding censorship and maintaining a high degree of anonymity.

MagicWave achieves this by combining a distributed high performance content delivery network (based on IPFS) with Onion Routing. Monetization for publishers, storage providers and ongoing routing providers is tokenized (based on the Stellar blockchain) and anoynmized.

## Monetization layer.

MagicWave makes deploying onion ervers profitable, which in turn means a faster network.

In addition, the MagicWave protocol pays content providers (i.e. web sites), encouraging creation and sharing of new content, without the need to rely on tracking ads.

## Caching layer.

MagicWave incorporates IPFS for distributed storage and caching. This means web sites hosted on MagicWave can concentrate on creating great content. Storage providers are also incentivised via a monetization layer, resulting in faster and easier access.

## The details.

MagicWave relies on Stellar-based tokens (MagicWave Token – “MWT”) to pay for bandwidth, storage and content.

MagicWave Clients receive an allocation of tokens as part of the MagicWave subscription (initially free), which are sufficient for covering normal usage.

The official MagicWave Client is implemented as a Google Chrome Extension (with some supporting services running in the background as a Mac/Windows app). In the future, we envision extending support to more platforms.

These tokens are used to pay for services on the MagicWave network anonymously.

## Payments and anonymity

When a client accesses the MagicWave network, it accesses an Onion “guard” node. The MagicWave client will maintain a small “balance” with the guard. The client can then send requests (via onion routing) which are opaque to the guard, maintaining anonymity.

To maintain the anonymity of payments, payments are decoupled from requests: all nodes maintain balances with their peers. When the client sends an (onion) request for a web page (or an IPFS block), the guard checks the client’s balance, and passes the request to the next node in the circuit and so on until the exit node sends the request to the content provider (web site, IPFS). When the request is returned back to the client, each node updates the balance with the previous node on the chain.

As the client only pays to its guard node, and the content provider is only paid by the exit node, payments are anonymized, providing similar anonymity guarantees as provided by onion routing. Note that two relay nodes will usually route a similar number of messages going in either direction, and would therefore only rarely need to pay their peers, reducing the number of transactions that are submitted to the blockchain.

## Caching

MagicWave incorporates IPFS as a distributed storage and cache layer.

To maintain anonymity guaranties, all access to IPFS is done over Onion Routing, with clients paying for IPFS content anonymously as described above.

By default, MagicWave IPFS nodes implement a caching protocol: nodes automatically fetch popular content on the network. This improves the network performance, while also making running an IPFS node more profitable.

## The MagicWave protocol:

A MagicWave circuit is established as follows:

1. The Client randomly chooses a Middle Relay and an Exit node (the Guard node usually doesn’t change).
2. The Client composes an “onion” message which it sends to its Guard.
3. The Guard decodes the outermost layer and forwards the message to the next node on the chain, and charges the client’s MagicWave balance for the service. It then forward the message to the Middle Relay it decoded from the onion message. The Middle Relay similarly charges the Guard node, decodes its layer of the onion and forwards the message to the Exit node, which finally forwards the message to its destination (whether on the MagicWave network or on the Internet). The same process in reverse happens for the reply.
4. Each node maintains a balance with its peers and with clients (if it’s a Guard node). When the balance goes over a threshold (by default, on average, after about 50MB of data), the node sends a payment request to the peer. Note that for internal (not client) nodes, requests will flow in both ways, and therefore only rarely require settlement of the balance. This reduces the load on the Stellar network and improves network performance. As a bonus, this also improves anonymity as less records are maintained on the blockchain.
5. On receiving payment requests, the peer node (or client) performs the actual payment of MWT tokens. As these payments are done between peers, anonymity is maintained.
Once the payment goes through, the balance is “topped up”.