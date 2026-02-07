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

---