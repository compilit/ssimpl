# Decentralised Storage

Traditionally, large tech companies have been responsible for storing data. This centralised model comes with several drawbacks, including single points of failure, high costs, and control over access to information.

Decentralised storage offers an alternative by distributing data across multiple nodes instead of relying on a central server. This approach provides several advantages:
- Eliminates Single Points of Failure – No central server means no single point of failure, reducing the risk of data loss or downtime.
- Censorship Resistance – Data isn’t controlled by a single entity, making it harder for governments or organizations to censor or remove information.
- Improved Availability – Multiple copies of data are stored across a network, ensuring accessibility even if some nodes go offline.
- Lower Costs – Users can share and utilize spare storage from others, reducing infrastructure costs compared to traditional cloud providers.
- Tamper Resistance – Cryptographic hashing and content addressing ensure data integrity and prevent unauthorized modifications.
- Trustless Environment – No need to rely on a central authority; cryptographic verification ensures data is stored and retrieved correctly.

## Decentralised Storage in DoaToa

For DoaToa, a peer-to-peer approach aligns best with its principles. The InterPlanetary File System (IPFS) is a Decentralised, peer-to-peer protocol for storing and sharing data. Unlike traditional location-based addressing (where URLs point to specific servers), IPFS uses content addressing, identifying files by their cryptographic hash (CID).

### Key Features of IPFS:
- Content Addressing – Files are identified by unique hashes, ensuring integrity and deduplication.
- Peer-to-Peer Distribution – Files are retrieved from multiple nodes rather than a central server.
- Versioning – IPFS can track changes, similar to Git.
- Caching – Popular content is cached across the network for faster access.
- Interoperability – Works well with blockchain and Decentralised applications (dApps).

### Problems IPFS Solves:
- Redundancy & Availability – Data remains accessible without reliance on a single server.
- Censorship Resistance – Information remains online as long as at least one node hosts it.
- Efficient Content Delivery – Data is retrieved from the nearest available node, reducing latency.

However, IPFS alone does not guarantee permanent storage—data can disappear if no node continues to host it. To ensure persistence, data must be pinned. This can be done either manually (free) or through a pinning service. Currently, DoaToa uses a pinning service, but in the future, your wallet will evolve to support self-hosting and pinning, ensuring full control over data storage.