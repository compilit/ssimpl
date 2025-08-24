# IPFS

**IPFS (InterPlanetary File System)** is a **peer-to-peer distributed file system** that aims to make the web more *
*Decentralised**, **resilient**, and **permanent**.

# Decentralised Storage

Traditionally, large tech companies have been responsible for storing data. This centralised model comes with several
drawbacks, including single points of failure, high costs, and control over access to information.

Decentralised storage offers an alternative by distributing data across multiple nodes instead of relying on a central
server. This approach provides several advantages:

- Eliminates Single Points of Failure – No central server means no single point of failure, reducing the risk of data
  loss or downtime.
- Censorship Resistance – Data isn’t controlled by a single entity, making it harder for governments or organizations to
  censor or remove information.
- Improved Availability – Multiple copies of data are stored across a network, ensuring accessibility even if some nodes
  go offline.
- Lower Costs – Users can share and utilize spare storage from others, reducing infrastructure costs compared to
  traditional cloud providers.
- Tamper Resistance – Cryptographic hashing and content addressing ensure data integrity and prevent unauthorized
  modifications.
- Trustless Environment – No need to rely on a central authority; cryptographic verification ensures data is stored and
  retrieved correctly.

## Decentralised Storage in DoaToa

For DoaToa, a peer-to-peer approach aligns best with its principles. The InterPlanetary File System (IPFS) is a
Decentralised, peer-to-peer protocol for storing and sharing data. Unlike traditional location-based addressing (where
URLs point to specific servers), IPFS uses content addressing, identifying files by their cryptographic hash (CID).

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

However, IPFS alone does not guarantee permanent storage—data can disappear if no node continues to host it. To ensure
persistence, data must be pinned. This can be done either manually (free) or through a pinning service. Currently,
DoaToa uses a pinning service. The ultimate way to do it, would be to support self-hosting and pinning from the wallet
itself, ensuring
full control over data storage. However, since the wallet resides on a phone, a phone is managed by their OS and most
OS-es are quite strict about persistent connections (and rightly so), this setup is not feasible.

## Key Features

- **Content-addressed:** Files are identified by their **cryptographic hash**, not by location (like a URL).
- **Distributed storage:** Files are shared and stored across a network of peers.
- **Versioned:** Supports version control and immutable data structures (like Git).
- **Offline-friendly:** Data can be accessed locally without needing a central server.

## How It Works

1. A file is added to IPFS and broken into chunks.
2. Each chunk is hashed and stored in a **Merkle DAG**.
3. The root hash (CID - Content Identifier) acts as the address for the entire file.
4. Anyone with the CID can fetch the file from any peer who has it.

## Common Use Cases

- Decentralised websites (often with IPNS or ENS)
- Content archiving and permanence
- Peer-to-peer file sharing
- Blockchain data storage (e.g., NFTs)

IPFS is often used with pinning services or local nodes to ensure data availability.