# Overview

The SSIMPL protocol defines a **Self-Sovereign Identity & Mondial Pseudonymous Ledger** framework that allows
individuals to maintain control over their digital identity while enabling verifiable credential issuance and
revocation. It bridges physical trust anchors (e.g., ePassports) with digital identity systems, ensuring cryptographic
verification without reliance on centralized authorities.

SSIMPL addresses two key problems:

1. **User control over digital identity** – eliminating reliance on centralized identity providers.
2. **Verification of identities** – ensuring online credentials correspond to real individuals using cryptographic
   proofs.

The protocol balances decentralization, peer authority, and optional server-assisted synchronization for efficiency
and availability. Everything is made to be deterministic by default, so no authorities whatsoever are required except
the one that signed the Trust Anchor.

## Identity Trust Model

SSIMPL identities are rooted in cryptographically verifiable credentials derived from **Trust Anchors (TA)**, such as
government-issued ePassports. A valid wallet:

* Scans a Trust Anchor (TA) via NFC.
* Performs an Active Authentication (AA) challenge.
* Derives a **Verifiable Credential Root (VCR)** tied to the user’s DID.
* Publishes this Verifiable Credential Root to Relay Peers.

This ensures only owners of verified physical credentials can create valid DIDs.

### Considerations

Identity is hard to verify by nature. Using a state-issued TA is not a perfect solution, but it's the best
deterministic proof of identity available at the moment.

## Levels of Authentication

* **Level 0:** DID confirms human ownership (bot detection).
* **Level 1:** Level 0 + unverified attributes requested via scope.
* **Level 2:** Level 1 + verified credentials requested via scope.

## Roles

* **Identity** The owner of the TA.
* **Wallet:** The actual software responsible for encapsulating the owners TA.
* **Peers:** Mobile devices with the Wallet installed.
* **Relay Peers:** Servers that act both as Peers and distribution points, allowing other Peers to sync their ledger.

