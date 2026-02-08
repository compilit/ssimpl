## Canonicalization Rules

To ensure that signatures are verifiable and consistent across all peers, every signed object in SSIMPL (including
LedgerEntry and VerifiableCredentialRoot) MUST be serialized in a canonical form before hashing and signing. These rules
remove ambiguity in encoding and field ordering, preventing mismatches between peers.

## Object Serialization

1. Field Ordering
    * All objects MUST have their fields sorted lexicographically by key name.
    * Example: For {"b":2,"a":1}, canonical form is {"a":1,"b":2}.
2. Whitespace
    * No unnecessary whitespace is allowed.
    * Only minimal separators required by the serialization format are permitted.
3. Encoding
    * All strings MUST be UTF-8 encoded.
    * No BOM (Byte Order Mark) or non-standard characters are allowed.
4. Number Representation
    * Numbers MUST be represented without leading zeros (except zero itself) and without exponential notation.
    * Example: 1.0 → 1.0; 01 → invalid.
5. Boolean and Null

* true, false, and null MUST use JSON literals exactly as shown (case-sensitive).

## Nested Objects

* Recursion: Apply canonicalization rules recursively to all nested objects and arrays.
* Arrays MUST preserve element order.
* Elements MUST themselves be canonicalized if they are objects or arrays.

## Signature Message

1. The message field in every SignatureEnvelope MUST be the hash of the canonical serialized data object.
2. Recommended hash function: SHA-256.
3. The hash input is strictly the canonical byte sequence of the data object (no additional metadata).
4. Signing the message binds the signer cryptographically to the exact canonical representation of the object.

## Deterministic Encoding

* Multibase or Base64url encoding MUST be used consistently for any binary data (e.g., cryptographic proofs).
* Multibase-encoding SHOULD be used consistently for any binary data (e.g., cryptographic proofs).
* This ensures that all peers can reproduce the exact same byte sequence for verification.

## Verification Procedure

To verify a signature:

1. Peer canonicalizes the data object according to the rules above.
2. Peer hashes the canonicalized bytes to compute the message.
3. Peer verifies that the computed message matches the message in signatureEnvelope.
4. Peer validates the signature using the provided publicKey and algorithm.
5. Only if all checks pass is the object considered valid.

## Rationale

Canonicalization guarantees that:

* Signatures are deterministic and portable across implementations.
* Peers can independently verify credentials, ledger entries, and revocations.
* Forks, replay attacks, and accidental divergence due to encoding differences are prevented.

