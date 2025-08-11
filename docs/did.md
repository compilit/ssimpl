# DID

Decentralised IDentifier. A DID is a globally unique identifier that is not controlled by a central authority. Unlike
traditional identifiers (usernames, emails, etc.), a DID is linked to a cryptographic keypair, allowing users to prove
ownership without relying on a centralised system.

A DID follows this format:

    did:<method>:<identifier>

In DoaToa, primarily the 'key' method is used. This means that each 'identifier' part is actually the public part of a
specific cryptographic public/private keypair. Each user is responsible for their own private key, which is used to sign
data. The public key can always be used to verify the signature.

The 'key' method also means there is no real need for a DID document, which is part of the DID spec.