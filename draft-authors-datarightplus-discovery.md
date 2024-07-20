%%%
title = "DataRight+: Discovery"
area = "Internet"
workgroup = "datarightplus"
submissionType = "independent"

[seriesInfo]
name = "Internet-Draft"
value = "draft-authors-datarightplus-discovery-latest"
stream = "independent"
status = "experimental"

date = 2024-07-22T00:00:00Z

[[author]]
initials="S."
surname="Low"
fullname="Stuart Low"
organization="Biza.io"
  [author.address]
  email = "stuart@biza.io"

%%%

.# Abstract

This specification outlines the technical requirements related to the delivery of the DataRight+ Discovery Document.

.# Notational Conventions

The keywords  "**REQUIRED**", "**SHALL**", "**SHALL NOT**", "**SHOULD**", "**SHOULD NOT**", "**RECOMMENDED**",  "**MAY**", and "**OPTIONAL**" in this document are to be interpreted as described in [@!RFC2119].

{mainmatter}

# Scope

The scope of this specification is focused on describing a discovery document for participants to identify supported functionality at a specific Provider supplied resource server.

# Terms & Definitions

This specification uses the terms "Provider" and "Initiator" as defined by [@!DATARIGHTPLUS-ROSETTA].

# DataRight+ Discovery Metadata

DataRight+ compatible Providers have metadata containing configuration related to the additional services they provide. These DataRight Provider Metadata values are used by participating Initiators.

| Attribute Name | Requirement  | JSON Type | Description                                                                        |
|----------------|--------------|-----------|------------------------------------------------------------------------------------|
| `version`      | **REQUIRED** | String    | The discovery document version. Currently only `V1` is supported.                  |
| `scheme`       | **REQUIRED** | Object    | Per scheme breakdown of support as outlined in [Scheme Metadata](#scheme-metadata) |

## Scheme Metadata

Each scheme represents the key value of the metadata. The currently supported schemes are as follows:

1. `cds`: The operations as outlined in Section 3.2 of [@!PROFILE-AU-CDR]
2. `dio`: The operations as outlined in Section 3.2 of [@!PROFILE-AU-DIO]

For each participating scheme an JSON object is used incorporating the following fields:

| Attribute Name  | Requirement  | JSON Type    | Description                                                                                                    |
|-----------------|--------------|--------------|----------------------------------------------------------------------------------------------------------------|
| `endpoint`      | **REQUIRED** | Object       | An object containing a per operationId key and breakdown as outlined [Operation Metadata](#operation-metadata) |

### Operation Metadata

For each scheme a set of supported endpoints is described whereby the key is the OpenAPI 3 `operationId` and the value is an object containing the following fields:

| Attribute Name     | Requirement                               | JSON Type      | Description                                                                                |
|--------------------|-------------------------------------------|----------------|--------------------------------------------------------------------------------------------|
| `baseUri`          | **REQUIRED** for all schemes except `cds` | String (URI)   | The Base URI to use for this endpoint.                                                     |
| `flags`            | **OPTIONAL**                              | Array (Enum)   | List of flags supported by the endpoint as outlined in [Operation Flags](#operation-flags) |
| `responseVersions` | **REQUIRED**                              | Array (String) | List of Response Versions supported by the endpoint for the specific operation.            |
| `requestVersions`  | **OPTIONAL**                              | Array (String) | List of Request Versions supported by the endpoint for the specific operation              |

### Operation Flags

Each operation may have additional flags to indicate certain support for non-payload related capability. The list of supported flags is as follows:

| Flag                      | Requirement  | Description                                                               |
|---------------------------|--------------|---------------------------------------------------------------------------|
| `SUPPORTS_CDS_VERSIONING` | **OPTIONAL** | Supports the header based version negotiation as specified within [@!CDS] |

## Non-Normative Example

The following is a non-normative example of a DataRight+ Discovery document:

```json
{
  "version": "V1",
  "scheme": {
    "cds": {
      "endpoint": {
        "getBankingAccountDetail": {
          "flags": [
            "SUPPORTS_CDS_VERSIONING"
          ],
          "responseVersions": [
            "V2"
          ]
        }
    },
    "dio": {
      "endpoint": {
        "getBankingTransactionDetailListStatus": {
          "baseUri": "https://secure-api.acme.bank/dio-au/v1",
          "responseVersions": [
            "V1"
          ]
        },
        "requestBankingTransactionDetailList": {
          "baseUri": "https://secure-api.acme.bank/dio-au/v1",
          "responseVersions": [
            "V1"
          ],
          "requestVersions": [
            "V1"
          ]
        },
        "retrieveBankingTransactionDetailList": {
          "baseUri": "https://secure-api.acme.bank/dio-au/v1",
          "responseVersions": [
            "V1"
          ]
        }
      }
    }
  }
}
```

# Provider

The Provider **SHALL** make available at the base uri advertised by the Ecosystem Authority for the Provider Brand `publicBaseUri` attribute, as described further in [@!DATARIGHTPLUS-REDOCLY] the following endpoint:

| Resource Server Endpoint                     | Valid `x-max-v` |
|----------------------------------------------|-----------------|
| `GET /discovery/datarightplus-configuration` | `1`             |

## Maximum Version Support

In order to provide a progressive update mechanism of the metadata itself the Provider:

1. **SHALL** accept a `x-max-v` request header value and use it to provide [DataRight+ Discovery Metadata](#dataright-discovery-metadata) equal to or less than the version specified in `version`
2. **MAY** include in the response an `x-v` header with the same value as the `version` attribute contained within the response payload

# Initiator

The Initiator resource server client **SHALL**:

1. Perform `GET` request to the prescribed endpoint location (i.e. `<publicBaseUri>/discovery/datarightplus-configuration`) and;
2. Include the `x-max-v` header describing the newest Initiator supported version of the [DataRight+ Discovery Metadata](#dataright-discovery-metadata) and;
3. Parse the response in accordance with [DataRight+ Discovery Metadata](#dataright-discovery-metadata)

# Implementation Considerations

For the `cdr` scheme, the `baseUri` value is intended to be provided by the Ecosystem Authority. In this case the `baseUri`, if specified, **MUST** match the relevant value supplied by the Ecosystem Authority.

{backmatter}

<reference anchor="CDS" target="https://consumerdatastandardsaustralia.github.io/standards"><front><title>Consumer Data Standards (CDS)</title><author><organization>Data Standards Body (Treasury)</organization></author></front> </reference>

<reference anchor="PROFILE-AU-CDR" target="https://datarightplus.github.io/datarightplus-cdr-profile/draft-authors-datarightplus-cdr-profile.html"> <front><title>DataRight+: Australian CDR Profile</title><author initials="S." surname="Low" fullname="Stuart Low"><organization>Biza.io</organization></author></front> </reference>

<reference anchor="PROFILE-AU-DIO" target="https://datarightplus.github.io/datarightplus-cdr-profile/draft-authors-datarightplus-dio-profile.html"> <front><title>DataRight+: Australian DataRight+ Profile</title><author initials="S." surname="Low" fullname="Stuart Low"><organization>Biza.io</organization></author></front> </reference>

<reference anchor="DATARIGHTPLUS-ROSETTA" target="https://datarightplus.github.io/datarightplus-rosetta/draft-authors-datarightplus-rosetta.html"> <front><title>DataRight+ Rosetta Stone</title><author initials="S." surname="Low" fullname="Stuart Low"><organization>Biza.io</organization></author></front> </reference>

<reference anchor="DATARIGHTPLUS-REDOCLY" target="https://datarightplus.github.io/datarightplus-redocly"> <front><title>DataRight+: Redocly</title><author initials="S." surname="Low" fullname="Stuart Low"><organization>Biza.io</organization></author><author initials="B." surname="Kolera" fullname="Ben Kolera"><organization>Biza.io</organization></author>
<author initials="W." surname="Cai" fullname="Wei Cai"><organization>Biza.io</organization></author></front> </reference>

