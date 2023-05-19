# CANDID DID Method Specification


## DID Method Name
The name string that shall identify this DID method is:  `candid`

A DID that uses this method MUST begin with the following prefix: `did:candid`. Per the DID specification, this string MUST be in lowercase. The remainder of the DID, after the prefix, is specified below.

## Method Specific Identifier
```
candid-did          := did:candid:<specific-identifier>
specific-identifier := [ <network> ":" ] <candid-id>
network             := <lowercase-char>{1,10}, "ex. ethereum | goerli | polygon"
candid-id           := base58(sha256(uuid())) "The uuid uses version 4"
lowercase-char      := [0-9 a-z]
sha256              := [0-9 a-f] 64*HEXDIG
base58              := [^0OIl+\/]
```
The method-specific identifier is a unique identifier created using a specific method. First, a function generates a random 128-bit number. Then, this number is encrypted to obtain a 256-bit hash value. Finally, this hash value is converted to a base58 format to obtain the final `candid-id`.
This generation process ensures the uniqueness and security of the `candid-id`.

## CRUD Operation Definitions
### Create (Register)
After a `candid` DID is established, its information, including the DID Document, is recorded on the blockchain.
The default DID document for an `did:candid:<candid-specific-identifier>` on Quorum mainnet, e.g. `did:candid:7NH4xQGbTHViMcc9BbyJRP6Lx28aTRrMHueWm5ffcBrk` looks like this:

DID subject `did:candid:7NH4xQGbTHViMcc9BbyJRP6Lx28aTRrMHueWm5ffcBrk`
```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://w3id.org/security/suites/ed25519-2020/v1"
  ],
  "id": "did:candid:7NH4...cBrk",
  "owner": "0x6789cf7752c4c966482927d8D5d222190a18F1bb",
  "controller": [ "did:candid:7NH4...cBrk" ],
  "verificationMethod": [
    {
      "id": "did:candid:7NH4...cBrk#key-1",
      "type": "Ed25519VerificationKey2020",
      "controller": "did:candid:7NH4...cBrk",
      "publicKeyMultibase": "9ecc7ce73cafc85ee1ebc6e5e7a5cc0e1078e753a77c14fbd3b5fa07b47920fb"
    }
  ],
  "authentication": [ "did:candid:7NH4...cBrk#key-1" ],
  "assertionMethod": [ "did:candid:7NH4...cBrk#key-1" ],
  "service": [ 
    {
     "id": "did:candid:7NH4...cBrk#InsuranceService",
      "type": "CredentialRegistry",
      "serviceEndpoint": "https://web3wallet.cci.cathaybc.com/"
    },
    {
      "id": "did:candid:7NH4...cBrk#CarService",
      "type": "CredentialRegistry",
      "serviceEndpoint": "https://web3wallet.tesla.cathaybc.com/"
    }
  ],
  "proof": {
    "type": "Ed25519Signature2020",
    "creator": "did:candid:7NH4...cBrk#key-1",
    "signatureValue": "..."
  }
}
```

### Read (Resolve)
A DID resolver **MUST** be able to take a `did:candid:<network>:<id>` string as an input and generate a JSON object providing all information necessary to authenticate and validate the given DID. The DID document is dynamically constructed from the data stored on the DID Register.
The Candid-Resolver-SDK is an open-source program that allows you to set up your own resolver service. Alternatively, you can utilize the service hosted by our team and perform resolve or dereference actions by making API calls.

### Update
Changes to an identity contract are made through executing transactions directly on the `DIDRegistry` contract, which is only be doable by the controller address.

### Delete
Only the controller address is able to delete an existing DID.

## References
[1] [**W3C DID Core**](https://www.w3.org/TR/did-core/)
