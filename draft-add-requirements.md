---
title: "Requirements for Discovering Designated Resolvers"
abbrev: "ADDREQ"
docname: draft-ietf-add-requirements-00
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
    org: BT
    street: 2000 Park Avenue
    city: Bristol
    country: UK
    email: chris.box@bt.com
  -
    ins: T. Pauly
    name: Tommy Pauly
    org: Apple
    street: One Apple Park Way
    city: Cupertino, California 95014
    country: United States of America
    email: tpauly@apple.com
  -
    ins: C. A. Wood
    name: Christopher A. Wood
    org: Cloudflare
    street: 101 Townsend St
    city: San Francisco
    country: United States of America
    email: caw@heapingbits.net
  -
    ins: T. Reddy
    name: Tirumaleswar Reddy
    org: McAfee
    street: Embassy Golf Link Business Park
    city: Bangalore
    country: India
    email: TirumaleswarReddy_Konda@McAfee.com
  -
    ins: D. Migault
    name: Daniel Migault
    org: Ericsson
    street: 8275 Trans Canada Route
    city: Saint Laurent, QC
    country: Canada
    email: daniel.migault@ericsson.com


normative:
  RFC2119:

informative:



--- abstract

Adaptive DNS Discovery is chartered to define mechanisms that allow clients to discover and select encrypted DNS
resolvers. This document describes one common use case, namely that of clients that connect to a network but where they
cannot securely authenticate the identity of that network. In such cases the client would like to learn which
encrypted DNS resolvers are designated by that network or by the Do53 resolver offered by that network.
It lists requirements that any proposed discovery mechanisms should seek to address.

--- middle

# Introduction

Several protocols for protecting DNS traffic with encrypted transports have been defined, such as DNS-over-TLS
(DoT) {{?RFC7858}} and DNS-over-HTTPS (DoH) {{?RFC8484}}. Encrypted DNS can provide many security and privacy
benefits for network clients.

While it is possible for clients to statically configure encrypted DNS resolvers to use, dynamic discovery and
provisioning of encrypted resolvers can expand the usefulness and applicability of encrypted DNS to many more use cases.

The Adaptive DNS Discovery (ADD) Working Group is chartered to define mechanisms that allow clients to automatically
discover and select encrypted DNS resolvers in a wide variety of network environments. This document describes one common
use case, namely that of clients that connect to a network but where they cannot securely authenticate
that network. Whether the network required credentials before the client was permitted to join is irrelevant; the client
still cannot be sure that it has connected to the network it was expecting.

In such cases the client would like to learn which encrypted DNS resolvers are designated by that network, or by the Do53
resolver offered by that network. It lists requirements that any proposed discovery mechanisms should seek to address.
They can do this either by providing a solution, or by explicitly stating why it is not in scope.


## Requirements language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in BCP 14 {{RFC2119}} {{!RFC8174}}
when, and only when, they appear in all capitals, as shown here.

# Terminology

This document makes use of the following terms.

Encrypted DNS: DNS-over-HTTPS {{?RFC8484}}, DNS-over-TLS {{?RFC7858}}, or any other encrypted DNS technology that
the IETF may publish, such as DNS-over-QUIC {{?I-D.ietf-dprive-dnsoquic}}.

Do53: Unencrypted DNS over UDP port 53, or TCP port 53 {{?RFC1035}}.

Designated: See {{designation}}.

Designator: The network or resolver that issued the designation.


# Use case description

It is often the case that a client possesses no specific configuration for how to operate DNS, and at some point
joins a network that it cannot authenticate. It may have no prior knowledge of the network, or it may have connected
previously to a network that looked the same. In either case the usual behaviour, because of lack of specific
configuration, is to dynamically discover the network's designated Do53 resolver and use it. This long-standing practice
works in nearly all networks, but presents a number of privacy and security risks that were the motivation for the
development of encrypted DNS.

The network's designated Do53 resolver may have a number of properties that differ from a generic resolver.
It may be able to answer names that are not known globally, it may exclude some names (for positive or negative
reasons), and it may provide address answers that have improved proximity. In this use case it is assumed that
the user who chose to join this network would also like to make use of these properties of the network's unencrypted resolver,
at least some of the time. However they would like to use an encrypted DNS protocol rather than Do53.

Using an encrypted and authenticated resolver can provide several benefits that are not possible if only unencrypted DNS is used:

- Prevent other devices on the network from observing client DNS messages
- Authenticate that the DNS resolver is the correct one
- Verify that answers come from the selected DNS resolver

To meet this case there should be a means by which the client can learn how to contact a set of encrypted DNS resolvers that
are designated by the network it has joined.


{: #designation}
## Designation 

Designation is the process by which a local network or a resolver can point clients towards a particular set of resolvers.
This is not a new concept, as networks have been able to dynamically designate Do53 resolvers for decades (see {{network}}).
However here we extend the concept in two ways:

* To allow resolvers to designate other resolvers
* The inclusion of support for encrypted DNS

The designated set could be empty, or it could list the contact details (such as DoH URI Template) of DNS resolvers that it recommends.
It is not required that there be any relationship between the resolvers in the set, simply that all of them are options that the
designator asserts are safe and appropriate for the client to use without user intervention.

There are two possible sources of designation.

* The local network can designate one or more encrypted DNS resolvers (B, C, etc) in addition to any Do53 resolver (A) it may offer. This is known as network-identified.
* During communication with the (often unencrypted) resolver (A), this resolver can designate one or more encrypted DNS resolvers (B, C, etc). This is known as resolver-identified.

Network-identified has the advantages that it derives from the same source of information as the network's Do53 announcement, and
removes the need to talk to the Do53 resolver at all. However it cannot be the sole mechanism, at least for several years, since
there is a large installed base of local network equipment that is difficult to upgrade with new features. Hence the second mechanism
should support being able to designate resolvers using only existing widely-deployed DNS features.

## Local addressing

Many networks offer a Do53 resolver on an address that is not globally meaningful, e.g. {{?RFC1918}}, link-local
or unique local addresses. To support the discovery of encrypted DNS in these environments, a means is needed for
the discovery process to work from a locally-addressed Do53 resolver to an encrypted DNS resolver that is accessible
either at the same (local) address, or at a different global address. Both options need to be supported.

## Use of designation information

After the client receives designation information, it must come to a decision on whether and when to use any of the
designated resolvers. 

In the case of resolver-identified designation, it would be advantageous for a solution to enable the client to
validate the source of the assertion in some way. For example it may be possible to verify that the designation
comes from an entity who already has full control of the client's Do53 queries. Network-identified designation should
not require this, unless the network-identified resolver in turn initiated a new resolver-identified designation.
It would be beneficial to extend such a verification process to defend against attackers that have only transient
control of such queries.

Clients may also seek to validate the identity of the designated resolver, beyond what is required by the relevant
protocol. Authors of solution specifications should be aware that clients may impose arbitrary additional
requirements and heuristics as they see fit.

{: #network}
## Network-identified designated resolvers

DNS servers are often provisioned by a network as part of DHCP options {{?RFC2132}},
IPv6 Router Advertisement (RA) options {{?RFC8106}}, Point-to-Point Protocol (PPP) {{?RFC1877}}, or
3GPP Protocol Configuration Options (TS24.008). Historically this is usually one or more Do53 resolver IP addresses,
to be used for traditional unencrypted DNS.

A solution is required that enhances the set of information delivered to include details of one or more
designated encrypted DNS resolvers, or states that there are none. Such resolvers could be on the local network, somewhere
upstream, or on the public Internet.

## Resolver-identified designated resolvers

To support cases where the network is unable to identify an encrypted resolver, it should be possible to learn
the details of one or more designated encrypted DNS resolvers by communicating with the network's designated
Do53 resolver. This should involve an exchange that uses standard DNS messages that can be
handled, or forwarded, by existing deployed software.

Each resolver in the set may be at a different network location, which leads to several subcases for the mapping from Do53
to a particular designated resolver.

### Local to local

If the local resolver has been upgraded to support encrypted DNS, the client may not initially be aware that its local resolver
supports it. Discovering this may require communication with the local resolver, or an upstream resolver, over Do53.
Clients that choose to use this local encrypted DNS gain the benefits of encryption while retaining the benefits
of a local caching resolver with knowledge of the local topology.

Clients will be aware when the designated resolver has the same IP address as the Do53 (after looking up its name if required). They
can use this information in their decision-making as to the level of trust to place in the designated resolver.
In some networks it will not be possible to deploy encrypted DNS on the same IP address, e.g. because of the increased
resource requirements of encrypted DNS. Discovery solutions should work in the presence of a change to a different
local IP address.

An additional benefit of using a local resolver occurs with IoT devices. A common usage pattern for such devices
is for it to "call home" to a service that resides on the public Internet, where that service is referenced through a
domain name. As discussed in Manufacturer Usage Description Specification {{?RFC8520}}, because these devices
tend to require access to very few sites, all other access should be considered suspect. However, if the query
is not accessible for inspection, it becomes quite difficult for the infrastructure to suspect anything.

### Local to upstream

It is frequently the case that Do53 resolvers announced by home networks are difficult to upgrade to support encrypted operation.
In such cases it is possible that the only option for encrypted operation is to refer to a separate globally-addressed
encrypted DNS resolver, somewhere upstream. Other networks may choose deploy their encrypted DNS resolver away from the
local network, for other reasons.

The use of an upstream resolver can mean the loss of local knowledge, such as the ability to respond to queries for
locally-relevant names. Solutions should consider how to guide clients when to direct their queries to the local Do53.
For example this could be through pre-emptive communication ("if you ever need to query *.example.com, use your local Do53"),
or reactively ("I don't know the answer to that, but your local Do53 should know").

### Public to public

In cases where the local network has designated a Do53 resolver on the public Internet, this resolver may designate its
own or another public encrypted DNS service. Since public IP addresses may appear in TLS certificates, solutions may
use this as one way to validate that the designated encrypted resolver is legitimately associated with the original Do53.

## Identification over an encrypted channel

In cases where the designation is delivered over an authenticated and encrypted channel, such as when one encrypted DNS
resolver designates another, one form of attack is removed. Specifically, clients may be more confident that the received
designation was actually sent by the designator. Clients may take this into account when deciding whether to follow the
designation.


# Privacy and security requirements {#priv-sec}

Encrypted (and authenticated) DNS improves the privacy and security of DNS queries and answers in the presence of malicious
attackers. Such attackers are assumed to interfere with or otherwise impede DNS traffic and corresponding
discovery mechanisms. They may be on-path or off-path between the client and entities with which the client
communicates {{?RFC3552}}. These attackers can inject, tamper, or otherwise interfere with traffic as needed.
Given these capabilities, an attacker may have a variety of goals, including, though not limited to:

- Monitor and profile clients by observing unencrypted DNS traffic

- Modify unencrypted DNS traffic to filter or augment the user experience

- Block encrypted DNS

Given this type of attacker, resolver discovery mechanisms must be designed carefully to not worsen a client's security or
privacy posture. In particular, attackers under consideration must not be able to:

- Redirect secure DNS traffic to themselves when they would not otherwise handle DNS traffic.

- Override or interfere with the resolver preferences of a user or administrator.

- Cause clients to use a discovered resolver which has no designation from a client-known entity.

When discovering DNS resolvers on a local network, clients have no mechanism to distinguish between cases 
where an active attacker with the above capabilities is interfering with discovery, and situations wherein 
the network has no encrypted resolver. Absent such a mechanism, an attacker can always succeed in these
goals. Therefore, in such circumstances, viable solutions for local DNS resolver discovery should consider 
weaker attackers, such as those with only passive eavesdropping capabilities. It is unknown whether such
relaxations represent a realistic attacker in practice. Thus, local discovery solutions designed around
this threat model may have limited value.


# Statement of Requirements {#requirements-summary}

This section lists requirements that flow from the above sections.

| Requirement | Description |
| R1.1 | Discovery SHOULD provide a local network the ability to announce to clients a set of, or absence of, designated resolvers. |
| R1.2 | Discovery SHOULD provide a resolver the ability to announce to clients a set of, or absence of, designated resolvers. |
| R1.3 | Discovery SHOULD support all encrypted DNS protocols standardised by the IETF. |
| R2.1 | Networks SHOULD be able to announce one or more designated encrypted DNS resolvers using existing mechanisms such as DHCPv4, DHCPv6, IPv6 Router Advertisement, and the Point-to-Point Protocol. |
| R2.2 | The format for resolver designation SHOULD be specified such that provisioning mechanisms defined outside of the IETF can advertise encrypted DNS resolvers. |
| R2.3 | This format SHOULD convey, at minimum, the information the client needs to make contact with each designated resolver. |
| R2.4 | This format MAY convey additional resolver information. |
| R3.1 | In resolver-identified designation (R1.2), the communication with the designator MAY be encrypted or not, depending on the capability of the resolver. |
| R3.2 | In resolver-identified designation (R1.2), that resolver MAY be locally or globally reachable. Both options SHOULD be supported. |
| R4.1 | If the local network resolver is a forwarder that does not offer encrypted DNS service, an upstream encrypted resolver SHOULD be retrievable via queries sent to that forwarder. |
| R4.2 | Achieving requirement 4.1 SHOULD NOT require any changes to DNS forwarders hosted on non-upgradable legacy network devices. |
| R5.1 | Discovery MUST NOT worsen a client's security or privacy posture. |
| R5.2 | Threat modelling MUST assume that there is a passive eavesdropping attacker on the local network. |
| R5.3 | Threat modelling MUST assume that an attacker can actively attack from outside the local network. |
| R5.4 | Attackers MUST NOT be able to redirect encrypted DNS traffic to themselves when they would not otherwise handle DNS traffic. |

# Security Considerations

See {{priv-sec}}.


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

This document was started based on discussion during the ADD meeting of IETF108, subsequent meetings,
on the list, and with text from draft-pauly-add-requirements. In particular this document was informed by contributions
from Martin Thomson, Eric Rescorla, Tommy Jensen, Ben Schwartz, Paul Hoffman, Ralf Weber, Michael Richardson,
Mohamed Boucadair, Sanjay Mishra, Jim Reid, Neil Cook, Nic Leymann, Andrew Campling, Eric Orth, Ted Hardie,
Paul Vixie, Vittorio Bertola, and Vinny Parla.
