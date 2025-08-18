# BIP32

BIP32 (Bitcoin Improvement Proposal 32) defines Hierarchical Deterministic (HD) wallets, which allow you to derive a
tree of key pairs from a single root seed.

How it works:

- Starts with a single seed (often from a BIP39 mnemonic).
- From that seed, it deterministically derives a master keypair (private + public key).
- You can derive child key pairs from the master, and children of those, forming a tree structure.

Key features:

- One backup = all keys: Backing up the seed gives access to all derived keys.
- Key separation: You can derive independent identities (child keys) without compromising the master.
- Public derivation: Given a parent public key, you can derive child public keys (but not private keys).
- Hardened derivation: A safer variant where child keys can’t be derived from public keys, but requires the parent
  private key.