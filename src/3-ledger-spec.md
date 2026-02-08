# Ledger Specification

The SSIMPL ledger is a **deterministically canonical, peer-replicated structure** tracking valid DIDs, maintained across
Peers and Relay Peers.

## Goals

* Track valid DIDs without a central authority.
* Support bounded state and deterministic pruning.
* Enable single-peer verification.
* Eventual consistency via optional gossip or client/server synchronization.
* Mobile-friendly operation for large ledgers (5–30 GB).

## State and Canonical Construction

1. Filter expired entries based on `did_expiry` and `GRACE_PERIOD`.
2. Normalize entries into canonical byte format.
3. Sort lexicographically by `did_expiry` then by `hash(did)`.
4. Compute `root_hash` using a **Merkle tree** over the current, complete, pruned ledger.

## Merkle Tree

The SSIMPL ledger uses a **Merkle tree** to enable efficient verification of ledger state and individual entries.

### Structure

- **Leaves:** Hashes of canonical ledger entries.
- **Branches:** Hashes of concatenated child nodes.
- **Root:** `root_hash` representing the current canonical ledger state.

### Updates

1. New ledger entries are appended as leaves.
2. The affected branches are rehashed up to the root.
3. Pruning does not delete nodes; the canonical ledger for the current epoch defines which entries are considered valid.

### Merkle Proofs

- To prove inclusion of a DID `xyz`, a peer can provide the leaf hash and the branch hashes up to `root_hash`.
- Other peers can recompute the root from the proof and verify it matches the canonical `root_hash`.

### Relation to Checkpoints

- Checkpoints store a `root_hash` for a specific epoch.
- Peers can verify their ledger or deltas by starting from the latest checkpoint and applying updates.

## Validation Rules

An entry is valid if:

1. `current_time` < `did_expiry` + `GRACE_PERIOD`.
2. `hash(verifiable_credential_root.data)` equals `verifiable_credential_root.signature_document.message`.
3. The public key in the DID matches `signature_document.signature.public_key`.
4. Signature verification succeeds:
   `
   verify_signature(
     verifiable_credential_root.signature_document.message,
     verifiable_credential_root.signature_document.signature.value,
     verifiable_credential_root.signature_document.signature.public_key,
     verifiable_credential_root.signature_document.signature.algorithm
   )
   `

Expired or invalid entries are ignored and removed during canonical construction.

## Pruning and Mutability

* Peers **MAY** remove their own entries and expired entries. To revoke a DID:

  1. Store a revocation payload on the local Ledger.
  2. Store and submit a revocation payload to (Relay) Peers.

* Peers **MUST NOT** remove valid entries or modify others' entries.
* Deterministic pruning ensures consistent ledger state across peers.

## Synchronization

* Peers exchange `{ epoch, root_hash }` metadata.
* **Matching hash:** No action required.
* **Hash mismatch:** Request missing entries or snapshots.
* Iterative delta exchange ensures eventual convergence.
* Relay Peers act as distribution points while also validating entries.
* Stateless Relay Peers may bootstrap from peers but should persist data for efficiency.

## Merkle Checkpoints

To enable efficient verification of ledger history and prevent forks from propagating, the SSIMPL protocol introduces
Merkle checkpoints. These checkpoints allow peers to validate that their current ledger state stems from a previously
agreed-upon canonical state.

### Definition

A Merkle checkpoint is a snapshot of the canonical ledger at a given epoch, containing:

* epoch — The deterministic epoch number of the snapshot
* root_hash — The canonical ledger Merkle root at that epoch
* metadata (optional) — Any additional information, such as total entry count, checkpoint creator DID, or a reference
  timestamp

### Purpose

Merkle checkpoints serve three main purposes:

1. Anchoring history — They provide a trusted reference point for peers to verify that their current ledger is an
   extension of a previous canonical state.
2. Fork detection — Peers can detect divergence by comparing their checkpointed root hash with peers’ reported
   checkpoints.
3. Efficient synchronization — When a peer joins the network or recovers from offline mode, it can request deltas
   starting from the latest known checkpoint rather than from the genesis ledger.

### Creation and Storage

* Peers MAY create checkpoints at deterministic intervals (e.g., end of each epoch).
* Relay Peers SHOULD store checkpoints for availability, enabling efficient delta requests for mobile peers.
* Checkpoints themselves are immutable; once created, they cannot be modified or deleted.

### Verification

To verify a ledger using a checkpoint:

1. Obtain the checkpoint for the target epoch (epoch, root_hash).
2. Compute the canonical ledger state from the checkpoint epoch to the current state.
3. Recompute the Merkle root for the resulting ledger.
4. Confirm that the recomputed root matches the current root_hash.

A mismatch indicates ledger tampering or divergence, and the peer MUST reject the inconsistent entries.

### Pruning and Expiry

* Checkpoints older than a configurable retention window MAY be pruned to reduce storage overhead.
* Retention policies SHOULD be deterministic and consistent across peers to maintain canonical verification paths.

### Security Considerations

* Checkpoint integrity — Checkpoints must be signed by the creating peer using their DID key.
* Fork prevention — Peers must reject any checkpoint that does not extend a known valid previous checkpoint.
* Replay attacks — Timestamps and epoch numbers help prevent replay of stale checkpoints.

## Security Properties

* Single-peer revocation verification.
* Privacy-preserving registration.
* Resistance to illegal pruning and tampering.
* Bounded state growth.
* Eventual convergence under client/server synchronization.

