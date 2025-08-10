# Self-Sovereign-Identity

Self-Sovereign Identity (SSI) is a decentralized identity model where individuals control their own digital identities
without relying on central authorities.

The main issue which the concept SSI usually tends to ignore is 'truth'. As long an individual is in complete control of
their own digital identity, who is to say this individual is not impersonating some other individual, or worse,
conjuring up a completely new non-existing one.

Cryptography can verify who issued a claim and whether it was tampered with, but it cannot verify the truthfulness of
the claim itself. The validity of claims like a name, nationality, or birthdate ultimately depends on who the issuer is
and whether they are trusted. However, since this requires authorities to be fully aligned with Web3 standards, we hit a
dead end. Currently, most (if not all) authorities don't have any way to issue DIDs. So what DoaToa does, is reversing
the flow yet again: an individual can create their own DID along with the related cryptographic keys. Which is actually
in line with the entire idea of decentralisation. But 'claims' an individual makes are divided into 2 categories: formal
and informal.

## Informal Claims

Informal claims can be made within certain scopes, where the user provides their own data along with their signature. By
nature, these claims cannot be verified by an authority. However, other identities can attest to a claim by signing it,
effectively endorsing its validity. This attestation carries moral responsibility, as the signer’s DID and signature
become publicly associated with the claim.

## Formal Claims

Formal claims cannot be self-issued; they must come from a recognized authority. At this point in time, the only formal
claims available to a DoaToa user, are the ones coming from their passport. These can be used to chain other claims of
course: by taking your full name and birthdate, you can match then with the names and birthdate written on an official
diploma. This functionality is out of scope for now.