# Ledger Specification

The SSIMPL ledger is a **deterministically canonical, peer-replicated structure** tracking valid DIDs, maintained across
Peers and Relay Peers.

## Goals

* Track valid DIDs without a central authority.
* Support bounded state and deterministic pruning.
* Enable single-peer verification.
* Eventual consistency via optional gossip or client/server synchronization.
* Mobile-friendly operation for large ledgers (5–30 GB).

## Entries

Each **DID Registration Entry** contains:

* `data` – Core credential and cryptographic proofs.
* `signature_document` – Signature verifying the integrity of `data`.

`data` object contains:

* `verifiable_credential_root` `{ did ,did_expiry, ta_hash }`.
* `proof` `{ DG15, AASignature, AAChallenge, SOD }`.

`signature_document` contains:

* `message` – Hashed `data`.
* `signature` – `{ public_key, algorithm, value }`.
* `signer` – DID of signing entity.

## State and Canonical Construction

1. Filter expired entries based on `did_expiry` and `GRACE_PERIOD`.
2. Normalize entries into canonical byte format.
3. Sort lexicographically by `did_expiry` then by `hash(did)`.
4. Compute `root_hash` using a **Merkle tree** over the current, complete, pruned ledger.

## Validation Rules

An entry is valid if:

1. `current_time < did_expiry + GRACE_PERIOD`.
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

1. Remove the DID from the local ledger.
2. Submit a revocation payload to Relay Peers, containing:

* `data` – `{ did, effective_epoch, previous_root_hash, new_root_hash, signature_by_did_key }`.
* `signature_document` – Signature verifying `data`.

* Peers **MUST NOT** remove valid entries or modify others' entries.
* Deterministic pruning ensures consistent ledger state across peers.

## Synchronization

* Peers exchange `{ epoch, root_hash }` metadata.
* **Matching hash:** No action required.
* **Hash mismatch:** Request missing entries or snapshots.
* Iterative delta exchange ensures eventual convergence.
* Relay Peers act as distribution points while also validating entries.
* Stateless Relay Peers may bootstrap from peers but should persist data for efficiency.

## Security Properties

* Single-peer revocation verification.
* Privacy-preserving registration.
* Resistance to illegal pruning and tampering.
* Bounded state growth.
* Eventual convergence under client/server synchronization.

---