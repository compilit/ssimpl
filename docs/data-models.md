# Data Models

## Root Claim

This data model defines the basic ingredients to be able to proof that a certain wallet is associated with a certain
passport.

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "type": "object",
  "properties": {
    "data": {
      "type": "object",
      "properties": {
        "rootClaim": {
          "type": "object",
          "properties": {
            "did": {
              "type": "string"
            },
            "identityDocumentHash": {
              "type": "string"
            }
          },
          "required": [
            "did",
            "identityDocumentHash"
          ]
        },
        "proof": {
          "type": "object",
          "properties": {
            "DG15": {
              "type": "string"
            },
            "AASignature": {
              "type": "string"
            },
            "AAChallenge": {
              "type": "string"
            },
            "SOD": {
              "type": "string"
            }
          },
          "required": [
            "DG15",
            "AASignature",
            "AAChallenge",
            "SOD"
          ]
        },
        "extraClaims": {
          "type": "object"
        }
      },
      "required": [
        "rootClaim",
        "proof",
        "extraClaims"
      ]
    },
    "signatureDocument": {
      "type": "object",
      "properties": {
        "message": {
          "type": "string"
        },
        "signature": {
          "type": "object",
          "properties": {
            "publicKey": {
              "type": "string"
            },
            "algorithm": {
              "type": "string"
            },
            "value": {
              "type": "string"
            }
          },
          "required": [
            "publicKey",
            "algorithm",
            "value"
          ]
        },
        "signer": {
          "type": "string"
        }
      },
      "required": [
        "message",
        "signature",
        "signer"
      ]
    }
  },
  "required": [
    "data",
    "signatureDocument"
  ]
}
```

## Subscription request payload

With this payload, any entity can request a subscription on one or more scopes (one or more related claims) of someone's identity.
```json

{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "type": "object",
  "properties": {
    "audience": {
      "type": "string"
    },
    "scopes": {
      "type": "array",
      "items": [
        {
          "type": "string"
        },
        {
          "type": "string"
        }
      ]
    },
    "destination": {
      "type": "string"
    }
  },
  "required": [
    "audience",
    "scopes",
    "destination"
  ]
}

```

## Signed wrapper

This model is used to associate some data to your DID by signing it, and then letting that result be signed by a notary
server.

```json

{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "type": "object",
  "properties": {
    "data": {
      "type": "object",
      "properties": {
        "data": {
          "type": "string"
        },
        "signature": {
          "type": "object",
          "properties": {
            "publicKey": {
              "type": "string"
            },
            "algorithm": {
              "type": "string"
            },
            "value": {
              "type": "string"
            }
          },
          "required": [
            "publicKey",
            "algorithm",
            "value"
          ]
        },
        "signer": {
          "type": "string"
        }
      },
      "required": [
        "data",
        "signature",
        "signer"
      ]
    },
    "signature": {
      "type": "object",
      "properties": {
        "publicKey": {
          "type": "string"
        },
        "algorithm": {
          "type": "string"
        },
        "value": {
          "type": "string"
        }
      },
      "required": [
        "publicKey",
        "algorithm",
        "value"
      ]
    },
    "signer": {
      "type": "string"
    }
  },
  "required": [
    "data",
    "signature",
    "signer"
  ]
}


```