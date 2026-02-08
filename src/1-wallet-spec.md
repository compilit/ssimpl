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

### Rules and Notes

* Optional fields in the data object MUST NOT affect the canonical hash unless explicitly defined in the
  canonicalization rules.
* Implementations MUST reject signatures if the hash does not match the canonicalized data object.
* Adherence to this pattern is mandatory to ensure correct ledger validation, delta verification, and revocation
  processing.

This canonical signature object pattern provides a single, consistent approach for proving authorship and integrity
across all SSIMPL objects.

## Wallet Functionality

In order to function properly, it is essential certain behavior is implemented:

* Before any transaction, the Wallet MUST call either the Root Relay Peer, or one of it's known Relay Peers for the
  latest delta.
* If this Peer is new, it must first be bootstrapped. Which means acquiring the full current Ledger. This is done by
  requesting delta's in chunks between epochs, starting with 0 (Unix Time). Until the Ledger is fully in place, the
  Wallet MUST NOT allow any transactions.
* Before any transaction, the Wallet MUST call either the Root Relay Peer, or one of it's known Relay Peers for the
  latest list of known Relay Peers.


