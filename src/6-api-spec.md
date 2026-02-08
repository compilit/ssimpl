# SSIMPL API Specification

This section defines the reference API for Relay Peers in the SSIMPL protocol. It provides endpoints for **DID
registration**, **revocation**, and **ledger synchronization**.

All requests **MUST** be authenticated using a `Bearer` token, containing a **signed DID in the canonical
SignatureEnvelope format, multibase-encoded**.



## Endpoints

### Perform a handshake

**POST** `/identity`

**Purpose:** Allows (Relay) Peers to check the health an initial validity of the Relay Peer

**Request payload (`LedgerDeltaRequest`):**

| Field             | Type    | Description                                         |
|-------------------|---------|-----------------------------------------------------|
| data              | integer | Last ledger epoch known to the peer                 |
| signatureEnvleope | string  | Merkle root hash of the peer's current ledger state |

### Request Ledger Delta

**POST** `/ledger/delta`

**Purpose:** Allows a Peer to request ledger entries that are newer than a given epoch, if the peer is out of sync.

**Request payload (`LedgerDeltaRequest`):**

| Field     | Type    | Description                                         |
|-----------|---------|-----------------------------------------------------|
| lastEpoch | integer | Last ledger epoch known to the peer                 |
| rootHash  | string  | Merkle root hash of the peer's current ledger state |

**Example:**

```json
{
  "lastEpoch": 42,
  "rootHash": "abcdef1234567890"
}
```

Response payload (LedgerDeltaResponse):

Field Type Description
currentEpoch integer Current epoch of the relay peer ledger
authoritativeRootHash string Merkle root hash after applying returned entries array Ordered ledger entries newer than lastEpoch

Example:

```json
{
  "currentEpoch": 43,
  "authoritativeRootHash": "1234abcd5678ef90",
  "entries": [
    {
      "data": {
        "verifiableCredentialRoot": {
          ...
        },
        "proof": {
          ...
        }
      },
      "signatureEnvelope": {
        ...
      }
    }
  ]
}
```

⸻

Register a DID

**POST** `/ledger`

Purpose: Register a new DID on the ledger.

Request payload: LedgerEntry object

Example:

```json
{
  "data": {
    "verifiableCredentialRoot": {
      ...
    },
    "proof": {
      ...
    }
  },
  "signatureEnvelope": {
    ...
  }
}
```

Response: 200 OK on success.

⸻

Revoke a DID

**PUT** `/ledger`

Purpose: Revoke a DID by submitting a revocation payload.

Request payload: LedgerEntryRevocation object

Example:

```json
{
  "data": {
    "did": "did:key:z6Mkw...example"
  },
  "signatureEnvelope": {
    ...
  }
}
```

Response: 200 OK on success.

⸻

Shared Object Schemas

### SignedObject (Signed<T>)

| Field             | Type   | Description                                       |
|-------------------|--------|---------------------------------------------------|
| data              | object | Contains the actual fields used for the signature |
| signatureEnvelope | object | Signature verifying the `data`                    |



### SignedObject: LedgerEntry

| Field             | Type   | Description                                     |
|-------------------|--------|-------------------------------------------------|
| data              | object | Contains `verifiableCredentialRoot` and `proof` |
| signatureEnvelope | object | Signature verifying the integrity of `data`     |



### VerifiableCredentialRoot

| Field                | Type   | Description                               |
|----------------------|--------|-------------------------------------------|
| did                  | object | Contains `did` and `identityDocumentHash` |
| identityDocumentHash | object | Signature verifying the `data`            |



### SignedObject: LedgerEntryRevocation

| Field             | Type   | Description                        |
|-------------------|--------|------------------------------------|
| data              | object | Contains the DID being revoked     |
| signatureEnvelope | object | Signature verifying the revocation |



### Proof

| Field       | Type   | Description                     |
|-------------|--------|---------------------------------|
| DG15        | string | Data group 15 proof             |
| AASignature | string | Active Authentication signature |
| AAChallenge | string | Active Authentication challenge |
| SOD         | string | Security Object Document        |



### SignatureEnvelope

| Field     | Type   | Description                                                         |
|-----------|--------|---------------------------------------------------------------------|
| message   | string | Canonical serialized representation of the signed data              |
| signature | object | `Signature` object containing `algorithm`, `publicKey`, and `value` |
| signer    | string | DID or public key identifier of the signer                          |



### Signature

| Field     | Type   | Description                       |
|-----------|--------|-----------------------------------|
| publicKey | string | Public key of signer              |
| algorithm | string | Signing algorithm                 |
| value     | string | Multibase-encoded signature value |