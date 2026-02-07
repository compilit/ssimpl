# Relay Peer Specification

The Relay Peer is a special kind of Peer in the sense that it is static and reachable over the internet on a fixed
hostname. It acts like any other Peer, but it also adds a storage layer for the Ledger and serves as distribution point
for the Ledger.

## Relay Peer Discovery and Verification

To enable robust peer-to-peer synchronization while maintaining decentralization, SSIMPL defines a **Relay Peer
Discovery and Verification Protocol**. This allows new Peers to bootstrap securely and verify that Relay Peers are
implementing the protocol correctly.

---

### Deterministic Relay Hostnames

Relay Peers are discoverable via a **canonical hostname scheme**:

- Hostnames are deterministic, e.g., `0.ssimpl.org` … `9999.ssimpl.org`.
- Each Relay Peer implements a fixed set of endpoints defined by the SSIMPL specification.
- Peers attempt to connect sequentially or via pseudo-randomized probing across the deterministic range.

> **Rationale:** Deterministic hostnames avoid the need for a centralized list, while still enabling Peers to discover
> Relay Peers without prior trust assumptions.

---

[//]: # (### Required Endpoints)

[//]: # ()
[//]: # (Relay Peers **MUST** implement the following endpoints:)

[//]: # ()
[//]: # (| Endpoint        | Method | Description                                                                                        |)

[//]: # (|-----------------|--------|----------------------------------------------------------------------------------------------------|)

[//]: # (| `/ledger-meta`  | GET    | Returns current epoch, root_hash, and relay_id metadata.                                           |)

[//]: # (| `/ledger-delta` | POST   | Accepts a peer’s last known epoch and root_hash, returns missing ledger entries and Merkle proofs. |)

[//]: # (| `/check-peer`   | GET    | Challenge-response endpoint to verify relay ownership of DID key.                                  |)

[//]: # ()
[//]: # (Responses **MUST** include cryptographic proofs allowing Peers to verify data integrity without trusting the Relay Peer)

[//]: # (blindly.)

### Relay Peer Verification Protocol

1. **Connection:** Peer attempts a connection to a candidate Relay Peer.
2. **Metadata Exchange:** Relay Peer returns:
    - `last_epoch` (latest ledger epoch)
    - `root_hash` (canonical ledger root)
    - Timestamp
    - `signature_document` - Signature over the metadata using the Relay Peer’s DID key
3. **Validation:** Peer verifies:
    - `root_hash` corresponds to canonical ledger rules
    - Timestamp is within an acceptable window
    - Signature is valid and matches the DID key
4. **Challenge-Response (Optional):**
    - Peer sends a nonce to the Relay Peer
    - Relay Peer signs the nonce and returns the signature
    - Peer validates the signature to ensure possession of the claimed DID key
5. **Trust Establishment:** Peer marks Relay Peer as verified if all checks pass; otherwise, the peer discards the
   candidate.

---

### Handling Non-Existing or Offline Relay Peers

- Peers **iterate** through deterministic hostnames.
- Temporarily unreachable hosts are retried after a backoff period.
- Optionally, a **seed CID on IPFS** may provide an initial snapshot of active Relay Peers to bootstrap discovery.

---

### Security and Trust Implications

- Deterministic discovery combined with cryptographic verification ensures that peers **cannot be misled** by malicious
  Relay Peers.
- Relay Peers are **auditable**: any failure to implement endpoints correctly or return invalid data can be detected.
- Peers remain authoritative for ledger validation; Relay Peers are **relays, not arbiters**.

---

**Summary:**  
This protocol allows new Peers to bootstrap without centralized coordination, ensures Relay Peers can be trusted, and
maintains the integrity and decentralization of the SSIMPL ledger.
