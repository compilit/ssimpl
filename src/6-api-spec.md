# SSIMPL API Specification

This section defines the reference API for Relay Peers in the SSIMPL protocol. It provides endpoints for **DID
registration**, **revocation**, and **ledger synchronization**.

All requests **MUST** be authenticated using a `Bearer` token, containing a **signed DID in the canonical
SignatureDocument format, multibase-encoded**.

---

## Endpoints

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
      "signatureDocument": {
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
  "signatureDocument": {
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
  "signatureDocument": {
    ...
  }
}
```

Response: 200 OK on success.

⸻

Shared Object Schemas

### LedgerEntry

| Field             | Type   | Description                                     |
|-------------------|--------|-------------------------------------------------|
| data              | object | Contains `verifiableCredentialRoot` and `proof` |
| signatureDocument | object | Signature verifying the integrity of `data`     |

---

### LedgerEntryRevocation

| Field             | Type   | Description                        |
|-------------------|--------|------------------------------------|
| data              | object | Contains the DID being revoked     |
| signatureDocument | object | Signature verifying the revocation |

---

### VerifiableCredentialRoot

| Field             | Type   | Description                               |
|-------------------|--------|-------------------------------------------|
| data              | object | Contains `did` and `identityDocumentHash` |
| signatureDocument | object | Signature verifying the `data`            |

---

### DataGroupProof

| Field       | Type   | Description                     |
|-------------|--------|---------------------------------|
| DG15        | string | Data group 15 proof             |
| AASignature | string | Active Authentication signature |
| AAChallenge | string | Active Authentication challenge |
| SOD         | string | Security Object Document        |

---

### SignatureDocument

| Field     | Type   | Description                                                         |
|-----------|--------|---------------------------------------------------------------------|
| message   | string | Canonical serialized representation of the signed data              |
| signature | object | `Signature` object containing `algorithm`, `publicKey`, and `value` |
| signer    | string | DID or public key identifier of the signer                          |

---

### Signature

| Field     | Type   | Description                       |
|-----------|--------|-----------------------------------|
| publicKey | string | Public key of signer              |
| algorithm | string | Signing algorithm                 |
| value     | string | Multibase-encoded signature value |