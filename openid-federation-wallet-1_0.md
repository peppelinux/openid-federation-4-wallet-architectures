%%%
title = "OpenID Federation for Wallet Architectures 1.0 - draft 05"
abbrev = "OpenID Federation for Wallet Architectures"
ipr = "none"
workgroup = "OpenID Connect Working Group"
keyword = ["security", "openid", "digital credentials", "issuer", "holder", "verifier"]

[seriesInfo]
name = "Internet-Draft"
value = "openid-federation-wallet-1_0"
status = "standard"

[[author]]
initials="G."
surname="De Marco"
fullname="Giuseppe De Marco"
organization="Dipartimento per la trasformazione digitale"
    [author.address]
    email = "gi.demarco@innovazione.gov.it"

[[author]]
initials="R."
surname="Hedberg"
fullname="Roland Hedberg"
organization="Catalogix"
    [author.address]
    email = "roland@catalogix.se"

[[author]]
initials="M.B."
surname="Jones"
fullname="Michael B. Jones"
organization="Self-Issued Consulting"
    [author.address]
    email = "michael_b_jones@hotmail.com"

[[author]]
initials="J."
surname="Bradley"
fullname="John Bradley"
organization="Yubico"
    [author.address]
    email = "ve7jtb@ve7jtb.com"

%%%

.# Abstract

This specification defines the OpenID Federation entity types for
digital wallet architectures.

{mainmatter}

# Introduction

As digital wallets become increasingly deployed for managing identity credentials,
establishing an architecture for trusted communication is required to allow each
participant in the ecosystem to evaluate other participants' compliance with mutual trust frameworks
and accomplish secure and trusted transactions.

This specification defines how to use OpenID Federation 1.0 [@!OpenID.Federation] to enhance the
security and interoperability of wallet ecosystems, facilitating trust establishment
among the parties and enabling secure metadata exchange and policy
application across large scale deployments.
It outlines the general architecture of a federated trust
infrastructure for wallet ecosystems, identifying participant roles and describing
the use of those roles.

Additionally, this specification provides practical examples of how to apply
policies for typical use cases within wallet ecosystems.
Finally, it offers guidance on defining trust marks for use within
wallet ecosystems.

## Requirements Notation and Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in
this document are to be interpreted as described in BCP 14 [@!RFC2119]
[@!RFC8174] when, and only when, they appear in all capitals, as shown here.

# Scope

This specification is a profile of [@!OpenID.Federation] for wallet ecosystems.
It defines entity types for entities participating in those ecosystems.
It describes trust evaluation mechanisms for those entities.
It uses applicable metadata parameters defined by other specifications
for wallet entities.

Collaboration Note: When a metadata parameter is needed for an Entity Type
defined by this specification that does not currently exist and
that would be usable by wallet ecosystems both using and not using OpenID Federation,
it is the editors' intent to work with the working groups creating
general-purpose wallet specifications to define those new parameters there.


# Terminology

This specification uses the terms
"End-User" and "Entity" as defined by OpenID Connect [@!OpenID.Core],
"JSON Web Token (JWT)" defined by JSON Web Token (JWT) [@!RFC7519],
"Client" as defined by [@!RFC6749],
"Verifiable Presentation" and "Wallet Attestation" defined in [@!OpenID4VP],
"Holder" and "Credential Issuer" defined in [@!OpenID4VCI],
and "Trust Mark", "Federation Entity", "Trust Anchor",
"Intermediate", and "Subordinate Statement" defined in [@!OpenID.Federation].

This specification also defines the following terms:

**Organizational Entity**:
: A Federation Entity represented by a legal entity, specifically referring to public or private organizations (excluding natural persons) recognized through a unique identifier. For the purposes of this specification, an Organizational Entity is also referred to as an Organization.

**Personal Device**:
: Any electronic device that is primarily used by an individual. This includes smartphones, tablets, laptops, personal computers, smart watches, and other wearable technologies. Personal Devices are owned and managed by End-Users as individuals, rather than by Organizations, or by End-Users on behalf of an Organization.

**Wallet Provider**:
: An Organizational Entity responsible for the development, publication, and management of a Wallet Solution.

**Wallet Instance**:
: Instance of a Wallet Solution belonging to and controlled by a person, be this natural or legal. It enables the request, storage, presentation, and management of Digital Credentials. It can be installed (instantiated) in a Personal Device or in a Remote Service.

**Wallet Solution**:
: The Wallet Solution is a product offered by a Wallet Provider to enable End-Users to securely manage and use their Digital Credentials. It is delivered by the Wallet Provider in the form of mobile app or cloud service or another form of software application. It may also utilize services and web services for the exchange of data between its Wallet Provider and the Wallet Instances.

**Authentic Source**:
: A protected Resource Server, not necessarily an OAuth 2.0 Resource Server, utilized by the Credential Issuer to retrieve the data necessary for issuing a Credential related to a subject.

**Credential Verifier**:
: Entity that requests and verifies Digital Credentials presented by a Holder. 

**Credential Verifier Instance**:
: A software application that allows an individual to request to an Holder and receive from that Holder a Digital Credential, sometimes in a proximity flow, and then verify the received Digital Credential.

## Trust Models and Trust Frameworks

The terms "trust model" and "trust framework" are often used in the context of security, identity management, and federation systems.

The Trust Model defines the relationships and mechanisms through which trust is established and maintained between entities in a system. It outlines how entities interact, the basis on which they can trust each other, and the roles they play within the system. Trust Models can be simple or complex, depending on the number of parties involved and the nature of their interactions. Common examples include:

- **Direct Trust**: Trust is established directly between two parties without intermediaries.
- **Trusted Third Party**: Trust is facilitated by a trusted third party.
- **Web of Trust**: Each participant makes individual decisions about whom to trust, using Direct Trust or potentially multiple Third-Parties.

**Trusted Third-Party** is the focus of this specification, although the **Web of Trust** model is not excluded if multiple trusted third parties (Trust Anchors) are supported by the participants.

A Trust Framework is a comprehensive structure that includes policies, standards, and guidelines that govern the implementation of a Trust Model. It provides detailed rules for how trust should be managed, including the legal, technical, and procedural aspects. To allow for a scalable approach, as many aspects of the framework as possible should be presented in a machine discoverable and machine-readable way.

In the scope of this specification, only the technical and procedural aspects are considered and fully covered.

OpenID Federation [@!OpenID.Federation] is a building block for assembling and using trust frameworks. It can help ensure that all participants in a system understand and adhere to the same principles and practices, making interactions predictable and secure.

# The Four-Party Model

The Four-Party Model is a framework involving four key participants:
the Holder, the Credential Issuer, the Credential Verifier,
and an Entity trusted by the other Entities called the Trust Anchor.
This is an extension of the three-party Issuer-Holder-Verifier Model described in
[@!OpenID4VCI] and [@!OpenID4VP] that adds a fourth party: the Trust Anchor.


The four Entities interact with each other as described below:

1. **Holder**: The Holder requests, stores, presents, and manages Digital Credentials and other forms of digital attestations. It discovers trustworthy Credential Issuers through the Trust Anchor and its Intermediates. Additionally, the Holder evaluates trust with Credential Verifiers recognized by the Trust Anchor and its Intermediates and checks for the non-revocation of the other Entities in use.
2. **Credential Issuer**: This Entity issues Digital Credentials to the Holder, after having evaluated the trust in the Wallet Solution and the security of the Holder.
3. **Credential Verifier**: This is any Entity that requires proof of the End-User's identity, through the presentation of Credentials, to provide services or carry out transactions. Credential Verifiers rely on the validity of the Digital Credentials presented via the End-User's Wallet. They MUST have the means to verify these Credentials against the Credential Issuer's cryptographic public keys or other verification methods to ensure they are authentic and have not been tampered with. The Credential Verifier uses the Trust Anchor and its Intermediates to establish the trust with the Credential Issuers, obtains their metadata and cryptographic material, and check the validity of the presented Digital Credentials. It also establishes trust with the Holder and the Wallet Solution used by it.
4. **Trust Anchor**: This Entity and its Intermediates, issue Subordinate Statements and any required information about the status of the Federation and its participants (Organizational Entities), to demonstrate their non-revocations, distribute the policies and prevents the repudiation of the past transaction about any trust evaluation, if signed. Historical proofs allow for the evaluation of an Organizational Entity's status within a federation and their past signatures, which can be verified using a historical Trust Chain.


~~~ ascii-art
+-------------------+    +---------------+    +---------------------+
| Credential Issuer |<-->|    Holder     |<-->| Credential Verifier |
|                   |    |               |    |                     |
+-------------------+    +---------------+    +---------------------+
         |                       |                     |
         |                       |                     |
         V                       V                     V
+--------------------------------------------------------------+
|                          Trust Anchor                        |
+--------------------------------------------------------------+
~~~
**Figure 1**: The relationships and interactions within a Wallet ecosystem using the Four-Party Model, where each entity uses the Trust Anchor to establish the trust with other entities.

In the Wallet Ecosystem, the primary interaction resolves around asset management. Unlike an Identity Provider in OpenID Connect or SAML2, which authenticates the End-User's identity for third parties, the Credential Issuer in the Wallet ecosystem focuses on managing the issuance of Digital Credentials to the Holder.

The transactions primarily involve the transfer or management of Digital Credentials rather than granting access to services based on identity verification.

Consequently, the End-User obtains and holds the Digital Credentials without disclosing their intended use to the Credential Issuers. At any subsequent time, the End-User can present these Digital Credentials to a Credential Verifier to authenticate themselves.


~~~ ascii-art
+------------------+     +-----------------+
| Authentic Source |     | Wallet Provider |
|                  |     |                 |
+------------------+     +-----------------+
         |                       |
         |                       |
         V                       V
+-------------------+    +---------------+    +---------------------+
| Credential Issuer |<-->|    Holder     |<-->| Credential Verifier |
|                   |    |               |    |                     |
+-------------------+    +---------------+    +---------------------+
         |                       |                     |
         |                       |                     |
         V                       V                     V
+-------------------------------------------------------------------+
|                          Trust Anchor                             |
+-------------------------------------------------------------------+
~~~
**Figure 2**: Representation acknowledging the roles of Authentic Sources and Wallet Providers in the ecosystem while maintaining the core structure of the Four-Party Model.

The Figure above illustrates at the center the Holder, who interacts directly with both the Credential Issuer and the Credential Verifier. The Credential Issuer provides Digital Credentials to the Holder, while the Credential Verifier relies on these Credentials to verify the Holder's claims. Above the Holder is the Wallet Provider, which facilitates the registration and the attestation of the security and integrity of the Holder. All entities, including the Credential Issuer, Credential Verifier, Wallet Provider and therefore Holders, and are underpinned by a Trust Anchor, ensuring that all interactions and transactions are anchored in a trusted third party.

# Wallet Instance Types

There are many ways to technically implement Wallet Instances to manage Digital Credentials. There are typically two types of Wallet End-Users: one is a natural person and another is an Organizational Entity. These two types of End-Users may have different usage and functional requirements.

Below a non-exhaustive list of the different Wallet Instance types.

**Mobile Wallet Native Application**
: Also known as Mobile Wallet only, is an application that runs natively on a Personal Device under the sole control of an End-User and provided through a platform vendor specific app-store, on behalf of the Wallet Solution. In some cases the End-User as natural person uses the Mobile Wallet representing a legal person.

**Web Wallet Native Application**
: Also known as Cloud Wallet or Web Wallet only, is a Wallet that uses native web technologies for its components, such as UI components. Cloud Wallets are typically suited for Organizational Entities that requires automated Digital Credential operations (request, issuance, store, presentation, revocations) in unsupervised flows, therefore without any human control. Web Wallets are divided into two additional subtypes:
    - **Custodial Web Wallet**: Cloud Wallets that have dependency on a cloud infrastructure, not necessarily hosted by the Wallet Provider, are typically classified as Custodial Web Wallets; in this case, the cryptographic keys used and the Digital Credentials are stored in the cloud infrastructure.
    - **Non-Custodial Web Wallet**: A Web Wallet where the cryptographic keys are stored and managed on a media in possession by the End-User and the Digital Credentials can only be used by the End-User, e.g. using a FIDO enabled security hardware token, no matter whether the Credentials are stored locally in a Personal Device or in cloud storage.

**Progressive Web Application Wallet** (PWAW)
: PWAW is a web application that looks like a native app. It can be installed on a Personal Device and not necessarily using the operative system specific app-store. The advantage with a PWAW is that it gives the End-User the same experience as a Mobile Native Wallet Application while also offering the benefits of a web application. PWAW can be Custodial or Non-Custodial.

## Establishing Trust with the Holder

Since the Holder may not be an Organizational Entity and cannot be registered as an Organization through registration services, it is not represented within a Trust Chain and does not qualify as a Federation Entity. This context sets the stage for understanding the unique position of the Holder in relation to the Trust Chain and Federation Entities.

~~~ ascii-art
+----------------------------+
| Trust Chain                |
| +------------------------+ |
| | Trust Anchor           | |
| | (Entity Configuration) | |
| +------------------------+ |
|                     |      |
|                     v      |
| +------------------------+ |
| | Trust Anchor           | |
| | (Subordinate Statement | |
| |  about the             | |
| |  Wallet Provider)      | |
| +------------------------+ |
|                     |      |
|                     v      |
| +------------------------+ |
| | Wallet Provider        | |    +-------------------------------+
| | (Entity Configuration) |----->|      Wallet Attestation       |
| +------------------------+ |    | (Not part of the Trust Chain) |
+----------------------------+    +-------------------------------+
~~~
**Figure 3**: Federation Trust Chain and Wallet Attestation are separate things, where the Wallet Attestation is linked to its Issuer attested within the Trust Chain.

Outside the Trust Chain, it is the Wallet Attestation, where the Wallet Provider that issued it is attestable through the Trust Chain, while the Wallet, such as the End-User's Native Mobile Application installed on the Personal Device, is attested through the Wallet Attestation and under the responsibility of its issuer, the Wallet Provider.

# Wallet Architecture Entity Types

This section defines the Entity Types used by Organizational Entities in their Entity Configurations according to their roles in the Wallet ecosystem.

| Entity         | Entity Type Identifiers                                              | References                          |
|-----------------------|------------------------------------------------------------|-------------------------------------|
| Trust Anchor          | `federation_entity`                                        | [@!OpenID.Federation]                       |
| Intermediate          | `federation_entity`                                        | [@!OpenID.Federation]                       |
| Wallet Provider       | `federation_entity`, `openid_wallet_provider`              | this specification                                  |
| Authorization Server  | `federation_entity`, `oauth_authorization_server`          | [@!OpenID4VCI], [@!RFC8414]                    |
| Credential Issuer     | `federation_entity`, `openid_credential_issuer`, `oauth_authorization_server` | [@!OpenID4VCI], this specification |
| Credential Verifier   | `federation_entity`, `openid_credential_verifier`          | [@!OpenID.Federation], [@!OpenID4VP], this specification |

**Table 1**: Map of the Federation Entity Types and corresponding metadata types for the Wallet architectures.


The Credential Issuer is an OAuth 2.0 Protected Resource Server and it MAY also implement, within the same Entity, an OAuth 2.0 Authorization Server. According to [@!OpenID4VCI], the Authorization Server can be external to the Entity that implements the Credential Endpoint, therefore the use of `oauth_authorization_server` is OPTIONAL.

## OpenID Wallet Provider Entity Type

The OpenID Federation Entity Type Identifier for the Wallet Provider is `openid_wallet_provider`.

For information on metadata parameters specific to OpenID Wallets,
refer to Section *10. Wallet Metadata (Authorization Server Metadata)* of
the OpenID for Verifiable Presentations [@!OpenID4VP] specification.

## OpenID Credential Issuer Entity Type

The OpenID Federation Entity Type Identifier for the Credential Issuer is `openid_credential_issuer`.

For information on metadata parameters specific to OpenID Credential Issuers,
refer to Section 11.2 (Credential Issuer Metadata) and Section 12.2.4 (Credential Issuer Metadata Parameters) of
the OpenID for Verifiable Credential Issuance [@!OpenID4VCI] specification.

### OpenID Credential Issuer Metadata Parameters

When the Credential Issuer is represented as a Federation Entity, the
`openid_credential_issuer` metadata is carried inside the Entity Configuration
and Entity Statements under the `metadata` (and, if used, `metadata_policy`)
parameters defined by OpenID Federation [@!OpenID.Federation].

This specification profiles the OpenID4VCI Credential Issuer metadata as
follows when used in Wallet federations:

- `credential_issuer`:
  The Credential Issuer Identifier as defined in [@!OpenID4VCI], Section 12.2.1.
  It MUST be a case-sensitive `https` URL and MUST be equal to the
  `iss`/`sub` pair value in the Credential Issuer's Entity Configuration and to the
  `sub` value in any Subordinate Statement about that Credential Issuer,
  as defined in [@!OpenID.Federation].

- `authorization_servers`:
  As defined in [@!OpenID4VCI], Section 12.2.4. Each element of this array
  is the Issuer Identifier of an OAuth 2.0 Authorization Server
  [@!RFC8414] that is itself represented as a Federation Entity of
  type `oauth_authorization_server` in this specification (see Table 1).

- `credential_endpoint`, `nonce_endpoint`, `deferred_credential_endpoint`,
  `notification_endpoint`, `credential_request_encryption`,
  `credential_response_encryption`, `batch_credential_issuance`,
  `display`, and `credential_configurations_supported`:
  These parameters MUST follow the definitions and processing rules in
  [@!OpenID4VCI], Section 12.2.4 and Appendix A and are carried
  unchanged in the `openid_credential_issuer` metadata inside the
  Federation `metadata` / `metadata_policy` structures.

- `jwks`:
  A JSON Web Key Set document containing the protocol-specific public keys
  of the Credential Issuer. When present in the `openid_credential_issuer`
  metadata, it MUST follow the same conventions as the `jwks` parameter
  defined for Federation Entities in [@!OpenID.Federation], Section 5.2.1.
  The keys in this set are used for signature of responses, tokens, and
  issued credentials. When a trust framework requires the Credential Issuer
  to sign its metadata (for example, when serving signed metadata at
  `.well-known/openid-credential-issuer` as defined in [@!OpenID4VCI],
  Section 12.2.3, using an X.509 certificate or other cryptographic
  material), the public key material used to verify that signature SHOULD be
  provided in the `jwks` parameter within the `openid_credential_issuer`
  metadata of the OpenID Federation Entity Configuration.

Additional metadata parameters MAY be defined by profiles of this
specification and OpenID4VCI (for example, `status_list_aggregation_endpoint`
as in [@!I-D.ietf-oauth-status-list]). In accordance with [@!OpenID4VCI],
Wallets and other clients interacting with `openid_credential_issuer`
metadata MUST ignore unrecognized metadata parameters.

OpenID4VCI also defines JOSE header parameters such as `trust_chain`
for proofs and signed metadata (see Appendix F.1 and Section 12.2.3
of [@!OpenID4VCI]), which enable a Credential Issuer to bind its keys
and metadata to an OpenID Federation Trust Chain [@!OpenID.Federation].
Use of these mechanisms is OPTIONAL; when they are used, they MUST be
consistent with the federation model defined in this document.

## OpenID Credential Verifier Entity Type

The OpenID Federation Entity Type Identifier for the Credential Verifier is `openid_credential_verifier`.

This specification introduces a distinct Entity Type Identifier for the OpenID Credential Verifier to clearly differentiate it from a traditional OpenID Connect Relying Party (`openid_relying_party`). This distinction highlights the unique characteristics and functionalities of the Wallet ecosystem and its Credential Verifier.

For information on metadata parameters specific to OpenID Credential Verifiers,
refer to Section *11. Verifier Metadata (Client Metadata)* of
the OpenID for Verifiable Presentations [@!OpenID4VP] specification.

### Additional OpenID Credential Verifier Metadata Parameters

This profile defines additional requirements on some `openid_credential_verifier`
metadata parameters used when a Credential Verifier acts as an OpenID4VP Verifier
and participates in a federation.

Unless otherwise stated, the definitions and processing rules from
OpenID for Verifiable Presentations [@!OpenID4VP] and
OpenID Connect Dynamic Client Registration 1.0 [@!OpenID.Registration]
apply.

The following parameters are used within the `openid_credential_verifier`
metadata:

`jwks`:
: OPTIONAL. A JSON Web Key Set, as defined in [@!RFC7591] and [@!RFC7517],
  that contains one or more public keys used by the Credential Verifier for
  OpenID4VP interactions.
  Keys in this set are used by Wallets to verify signed Request Objects and,
  when supported by the profile, as input to key agreement or for encrypting
  Authorization Responses as defined in [@!OpenID4VP], Sections 5 and 8.3.
  Each JWK in the set MUST have a `kid` (Key ID) parameter that uniquely
  identifies the key within the context of the Credential Verifier.
  When a Credential Verifier participates in a federation and exposes
  `openid_credential_verifier.jwks` via its Entity Configuration and the
  evaluated Trust Chain, Wallets and other relying parties MUST use the
  keys obtained from the federation metadata and MUST ignore any `jwks`
  value conveyed in `client_metadata` within the Authorization Request.
  Only when no federated `openid_credential_verifier` metadata is available
  MAY implementations rely on `jwks` from `client_metadata` as defined in
  [@!OpenID4VP].

`request_uris`:
: OPTIONAL. A non-empty array of HTTPS URLs that are pre-registered Request
  Object endpoints for the Credential Verifier.
  Each value corresponds to a `request_uri` that MAY be used in
  Authorization Requests as defined in [@!RFC9101] and [@!OpenID4VP],
  Section 5.10.
  Wallets and Federation Authorities MUST treat these values as the set of
  trusted Request Object endpoints for the Credential Verifier.

`response_uris`:
: OPTIONAL. A non-empty array of HTTPS URLs that are pre-registered Response
  URIs for the Credential Verifier.
  Each value corresponds to a `response_uri` that MAY be used in
  Authorization Requests in conjunction with the `direct_post` and
  `direct_post.jwt` Response Modes as defined in [@!OpenID4VP],
  Section 8.2 and Section 8.3.2.
  Wallets and Federation Authorities MUST treat these values as the set of
  trusted Response Endpoints for the Credential Verifier.

`redirect_uris`:
: OPTIONAL. A non-empty array of HTTPS Redirect URIs at which the Credential
  Verifier continues the user interaction after processing an Authorization
  Response.
  Each value corresponds to a `redirect_uri` used with
  Response Mode `fragment` or as the `redirect_uri` value returned from the
  Response Endpoint in the `direct_post` and `direct_post.jwt` modes, as
  defined in [@!OpenID4VP], Section 8.2.
  Wallets and Federation Authorities MUST treat these values as the set of
  trusted Redirect URIs for the Credential Verifier.

`dcql_queries`:
: OPTIONAL. A non-empty array of JSON objects, each containing a Digital
  Credentials Query Language (DCQL) query as defined in [@!OpenID4VP],
  Section 6.
  Each array element has the same structure as the `dcql_query` Authorization
  Request parameter in [@!OpenID4VP].
  When present, this parameter lists the DCQL queries that the Credential
  Verifier is authorized to use in different situations (e.g. identification,
  education, employment, voluntary work).
  Profiles of this specification MAY define policies that require the
  `dcql_query` in an Authorization Request to be equal to, or a constrained
  refinement of, one of the query objects in this array.

#### Rationale for Extending the Set of `openid_credential_verifier` Metadata Parameters

The rationale for extending the set of `openid_credential_verifier` metadata
parameters is to align OpenID4VP Verifiers with the Trusted Third-Party trust
model employed by this specification. Cryptographic material, protocol endpoints 
and default or constrained DCQL queries are published in federation-managed metadata,
constrained DCQL queries in federation-managed metadata. This practice aims to:

- enforce common technical and policy requirements on all participating
  Credential Verifiers,
- restrict which endpoints and keys are considered trustworthy for
  presentation flows, and
- limit the kinds of Digital Credentials and claims that a Credential
  Verifier is allowed to request from Wallets.

When these parameters are expressed and enforced through Subordinate
Statements, as defined in [@!OpenID.Federation], critical configuration and
authorization decisions move from ad‑hoc, per‑instance configuration into a
federated, policy-driven framework anchored in the Trust Anchor and the Superior Entities.

### OpenID Federation Client IDs Used with OpenID for Verifiable Presentations

Note that Section 5.9.3 of [@!OpenID4VP] defines that the Client ID values
specified by Section 12.1 of [@!OpenID.Federation], which are Entity Identifiers,
MUST be prefixed by `openid_federation:` when used with [@!OpenID4VP].
Therefore, such Client IDs MUST be prefixed when used with
[@!OpenID4VP] protocols and APIs
and the prefix MUST be disregarded from any Client IDs received from
[@!OpenID4VP] protocols and APIs
when applying the processing rules in OpenID Federation 1.0. The prefix MUST be used wherever the client id is used in responses, for example `aud` values in JWTs.
To guard against confusion attacks with other protocols, Authorization servers are suggested to implement this by internally prefixing all federation client_ids with `openid_federation:`, ensuring non-federation clients cannot be assigned a client id beginning with `openid_federation:`, and removing the prefix when returning replies to Federation 1.0 clients.


# Federation Policies

Policies refer to a set of rules that govern the operations, security, and interactions within a federation.

Technical implementation of federation policies over participants metadata is managed with the use of `metadata` and `metadata_policy` parameters in Subordinate Statements. These parameters allow for the configuration enforcement of application-specific metadata changes for each subject (Leaf).

Qualitative aspects of federation entities, including administrative protocols, security measures, and behavioral profiling, are regulated by Trust Marks. These marks provide verifiable assertions of compliance with specific profiles beyond the scope of the application-specific metadata.

## Using Metadata

Metadata refers to application-specific properties about a subject and for the purpose of the interoperability. This includes details such as service endpoints, cryptographic keys, and other supported configurations.

Metadata within a Subordinate Statement allows for modifications to the metadata published in a Leaf's Entity Configuration.
These modifications allow a federation authority, such as a Trust Anchor, to apply policies coercively to its subordinates. This can include actions such as removing weak signature algorithms from their metadata, enforcing the use of specific endpoints configured at the time of the entity's registration within the ecosystem, or restricting the personal data that a Credential Verifier is allowed to request.

```
{
  "iss": "https://trust-anchor.example.com",
  "sub": "https://credential-verifier.example.it",
  "iat": 1616239022,
  "exp": 1616239322,
  "metadata": {
    "federation_entity": {
      "organization_name": "Example Credential Verifier",
    },
    "openid_credential_verifier": { ... as defined in the OpenID4VP specs ... }
  },
  "jwks": {
    "keys": [
      {
        "kty": "RSA",
        "use": "sig",
        "kid": "1",
        "n": "0vx7agoebGcQSuuPiLJXZptN9nndrQmbXEPCRfceaC7mkxr8v...",
        "e": "AQAB"
      }
    ]
  }
}
```
**Example 1**: Example demonstrating how a Federation Authority can issue a Subordinate Statement about a Credential Verifier, specifying certain metadata parameters such as the endpoints to use and the allowed Digital Credentials to be requested.

### OpenID Credential Verifier Presentation Metadata in Subordinate Statements

When the subject of a Subordinate Statement is a Credential Verifier, the
`metadata` and `metadata_policy` members of that Subordinate Statement MAY
contain an `openid_credential_verifier` object.
This profile uses these objects to control the OpenID4VP behaviour of the
Credential Verifier in a way that can be enforced by superior entities.

Superior entities (for example Trust Anchors or Intermediates):

- MAY set, add or replace the `openid_credential_verifier.jwks` value
  to constrain which public keys are accepted for signing Request Objects
  and, where applicable, for response encryption.
- MAY set or restrict the `openid_credential_verifier.request_uris`,
  `openid_credential_verifier.response_uris`, and
  `openid_credential_verifier.redirect_uris` values to the endpoints that
  have been registered and approved for the Credential Verifier.
- MAY add or constrain the `openid_credential_verifier.dcql_queries` value to
  define the list of DCQL queries that the Credential Verifier is permitted
  to use.

When `metadata` is used, the resulting `openid_credential_verifier` metadata
in the Leaf Entity Configuration after Trust Chain evaluation represents the
effective set of keys, endpoints, and DCQL queries that the Wallet MUST use
for trust and policy checks.

When `metadata_policy` is used, the same constraints are propagated along the
Trust Chain according to [@!OpenID.Federation], Sections 3, 5, and 6.1.
In particular:

- A Wallet evaluating a Credential Verifier MUST treat the final
  `openid_credential_verifier.request_uris`,
  `openid_credential_verifier.response_uris`, and
  `openid_credential_verifier.redirect_uris` values as the authoritative
  lists of allowed endpoints.
  Any `request_uri`, `response_uri`, or `redirect_uri` appearing in an
  Authorization Request that is not contained in the corresponding list
  MUST cause the Wallet to reject the request to prevent endpoint mix-up
  attacks, consistent with the considerations in [@!OpenID4VP],
  Section 8.2 and the checks defined for Relying Parties in the
  IT Wallet specifications.
- A Wallet evaluating a Credential Verifier SHOULD use the effective
  `openid_credential_verifier.dcql_queries` metadata to determine which
  Credential types and claims the Credential Verifier is authorized to
  request.
  When a `dcql_queries` value is provided via
  `metadata.openid_credential_verifier` in a Subordinate Statement,
  any `dcql_query` conveyed by the Credential Verifier in the Request Object
  or in `client_metadata` MUST be ignored, as it is overridden by the
  federation-managed list.
  When a `dcql_queries` constraint is expressed via
  `metadata_policy.openid_credential_verifier`, that policy MUST be
  applied to any `dcql_queries` present in the Credential Verifier metadata,
  if available, or otherwise to the `dcql_query` contained in
  `client_metadata` in the Authorization Request.
  Profiles MAY additionally convey `dcql_queries`-related policies using
  Trust Marks bound to the Credential Verifier; see the section on
  Trust Marks and policy expression for further guidance.

This mechanism allows superior entities to centrally define and enforce
policy on Credential Verifiers’ OpenID4VP behaviour (including cryptographic
material, endpoints, and requested Digital Credentials) while Wallets rely
only on the final, policy-processed `metadata.openid_credential_verifier`
obtained from Trust Chain evaluation.

## Differences Between `metadata` and `metadata_policy`

The key difference between `metadata` and `metadata_policy` is that metadata directly affects only the Immediate Subordinate Entity, while `metadata_policy` impacts the configuration of all Subordinate Entities along a Trust Chain, as defined in Sections 3, 5 and 6.1 of [@!OpenID.Federation].


## Using Metadata Policies

Differently from `metadata`, `metadata_policy` ensures that specific settings can be propagated to all the Entities Statements contained within a Trust Chain.

## Using Trust Marks

Trust Marks are issued by authorized entities (Trust Mark Issuers) within the federation, typically after an entity has demonstrated compliance with certain standards, this might happen through auditing or certification processes.

Trust Marks are typically implemented as signed assertions that can be verified by other entities.

This verification process involves checking the digital signature against the public key of the Trust Mark Issuer to ensure the Trust Mark has not been forged, and its check to the Trust Mark Status endpoint to check it against any revocation.

Trust Marks SHOULD be defined within the trust framework. Trust Marks are asserted about a subject through a registration service or compliance evaluation mechanism and therefore included in subject's Entity Configurations. This allows other entities to quickly assess the compliance status of a subject by examining the Entity Configuration of a subject.


```json=
{
  "trust_mark_type":"https://diligent.federation.example.com/openid_credential_verifier/private/under-age",
  "iss": "https://trustissuer.pinarolo.example.it",
  "sub": "https://vavuso.example.com/rp",
  "iat": 1579621160,
  "policy_uri": "https://vavuso.example.com/policy",
  "tos_uri": "https://vavuso.example.com/tos"
}
```
**Example 2**: Trust Mark to be included in a Leaf Entity Configuration, which payload states Leaf's compliance in interacting with under-age End-User.

# Federation Trust Discovery Use Cases

The process of trust establishment in federated environments is illustrated in this section through specific use cases involving Wallet Instances, Credential Issuers (CIs), and Credential Verifiers (CVs).

## Establishing Trust with a Credential Verifier Instance

A Credential Verifier Instance is typically installed on a mobile device, personal computer, or embedded system. It enables an individual to perform Digital Credential verification tasks locally, often in proximity to the Holder, and without necessarily requiring a broadband connection. This instance engages in peer-to-peer exchanges with Holders, facilitating Credential verifications directly on the device. This approach represents a shift from traditional server-based verification to a more user-centric model within the Wallet ecosystem.

To establish trust between a Holder's Wallet Instance and a Credential Verifier Instance, a mechanism using a verifiable attestation, such as the Wallet Instance Attestations, SHOULD be employed. This process ensures that the Credential Verifier Instance is legitimate and trustworthy.

The mechanisms used to present the Wallet Instance Attestation to a Credential Verifier are out of scope for this specification, as they are related to Credential presentation flows. For implementation details on these presentation mechanisms, please refer to the OpenID for Verifiable Presentations (OpenID4VP) specification.  

## Wallet Checking the Non-Revocation of its Wallet Provider

Wallets SHOULD periodically check their Wallet Providers' compliance through the federation's trust infrastructure. This involves retrieving the Wallet Provider's Entity Configuration and verifying its Trust Chain up to a recognized Trust Anchor, ensuring that the Wallet Provider has not been revoked within the federation. Wallets SHOULD remain neutral in attesting to the reliability of their Wallet Providers for the End-User, thereby protecting the End-User against any malevolent behavior by the Wallet Provider.

The Wallet Provider’s Entity Configuration provides essential information, including its roles within the federation, policies it adheres to, and cryptographic keys for secure communication. The Wallet Instance SHOULD use the Federation API to periodically reestablish trust with its Wallet Provider.

The process to discover the trust with a Wallet Provider is equivalent to the one used for discovering the trust with a Credential Issuer, as described in the dedicated section below.

## Wallet Discovering Credentials Issuers

Wallets begin by discovering the identity of Credential Issuers through the federation's trust infrastructure. This involves retrieving the Credential Issuer's Entity Configuration and verifying its Trust Chain up to a recognized Trust Anchor. The Credential Issuer’s Entity Configuration provides essential information, including its roles within the federation, policies it adheres to, and cryptographic keys for secure communication.

In the process represented in the sequence diagram below, the Wallet Instance uses the Federation API to discover and collect all the Credential Issuers enabled within the federation.

~~~ ascii-art

        +------+                        +------------+ +------------+ +-----------------+
        |Wallet|                        |Trust Anchor| |Intermediate| |Credential Issuer|
        +---+--+                        +------+-----+ +------+-----+ +--------+--------+
            |  Fetch the list of all           |              |                |
            |  Intermediates and CIs           |              |                |
            |--------------------------------->|              |                |
            |                                  |              |                |
+-----------+----------------------------------+--------------+------+         |
| LOOP  |for each Intermediate                 |              |      |         |
|-------+   |                                  |              |      |         |
|           | Fetch listing of                 |              |      |         |
|           | Subordinates/CIs                 |              |      |         |
|           |------------------------------------------------>|      |         |
+-----------+----------------------------------+--------------+------+         |
            |                                  |              |                |
+-----------+----------------------------------+--------------+----------------+--------+
| LOOP  |for each CI                           |              |                |        |
|-------+   |                                  |              |                |        |
|           | Fetch CI's Entity Configuration  |              |                |        |
|           |----------------------------------------------------------------->|        |
|           |                                  |              |                |        |
|           | Fetch Subordinate Statement(s)   |              |                |        |
|           | for CI                           |              |                |        |
|           |------------------------------------------------>|                |        |
|           |                                  |              |                |        |
|           | Fetch Subordinate Statement      |              |                |        |
|           | for Intermediate(s)              |              |                |        |
|           |--------------------------------->|              |                |        |
|           |----+                             |              |                |        |
|           |    | Validate Trust Chain        |              |                |        |
|           |    | for CI                      |              |                |        |
|           |<---+                             |              |                |        |
|           |                                  |              |                |        |
|           |----+                             |              |                |        |
|           |    | Include CI in               |              |                |        |
|           |    | Discovery Page              |              |                |        |
|           |    | with validated              |              |                |        |
|           |    | information and logo        |              |                |        |
|           |<---+                             |              |                |        |
+-----------+----------------------------------+--------------+----------------+--------+
        +---+--+                        +------+-----+ +------+-----+ +--------+--------+
        |Wallet|                        |Trust Anchor| |Intermediate| |Credential Issuer|
        +------+                        +------------+ +------------+ +-----------------+
~~~
**Figure 4**: Federation Credential Issuer listing, the Wallet Instance browse the entire federation collecting all the Credential Issuers.  


The diagram above shows how a Wallet navigates the federation, collecting and validating the Trust Chain for each Credential Issuer (CI), and creating a discovery page including each Credential Issuer using the information, such as the Credential Types and logo obtained through their Trust Chain.

The diagram below illustrates how a Wallet establishes trust with a Credential Issuer by verifying its link (even if indirect) to a Trust Anchor and validating which Credentials it is authorized to issue. This may happen in a credential offer flow, for instance, where the Wallet is used by an End-User starting from the Credential Issuer website and without any discovery phases started before within the Wallet.

~~~ ascii-art
        +------+                                 +-----------------+ +-------------------------+          
        |Wallet|                                 |Credential Issuer| |Intermediate/Trust Anchor|          
        +---+--+                                 +--------+--------+ +------------+------------+          
            |    Fetch CI's Entity Configuration          |                       |                       
            |-------------------------------------------->|                       |                       
            |                                             |                       |                       
            |----+                                        |                       |                       
            |    | Extract Authority Hints                |                       |
            |    | from CI's Configuration                |                       |                       
            |<---+                                        |                       |                       
            |                                             |                       |                       
            |                                             |                       |                       
+-------+---+---------------------------------------------+-----------------------+------------+
| LOOP  |for each Authority Hint                          |                       |            |
+-------+   |                                             |                       |            |
|           |                     Fetch Entity Configuration                      |            |
|           |-------------------------------------------------------------------->|            |
|           |                                             |                       |            |
|           |                    Fetch Subordinate Statement                      |            |
|           |-------------------------------------------------------------------->|            |
|           |                                             |                       |            |
|           |----+                                        |                       |            |
|           |    | Validate the previous statement        |                       |            |
|           |<---+ using the Federation Entity Keys       |                       |            |
|           |      provided in the Subordinate Statement  |                       |            |
|           |                                             |                       |            |
+-----------+---------------------------------------------+-----------------------+------------+
            |                                             |                       |                       
            |----+                                        |                       |                       
            |    | Validate Trust Chain                   |                       |                       
            |<---+                                        |                       |                       
            |                                             |                       |                       
            |                                             |                       |                       
+------+----+----------------------------------------+    |                       |                       
| ALT  |If Trust Chain is Valid and Unexpired        |    |                       |                       
+------+    |                                        |    |                       |                       
|           |----+                                   |    |                       |                       
|           |    | Proceed with Federation Process   |    |                       |                       
|           |<---+                                   |    |                       |                       
+-----------+----------------------------------------+    |                       |                       
|           |                                        |    |                       |                       
|           |----+                                   |    |                       |                       
|           |    | Abort Process with Error          |    |                       |                       
|           |<---+                                   |    |                       |                       
+-----------+----------------------------------------+    |                       |                       
            |                                             |                       |                       
            |----+                                        |                       |                       
            |    | Applies Policies                       |                       |                       
            |<---+                                        |                       |                       
            |                                             |                       |                       
            |----+                                        |                       |                       
            |    | Derive CI's final metadata             |                       |                       
            |<---+                                        |                       |                       
            |                                             |                       |                       
            |----+                                        |                       |                       
            |    | Get available Credentials              |                       |
            |    | allowed for issuance                   |                       |                       
            |<---+                                        |                       |                       
        +---+--+                                 +--------+--------+ +------------+------------+          
        |Wallet|                                 |Credential Issuer| |Intermediate/Trust Anchor|          
        +------+                                 +-----------------+ +-------------------------+     
~~~
**Figure 5**: Federation Entity Discovery, the Wallet Instance evaluates the trust with a Credential Issuer.


## Credential Issuers Establishing Trust in the Wallet Provider

The evaluation of trust by the Credential Issuer towards the Wallet Provider is conducted exactly as other federation entities. This process can be achieved through Federation Entity Discovery, where the Trust Chain is constructed starting from the Entity Configuration of the Wallet Provider. The Credential Issuer retrieves the Entity Configuration of the Wallet Provider and follows the `authority_hints` to build the Trust Chain in the usual manner. 

Alternatively, trust can be established via a signed data object issued by Wallet Provider, which includes the `trust_chain` parameter, as defined in Section 4.3 of [@!OpenID.Federation]. This parameter contains a pre-constructed and verifiable Trust Chain, which MUST be validated using one of the the public keys of the Trust Anchor. This method allows for a streamlined trust evaluation process, as the Trust Chain is provided directly by the Wallet Provider and can be quickly validated.

## Credential Issuers Establishing Trust in the Wallet

During the issuance phase, the Wallet Instance authenticates with the Credential Issuer using a Client authentication mechanism that includes a proof issued by its Wallet Provider. 

This proof is a signed data object that confirms the match of a Wallet Instance to a Wallet Solution, as attested by the Wallet Provider. This proof contains all the information the Credential Issuer requires regarding the security and compliance of the Wallet Instance and the cryptographic proof of possession of this attestation provided by the Wallet Instance presenting it.

To establish trust with the Wallet Instance, the Credential Issuer MUST first establish trust with the Wallet Provider that is the issuer of the verifiable attestation, as described in the previous section.

The verifiable attestation issued by the Wallet Provider to the Wallet Instance MUST be cryptographically validated using the cryptographic material provided by the federation Trust Chain. The Credential Issuer evaluates the adequacy of these verifiable attestations using mechanisms and rules that might depend upon different regulations and frameworks that are out of the scope of this specification.

## Wallet Establishing Trust in the Credential Verifier

The Federation Entity Discovery starts with the Wallet Instance fetching the Credential Verifier's Entity Configuration to identify authority hints, pointing to Federation Entities that can issue Subordinate Statements about the Credential Verifier. The Wallet Instance then follows these hints and collects the Subordinate Statements and validating each one. The process continues until the Wallet Instance reaches the Trust Anchor. Finally, the Wallet Instance compiles the validated Trust Chain. If the Trust Chain is valid, the Wallet Instance processes the Credential Verifier final metadata.

Note: While this section exemplifies the journey of discovery from the perspective of an OpenID Wallet Instance, it is important to understand that this approach can be applied to every kind of entity type within the federation.

~~~ ascii-art
        +------+                               +-------------------+ +-------------------------+          
        |Wallet|                               |Credential Verifier| |Intermediate/Trust Anchor|          
        +---┬--+                               +--------+----------+ +--------+----------------+          
            |       Fetch Entity Configuration          |                     |                       
            |------------------------------------------>|                     |                       
            |                                           |                     |                       
            |----+                                      |                     |                       
            |    | Extract Authority Hints              |                     |
            |    | from Entity Configuration            |                     |                       
            |<---+                                      |                     |                       
            |                                           |                     |                       
            |                                           |                     |                       
+-------+---+-------------------------------------------+---------------------+----------+
| LOOP  |for each Authority Hint                        |                     |          |
+-------+   |                                           |                     |          |
|           |                   Fetch Entity Configuration                    |          |
|           |---------------------------------------------------------------->|          |
|           |                                           |                     |          |
|           |                    Fetch Subordinate Statement                  |          |
|           |---------------------------------------------------------------->|          |
|           |                                           |                     |          |
|           |----+                                      |                     |          |
|           |    | Validate the previous statement      |                     |          |
|           |<---+ using the Federation Entity Keys     |                     |          |
|           |      provided in the Subordinate Statement|                     |          |
|           |                                           |                     |          |
+-----------+-------------------------------------------+---------------------+----------+
            |                                           |                     |                       
            |----+                                      |                     |                       
            |    | Validate Trust Chain                 |                     |                       
            |<---+                                      |                     |                       
            |                                           |                     |                       
            |                                           |                     |                       
+------+----+----------------------------------------+  |                     |                       
| ALT  |If Trust Chain is Valid and Unexpired        |  |                     |                       
+------+    |                                        |  |                     |                       
|           |----+                                   |  |                     |                       
|           |    | Proceed with Federation Process   |  |                     |                       
|           |<---+                                   |  |                     |                       
+-----------+----------------------------------------+  |                     |                       
|           |                                        |  |                     |                       
|           |----+                                   |  |                     |                       
|           |    | Abort Process with Error          |  |                     |                       
|           |<---+                                   |  |                     |                       
+-----------+----------------------------------------+  |                     |                       
            |                                           |                     |                       
            |----+                                      |                     |                       
            |    | Applies Policies                     |                     |                       
            |<---+                                      |                     |                       
            |                                           |                     |                       
            |----+                                      |                     |                       
            |    | Derive final metadata                |                     |                       
            |<---+                                      |                     |                       
            |                                           |                     |                       
            |----+                                      |                     |                       
            |    | Get Credentials                      |                     |
            |    | allowed for presentation             |                     |                       
            |<---+                                      |                     |                       
        +---+--+                               +--------+--------+ +----------+--------------+          
        |Wallet|                               |Credential Issuer| |Intermediate/Trust Anchor|          
        +------+                               +-----------------+ +-------------------------+     
~~~
**Figure 6**: Federation Entity Discovery, the Wallet Instance evaluates the trust with a Credential Verifier.

# Implementation Considerations for Offline Flows

The static Trust Chain parameter within the JWT headers, as defined in Section 4.3 of [@!OpenID.Federation], is used as a hint to the Entity involved in a transaction with a common Trust Anchor. This facilitates trust evaluation without the need for real-time Federation Entity Discovery using Federation API endpoints.

The Entity that issues a signed data object, including the `trust_chain` parameter, might be:

- Wallet Providers in signed Wallet Attestations. The Wallet Instance obtains one or more Wallet Attestations from its Wallet Provider, each of them including a Trust Chain related to each Trust Anchor the Wallet Provider trusts;
- Credential Verifiers in signed request objects. The Wallet Instance obtains a presentation request that includes a Trust Chain using a Trust Anchor that the Credential Verifier has in common with the Wallet Provider, according to the information obtained in the `wallet_metadata` parameter provided by the Wallet using the Request URI POST;
- A Credential Issuer in a signed Digital Credential. The Wallet Instance obtains a Digital Credential from its Credential Issuer, which includes the Trust Chain using a Trust Anchor that the Credential Verifier has in common with the Wallet Provider, according to the Wallet Attestation used during the Issuance.

The Entity that receives the data object including the JWT `trust_chain`, such as the Wallet Instance obtaining a signed request object, verifies the Trust Chain using the Trust Anchor's public keys and applies any metadata policies, without needing to have a working network connection for reaching the Federation API.

Using short-lived Trust Chains ensures compatibility with required revocation administrative protocols, such as those defined in a legal framework. For example, if a revocation must be propagated in less than 24 hours, the Trust Chain should not be valid for more than that period.

# Security Considerations

The security considerations in
[@!OpenID.Federation], [@!OpenID4VP], and [@!OpenID4VCI]
apply to this specification.

# IANA Considerations

## OAuth Parameters Registry

This specification registers the following parameter in the IANA "OAuth Parameters" registry [@IANA.OAuth.Parameters] established by [@!RFC6749].

### dcql_queries

* Name: `dcql_queries`
* Parameter Usage Location: authorization request, client metadata, and in `openid_credential_verifier` entity metadata as defined by this specification.
* Change Controller: OpenID Foundation AB/Connect Working Group - openid-specs-ab@lists.openid.net
* Reference: Additional OpenID Credential Verifier Metadata Parameters section of this specification

# Acknowledgements

We would like to thank the following individuals for their comments, ideas, and contributions to this implementation profile and to the initial set of implementations:
Leif Johansson,
Stefan Liström,
Francesco Antonio Marino,
Eduardo Perottoni,
Samuel Rinnetmäki,
Giada Sciarretta,
and
Niels van Dijk.

{backmatter}

<reference anchor="OpenID.Core" target="http://openid.net/specs/openid-connect-core-1_0.html">
  <front>
    <title>OpenID Connect Core 1.0 incorporating errata set 2</title>
    <author initials="N." surname="Sakimura" fullname="Nat Sakimura">
      <organization>NRI</organization>
    </author>
    <author initials="J." surname="Bradley" fullname="John Bradley">
      <organization>Ping Identity</organization>
    </author>
    <author initials="M." surname="Jones" fullname="Michael B. Jones">
      <organization>Microsoft</organization>
    </author>
    <author initials="B." surname="de Medeiros" fullname="Breno de Medeiros">
      <organization>Google</organization>
    </author>
    <author initials="C." surname="Mortimore" fullname="Chuck Mortimore">
      <organization>Salesforce</organization>
    </author>
   <date day="15" month="December" year="2023"/>
  </front>
</reference>

<reference anchor="OpenID.Discovery" target="https://openid.net/specs/openid-connect-discovery-1_0.html">
  <front>
    <title>OpenID Connect Discovery 1.0</title>

    <author fullname="Nat Sakimura" initials="N." surname="Sakimura">
      <organization abbrev="NAT.Consulting (was at NRI)">NAT.Consulting</organization>
    </author>

    <author fullname="John Bradley" initials="J." surname="Bradley">
      <organization abbrev="Yubico (was at Ping Identity)">Yubico</organization>
    </author>

    <author fullname="Michael B. Jones" initials="M.B." surname="Jones">
      <organization abbrev="Self-Issued Consulting (was at Microsoft)">Self-Issued Consulting</organization>
    </author>

    <author fullname="Edmund Jay" initials="E." surname="Jay">
      <organization abbrev="Illumila">Illumila</organization>
    </author>

    <date day="15" month="December" year="2023"/>
  </front>
</reference>

<reference anchor="OpenID.Registration" target="https://openid.net/specs/openid-connect-registration-1_0.html">
  <front>
    <title>OpenID Connect Dynamic Client Registration 1.0</title>

    <author fullname="Nat Sakimura" initials="N." surname="Sakimura">
      <organization abbrev="NAT.Consulting (was at NRI)">NAT.Consulting</organization>
    </author>

    <author fullname="John Bradley" initials="J." surname="Bradley">
      <organization abbrev="Yubico (was at Ping Identity)">Yubico</organization>
    </author>

    <author fullname="Michael B. Jones" initials="M.B." surname="Jones">
      <organization abbrev="Self-Issued Consulting (was at Microsoft)">Self-Issued Consulting</organization>
    </author>

    <date day="15" month="December" year="2023"/>
  </front>
</reference>

<reference anchor="OpenID4VP" target="https://openid.net/specs/openid-4-verifiable-presentations-1_0.html">
      <front>
        <title>OpenID for Verifiable Presentations</title>
        <author initials="O." surname="Terbu" fullname="Oliver Terbu">
         <organization>MATTR</organization>
        </author>
        <author initials="T." surname="Lodderstedt" fullname="Torsten Lodderstedt">
          <organization>SPRIND</organization>
        </author>
        <author initials="K." surname="Yasuda" fullname="Kristina Yasuda">
          <organization>SPRIND</organization>
        </author>
        <author initials="D." surname="Fett" fullname="Daniel Fett">
          <organization>Authlete</organization>
        </author>
        <author initials="J." surname="Heenan" fullname="Joseph Heenan">
          <organization>Authlete</organization>
        </author>
       <date day="9" month="July" year="2025"/>
      </front>
</reference>

<reference anchor="OpenID4VCI" target="https://openid.net/specs/openid-4-verifiable-credential-issuance-1_0.html">
        <front>
          <title>OpenID for Verifiable Credential Issuance</title>
          <author initials="T." surname="Lodderstedt" fullname="Torsten Lodderstedt">
            <organization>SPRIND</organization>
          </author>
          <author initials="K." surname="Yasuda" fullname="Kristina Yasuda">
            <organization>SPRIND</organization>
          </author>
          <author initials="T." surname="Looker" fullname="Tobias Looker">
            <organization>Mattr</organization>
          </author>
          <author initials="P." surname="Bastian" fullname="Paul Bastian">
            <organization>Bundesdruckerei</organization>
          </author>
          <date day="16" month="September" year="2025"/>
        </front>
</reference>

<reference anchor="OpenID.Federation" target="https://openid.net/specs/openid-federation-1_0.html">
        <front>
          <title>OpenID Federation 1.0</title>
		  <author fullname="R. Hedberg, Ed.">
            <organization>independent</organization>
          </author>
          <author fullname="Michael B. Jones">
            <organization>Self-Issued Consulting</organization>
          </author>
          <author fullname="A. Solberg">
            <organization>Sikt</organization>
          </author>
          <author fullname="John Bradley">
            <organization>Yubico</organization>
          </author>
          <author fullname="Giuseppe De Marco">
            <organization>independent</organization>
          </author>
          <author fullname="Vladimir Dzhuvinov">
            <organization>Connect2id</organization>
          </author>
          <date day="15" month="February" year="2026"/>
        </front>
</reference>

<reference anchor="IANA.OAuth.Parameters" target="https://www.iana.org/assignments/oauth-parameters/">
  <front>
    <title>OAuth Parameters</title>
    <author><organization>IANA</organization></author>
  </front>
</reference>

# Notices

Copyright (c) 2026 The OpenID Foundation.

The OpenID Foundation (OIDF) grants to any Contributor, developer, implementer, or other interested party a non-exclusive, royalty free, worldwide copyright license to reproduce, prepare derivative works from, distribute, perform and display, this Implementers Draft, Final Specification, or Final Specification Incorporating Errata Corrections solely for the purposes of (i) developing specifications, and (ii) implementing Implementers Drafts, Final Specifications, and Final Specification Incorporating Errata Corrections based on such documents, provided that attribution be made to the OIDF as the source of the material, but that such attribution does not indicate an endorsement by the OIDF.

The technology described in this specification was made available from contributions from various sources, including members of the OpenID Foundation and others. Although the OpenID Foundation has taken steps to help ensure that the technology is available for distribution, it takes no position regarding the validity or scope of any intellectual property or other rights that might be claimed to pertain to the implementation or use of the technology described in this specification or the extent to which any license under such rights might or might not be available; neither does it represent that it has made any independent effort to identify any such rights. The OpenID Foundation and the contributors to this specification make no (and hereby expressly disclaim any) warranties (express, implied, or otherwise), including implied warranties of merchantability, non-infringement, fitness for a particular purpose, or title, related to this specification, and the entire risk as to implementing this specification is assumed by the implementer. The OpenID Intellectual Property Rights policy (found at openid.net) requires contributors to offer a patent promise not to assert certain patent claims against other contributors and against implementers. OpenID invites any interested party to bring to its attention any copyrights, patents, patent applications, or other proprietary rights that may cover technology that may be required to practice this specification.

# Document History

   [[ To be removed from the final specification ]]

   -06

   * Added note on prefixing Client ID values used with OpenID4VP.

   -05

   * Added subsection "OpenID Credential Issuer Metadata Parameters" under OpenID Credential Issuer Entity Type, profiling `openid_credential_issuer` metadata when used in Wallet federations.
   * Documented `credential_issuer`, `authorization_servers`, and the OpenID4VCI parameters (credential_endpoint, nonce_endpoint, display, credential_configurations_supported, etc.) as carried in Federation metadata/metadata_policy.
   * Added `jwks`: scope for signature of responses, tokens, and issued credentials; requirement that public keys used to verify signed Credential Issuer metadata (e.g. at .well-known/openid-credential-issuer per OpenID4VCI 12.2.3) be provided in `openid_credential_issuer` jwks when trust frameworks require it.
   * Extended OpenID Credential Verifier entity type with additional metadata
     parameters: jwks, request_uris, response_uris, redirect_uris, and
     dcql_queries, with definitions and rationale aligned to the Trusted
     Third-Party trust model.
   * Specified that when a Credential Verifier participates in a federation,
     Wallets MUST use jwks from federation metadata and MUST ignore jwks
     from client_metadata in the Authorization Request.
   * Added section on using openid_credential_verifier metadata and
     metadata_policy in Subordinate Statements for policy enforcement by
     superior entities (endpoints, keys, dcql_queries).
   * Clarified dcql_queries override rules: metadata in a Subordinate
     Statement overrides request/client_metadata; metadata_policy applies to
     verifier metadata or, if absent, to client_metadata; profiles MAY use
     Trust Marks to convey dcql_queries-related policies.
   * Defined dcql_queries as a non-empty array of DCQL query objects (as in
     OpenID4VP) so verifiers can publish multiple authorized queries for
     different situations.
   * Added IANA Considerations registering the dcql_queries parameter.
   * Added Security Considerations.

   -04

   * Changed name to OpenID Federation for Wallet Architectures 1.0.
   * Updated references.
   * Removed unused references.

   -03
   
   * Added section about Credential Issuers establishing trust with Wallet Solution
   * Added sequence diagrams using ascii-arts
   * Renamed Third-Party Trust Model to Trusted Third-Party Model
   * Added legends to the figures
   * Added section about Wallet Checking the Non-Revocation of its Wallet Provider
   * Added implementation Considerations for Offline Flows
   * Improved clarification about using metadata in Subordiante Statements
   
   -02

   * Added non-normative example about using policies with metadata and trust marks
   * Added Credential Verifier and Credential Verifier Instance
   * Added section about Credential Verifier Instance
   * Illustrative rationale about Authentic Sources and Wallet Provider within the Four-Party Model sections
   * Moved text on Possible Use of Metadata Parameters by Wallet Ecosystems to issue #22.
   * Added warning about the specification not being final.

   -01

   * Created Scope section describing the purpose of the document and collaboration with other working groups.
   * Moved metadata tables and examples to an informative appendix on possible usage.
   * Fixed #10: Renamed `openid_wallet_relying_party` to `openid_credential_verifier`.

   -00 

   *  Initial version
