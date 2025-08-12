## Cryptography

Achieving true decentralization requires users to handle complex cryptographic operations. To make this accessible, all
cryptographic functionality should be encapsulated within a user-friendly interface — a ‘wallet’.

This wallet contains at least one cryptographic keypair:

1. A root AES key for encrypting and decrypting the user’s data.
2. An EC-based public/private key pair for signing and verifying data.

These algorithms are widely recognized standards, so I won’t delve into their inner workings—Wikipedia is your friend
for that.

With these keys, users can encrypt, decrypt, sign, and verify data entirely on the client side, ensuring full control
over their information.

In the subscription system, an additional key pair is introduced. This keypair facilitates sharing a derived
AES key—not the root AES key, but a deterministically generated AES key unique to each subscription.

## Post-Quantum disclaimer

With the inevitable advent of quantum computing, these algorithms will change eventually. Currently, there are several
candidates which could potentially take over the aforementioned algorithms. DoaToa will adapt accordingly.

The biggest concern lies in asymmetric encryption, which is used for signing and sharing of secrets. Through Shor's
algorithm, the asymmetric encryption algorithms we currently rely on will be cracked, meaning that private keys can
be derived based on public keys. This is why everything related to asymmetric encryption should have an expiration
time. And this is also part of the design of DoaToa: most data stays client-side.
