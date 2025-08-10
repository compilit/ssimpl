# Cryptographic Standards Enabling European e-Passport Integrity

A European e-passport relies on a set of international standards to prove its authenticity and integrity using cryptographic techniques. These standards work together to ensure the data on the passport is both verifiable and resistant to tampering or cloning.

## ICAO Doc 9303

The foundational specification for Machine Readable Travel Documents (MRTDs). It defines:

- The structure and contents of the e-passport chip.
- Cryptographic protocols such as:
    - **Passive Authentication (PA):** Verifies the integrity and authenticity of the stored data using digital signatures from the issuing authority.
    - **Active Authentication (AA):** Ensures the chip hasn’t been cloned by using a private key challenge/response protocol.

## ISO/IEC 7816 (Parts 4, 8, 9)

Standards for electronic identification cards with contacts, adapted for contactless chips in e-passports:

- **Part 4:** File system structure and command set (APDUs).
- **Part 8:** Security-related functions including authentication.
- **Part 9:** Access control and secure messaging.

These standards allow secure and standardized access to the data groups (DG1–DG15) on the chip.

## ISO/IEC 14443 (Parts 1–4)

Defines the physical and communication characteristics for contactless smartcards:

- Enables NFC communication between passport chips and readers.
- Standard across all ICAO-compliant e-passports.

## ISO/IEC 7501-1

Specifies the physical format and layout of machine-readable passports, including:

- Dimensions
- Data placement
- The Machine Readable Zone (MRZ), which is used to derive keys for secure access (e.g., Basic Access Control).

## CSCA and DSC Certificates

Each country operates a **Country Signing Certificate Authority (CSCA)** and issues **Document Signer Certificates (DSCs)**:

- The CSCA’s public key is distributed globally.
- The DSC signs the data on each passport.
- Verifiers use the CSCA chain to validate the DSC signature and ensure the data’s authenticity.

---

Together, these standards form the cryptographic backbone that allows e-passports to be trusted across borders.