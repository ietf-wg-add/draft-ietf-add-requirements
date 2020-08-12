---
title: "Requirements for Adaptive DNS Discovery"
abbrev: "ADDREQ"
docname: draft-add-requirements-latest
category: info

ipr: trust200902
area: Internet
workgroup: ADD
keyword: Internet-Draft

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:
 -
    ins: C. Box
    name: Chris Box
    organization: BT
    street: 2000 Park Avenue
    city: Bristol
    country: UK
    email: chris.box@bt.com

normative:
  RFC2119:

informative:



--- abstract

Adaptive DNS Discovery is chartered to define mechanisms that allow clients to discover and select encrypted DNS resolvers.
This document seeks to provide a set of use cases and requirements that proposed mechanisms should address.

--- middle

# Introduction

The Adaptive DNS Discovery (ADD) Working Group is chartered to define mechanisms that allow clients to discover and select
encrypted DNS resolvers in a wide variety of network environments. This document seeks to provide a set of use cases and
requirements that proposed mechanisms should address. They can do this either by providing a solution, or by
explicitly stating why it is not in scope.

Use cases are described between {{first-usecase}} and {{last-usecase}}.
Each use case contains a narrative and a set of requirements that apply in that case.
Each is identified as "Ra.b" where a is the requirement group number and b is the number within that group.
Both a and b are integers starting with 1. A summary of all requirements in listed in {{requirements-summary}}.

# Conventions and Definitions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in BCP 14 {{RFC2119}} {{!RFC8174}}
when, and only when, they appear in all capitals, as shown here.

# Discovery of associated resolvers {#first-usecase}

A client may begin with information from the attached networks, and/or resolvers known from configuration.
This information may be used to then discover one or more associated or equivalent encrypted resolvers.
These are expected to be operated by the same entity that provides the resolver the client started with, but
may not have identical behaviour. For example one entity may offer a set of encrypted resolvers with different levels of
filtering or proximity.

| Requirement | Description |
| R1.1 | There must be a mechanism for a client to learn the set of encrypted resolvers that are associated with a resolver that is known only by its IP address. |
| R1.2 | Discovery must be possible even when the IP address is only valid locally. |
| R1.3 | More to be added |

## Discover resolvers associated with one declared by an attached network

During network attach, networks frequently announce a resolver that they recommend clients should use.
This could be through DHCP, RA, PPP, PCO, or another mechanism. Historically this is usually a Do53 resolver,
declared simply by its IP address. However it could also be a richer set of information.

| Requirement | Description |
| R2.1 | Example requirement |

### Unencrypted forwarder

If the resolver announced by the network is a classic unencrypted forwarder, it is frequently the case that such forwarders are difficult to upgrade
to support encrypted operation. In such cases it is useful for the resolver provider to be able to declare which encrypted resolvers they provide,
and for the client to be able to discover them. If the client wishes to, it can then use one of those resolvers and bypass the local forwarder.

| Requirement | Description |
| R3.1 | Example requirement |

### Encrypted forwarder

If a subset of local resolvers supports encrypted DNS, the client may not initially be aware that its local resolver supports it. Discovering this
may require communication with the local resolver, or an upstream resolver, over an unencrypted transport. Once discovered, the local encrypted forwarder
may be selected by the client, gaining the benefits of encryption while retaining the benefits of a local caching forwarder with knowledge of the local
topology.

| Requirement | Description |
| R4.1 | Example requirement |

## Discover resolvers associated with a manually-configured resolver

A client with manual configuration directing it to use a particular resolver will generally use that resolver. However if that communication is
unencrypted, it is beneficial for the client to learn which associated resolvers are offered by the same entity and provide the same semantics.

| Requirement | Description |
| R5.1 | Example requirement |

## Discover resolvers associated with a VPN-configurated resolver

Narrative required.

| Requirement | Description |
| R6.1 | Example requirement |


# Discovery of limited domain resolvers

Narrative required.

| Requirement | Description |
| R7.1 | Example requirement |

## Discover a mapping between a locally-hosted domain and a resolver

Narrative required.

| Requirement | Description |
| R8.1 | Example requirement |

### Walled garden content

Narrative required.

| Requirement | Description |
| R9.1 | Example requirement |

### Locally-cached content

Narrative required.

| Requirement | Description |
| R10.1 | Example requirement |

### Private enterprise names

Narrative required.

| Requirement | Description |
| R11.1 | Example requirement |

## Discover a mapping between a public domain and a resolver {#last-usecase}

Narrative required.

| Requirement | Description |
| R12.1 | Example requirement |


# Requirements Summary {#requirements-summary}

This sections lists the complete set of requirements described above, for ease of reference.

| Requirement | Description |
| R1.1 | There must be a mechanism for a client to learn the set of encrypted resolvers that are associated with a resolver that is known only by its IP address. |
| R1.2 | Discovery must be possible even when the IP address is only valid locally. |
| R1.3 | More to be added |
| R2.1 | Example requirement |
| R3.1 | Example requirement |



# Security Considerations

All security considerations relevant to a particular use case are described under that section.
Additional consideration common to all of them are described here.


# IANA Considerations

This document has no IANA actions.



--- back

# Acknowledgments
{:numbered="false"}

This document was started based on contributions during the ADD meeting of IETF108, and on the list (TODO: add names).

More authors are required! Please advise if you want to be one. Alternatively contribute on the list,
start a github issue or submit a pull request.

