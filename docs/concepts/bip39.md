# BIP39

BIP39 (Bitcoin Improvement Proposal 39) defines a way to represent a deterministic wallet’s private key using a
human-readable set of words, called a mnemonic phrase.

How it works:

- A cryptographically strong random number (entropy) is generated.
- That number is converted into a sequence of 12–24 words chosen from a predefined list of 2048 words.
- This phrase can be used to derive a seed, which in turn can generate a hierarchy of keys (e.g., via BIP32).

Key properties:

- Mnemonic = backup: If you lose your device, you can recover your entire wallet using the phrase.
- Language-agnostic: Wordlists exist for multiple languages.
- Deterministic: The same phrase will always regenerate the same wallet.