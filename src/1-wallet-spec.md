# Wallet Specification

The wallet is a Self-Sovereign means of identification that can be installed and bootstrapped entirely independent of
any authority. It solely relies on cryptographic proof.

## Wallet Requirements

Wallets **MUST**:

* Perform NFC scans of Trust Anchors.
* Support Active Authentication challenge verification.
* Derive BIP32 keypairs from a BIP39 mnemonic.
* Store the master BIP32 keypair in a secure storage section of their device (e.g. Apple Keychain, Android Keystore, due
  to lack of true HSMs in mobile devices).
* Sign and manage the Verifiable Credential Root.
* Generate DIDs compliant with `did:key` for interoperability.
* Support JWT issuance and asymmetric key exchange.

Wallets **SHOULD**:

* Encrypt credentials for local storage.
* Store additional credentials such as DG11 for user convenience.
* Send their signed VCR to Relay Peers.

## Activation Process

1. User installs wallet.
2. Wallet prompts for MRZ line.
3. NFC scan and extraction of passport data.
4. Creation of Verifiable Credential Root (VCR).
5. BIP39 mnemonic generation.
6. BIP32 keypair derivation and storage.
7. Signing of the VCR.
8. Submission of the VCR to a Relay Peer.

## Canonical Signature Object Pattern

All cryptographically signed objects in SSIMPL **MUST** follow a uniform, canonical structure, known as the *
*SignatureDocument**. This ensures consistency across all signed data, including ledger entries, Verifiable Credential
Roots (VCR), revocations, and delta payloads.

### Structure

Each signed object **MUST** include a `signatureDocument` with the following structure:

- **`message`** — The cryptographic hash of the canonical serialization of the `data` object. See §Canonicalization
  Rules.
- **`signature`** — Object containing the signature itself:
    - `publicKey` — Base64url-encoded public key corresponding to the signing private key.
    - `algorithm` — Signature algorithm used (e.g., EdDSA, ECDSA).
    - `value` — Base64url-encoded signature value.
- **`signer`** — DID of the entity producing the signature.

Example (in JSON for clarity):

```json
{
  "message": "<hash-of-canonical-data>",
  "signature": {
    "publicKey": "<multibase-encoded public key>",
    "algorithm": "<algorithm identifier>",
    "value": "<multibase-encoded signature>"
  },
  "signer": "<DID of signer>"
}
```

### Usage

* Every signed object in SSIMPL, including LedgerEntry, VerifiableCredentialRoot, revocation entries, and delta updates,
  MUST include a signatureDocument.
* The message field MUST be derived from the canonical serialization of the associated data object, ensuring that
  identical logical objects produce identical hashes.
* This pattern guarantees interoperability, verifiability, and deterministic ordering for all signed objects across the
  network.

### Rules and Notes

* Optional fields in the data object MUST NOT affect the canonical hash unless explicitly defined in the
  canonicalization rules.
* Implementations MUST reject signatures if the hash does not match the canonicalized data object.
* Adherence to this pattern is mandatory to ensure correct ledger validation, delta verification, and revocation
  processing.

This canonical signature object pattern provides a single, consistent approach for proving authorship and integrity
across all SSIMPL objects.

---