---
title: "Requirements for Adaptive DNS Discovery"
abbrev: "ADDREQ"
docname: draft-box-add-requirements-00
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
resolvers. This document describes one common use case, that of discovering the encrypted DNS resolver that
corresponds to the Do53 resolver offered by a network. It lists requirements that any proposed discovery and
selection mechanisms should address.

--- middle

# Introduction

Several protocols for protecting DNS traffic with encrypted transports have been defined, such as DNS-over-TLS
(DoT) {{?RFC7858}} and DNS-over-HTTPS (DoH) {{?RFC8484}}. Encrypted DNS can provide many security and privacy
benefits for network clients.

While it is possible for clients to statically configure encrypted DNS resolvers to use, dynamic discovery and
provisioning of encrypted resolvers can expand the usefulness and applicability of encrypted DNS to many more use cases.

The Adaptive DNS Discovery (ADD) Working Group is chartered to define mechanisms that allow clients to automatically
discover and select encrypted DNS resolvers in a wide variety of network environments.
This document describes one common use case, that of discovering the encrypted DNS resolver that corresponds to
the Do53 resolver offered by a network. It lists requirements that any proposed discovery and selection mechanisms should address.
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

Equivalent: See {{equivalence}}.


# Use case description

It is often the case that a client possesses no specific configuration for how to operate DNS, and at some point
joins a network that it has no previous knowledge about. In such a case the usual existing behaviour is to
dynamically discover the network's recommended Do53 resolver and use it. This long-standing practice works in
nearly all networks, but presents a number of privacy and security risks that were the motivation for the development
of encrypted DNS.

The network's recommended unencrypted resolver may have a number of properties that differ from a generic resolver.
It may be able to answer names that are not known globally, it may exclude some names (for positive or negative
reasons), and it may provide address answers that have improved proximity. In this use case it is assumed that
the user who chose to join this network would also like to make use of these properties of the network's unencrypted resolver,
at least some of the time. However they would like to use an encrypted DNS protocol rather than Do53.

Using an encrypted and authenticated resolver that is equivalent to the one provisioned by the network
can provide several benefits that are not possible if only unencrypted DNS is used:

- Prevent other devices on the network from observing client DNS messages
- Authenticate that the DNS resolver is the correct one
- Verify that answers come from the selected DNS resolver

To meet this case there should be a means by which the client can learn how to contact an encrypted DNS resolver
that provides essentially the same responses as the ones served by the network's recommended unencrypted resolver.
It is not a requirement that these two resolvers are the same physical or logical machine. Often they will be, but they
could equally be separated, perhaps by hundreds of miles. However it is deployed, the key is that they are equivalent.

{: #equivalence}
## Equivalence 

Equivalence is the property that a specific Encrypted DNS resolver provides the same answers, or similar answers
with the *same* resulting behavior, as those of the locally recommended Do53 resolver. This implies a necessary precondition
is that they are operated by the same administrative domain. This administrator has the task of keeping them aligned.

There are two possible ways to claim equivalence.

* The local network can claim that one or more encrypted DNS resolvers are equivalent to the Do53 resolver it has offered. This is known as network-identified.
* During communication with the (generally unencrypted) resolver, this resolver can claim that one or more encrypted DNS resolvers are equivalent. This is known as resolver-identified.

The former is preferred since it comes from the same source of information, and removes the need to talk to the Do53 resolver
at all. However it cannot be the sole mechanism, at least for several years, since there is a large installed base of local
network equipment that is difficult to upgrade with new features. Hence the second mechanism must support being able to
announce an equivalent resolver using only existing widely-deployed DNS features.

## Verification of equivalence

For resolver-identified claims, it is desirable that there is a method of verifying that the claimed-equivalent Encrypted DNS resolver
is operated by the same administrative domain as the Do53 resolver. In the subset of cases
where the two operate at the same IP address, the equivalence can be more easily determined.

For network-identified encrypted resolvers, at present the discovery process needs to accept the client's
existing trust of unauthenticated network information, assume that it is valid, and proceed from there.
When better methods of authenticating the network are available, this can be revisited.


## Local addressing

Many networks offer a Do53 resolver on an address that is not globally meaningful, e.g. {{?RFC1918}}, link-local
or unique local addresses. To support the discovery of Encrypted DNS in these environments, a means is needed for
the discovery process to work from a locally-addressed Do53 resolver to an Encrypted DNS resolver that is accessible
either at the same (local) address, or at a different global address. Both options need to be supported.

An encrypted DNS resolver can offer a TLS certificate proving ownership of a name, but it is important to note that
this name (or any associated address) is often not sufficient to prove that an Encrypted DNS resolver is equivalent
to a Do53 resolver.

# Network-identified encrypted resolvers

DNS servers are often provisioned by a network as part of DHCP options {{?RFC2132}},
IPv6 Router Advertisement (RA) options {{?RFC8106}}, Point-to-Point Protocol (PPP) {{?RFC1877}}, or
3GPP Protocol Configuration Options (TS24.008). Historically this is usually one or more Do53 resolver IP addresses,
to be used for traditional unencrypted DNS.

A solution is required that enhances the set of information delivered to include details of one or more
equivalent encrypted DNS resolvers.

# Resolver-identified encrypted resolvers

To support cases where the network is unable to identify an encrypted resolver, it should be possible to learn
the details of one or more equivalent encrypted DNS resolvers by communicating with the network-recommended
unencrypted Do53 resolver. This should involve an exchange that uses standard DNS messages that can be
handled, or forwarded, by existing deployed software.

It is frequently the case that Do53 resolvers announced by networks are difficult to upgrade to support encrypted operation.
In such cases it is likely that the only option for encrypted operation is to refer to a separate globally-addressed
encrypted DNS resolver. It is incumbent on the operator of the unencrypted local resolver to ensure that this referral
is equivalent.

If the local resolver has been upgraded to support encrypted DNS, the client may not initially be aware that its local resolver
supports it. Discovering this may require communication with the local resolver, or an upstream resolver, over Do53.
Once discovered, the local encrypted resolver may be selected by the client, gaining the benefits of encryption
while retaining the benefits of a local caching resolver with knowledge of the local topology.

An additional benefit of using a local resolver occurs with IoT devices. A common usage pattern for such devices
is for it to "call home" to a service that resides on the public Internet, where that service is referenced through a
domain name. As discussed in Manufacturer Usage Description Specification {{?RFC8520}}, because these devices
tend to require access to very few sites, all other access should be considered suspect. However, if the query
is not accessible for inspection, it becomes quite difficult for the infrastructure to suspect anything.

## Opportunistic discovery by well-known port

Some encrypted DNS protocols (DNS-over-TLS {{?RFC7858}} and DNS-over-QUIC {{?I-D.ietf-dprive-dnsoquic}}) already
support automatic discovery by probing the well-known port at the same IP address as the unencrypted resolver.
This could form part of the solution for upgrading to an encrypted local resolver, but is not a complete solution.

# Privacy and security requirements {#priv-sec}

Encrypted (and authenticated) DNS improves the privacy and security of DNS queries and answers in the presence of malicious
attackers. Such attackers are assumed to interfere with or otherwise impede DNS traffic and corresponding
discovery mechanisms. They may be on-path or off-path between the client and entities with which the client
communicates {{?RFC3552}}. These attackers can inject, tamper, or otherwise interfere with traffic as needed.
Given these capabilities, an attacker may have a variety of goals, including, though not limited to:

- Monitor and profile clients by observing unencrypted DNS traffic

- Modify unencrypted DNS traffic to filter or augment the user experience

- Block encrypted DNS

In general clients cannot assume that their network does not have such an attacker unless given some means of authenticating
or otherwise trusting the communication with their DNS resolver.

Given this type of attacker, resolver discovery mechanisms must be designed carefully to not worsen a client's security or
privacy posture. In particular, attackers under consideration must not be able to:

- Redirect secure DNS traffic to themselves when they would not otherwise handle DNS traffic.

- Override or interfere with the resolver preferences of a user or administrator.

- Cause clients to use a discovered resolver which has no authenticated delegation from a client-known entity.

- Influence automatic discovery mechanisms such that a client uses one or more resolvers that are not
otherwise involved with providing service to the client, such as: a network provider, a VPN server, a
content provider being accessed, or a server that the client has manually configured.

When discovering DNS resolvers on a local network, clients have no mechanism to distinguish between cases 
where an active attacker with the above capabilities is interfering with discovery, and situations wherein 
the network has no encrypted resolver. Absent such a mechanism, an attacker can always succeed in these
goals. Therefore, in such circumstances, viable solutions for local DNS resolver discovery should consider 
weaker attackers, such as those with only passive eavesdropping capabilities. It is unknown whether such
relaxations represent a realistic attacker in practice. Thus, local discovery solutions designed around
this threat model may have limited value.

Beyond these requirements, standards describing resolver discovery mechanisms must not place any requirements
on clients to select particular resolvers over others.

## Handling exceptions and failures

Even with encrypted DNS resolver discovery in place, clients must be prepared to handle certain scenarios where encrypted DNS
cannot be used. In these scenarios, clients must consider if it is appropriate to fail open by sending the DNS queries without
encryption, fail closed by not doing so, or presenting a choice to a user or administrator. The exact behavior is a
local client policy decision.

Some networks that use Captive Portals will not allow any Internet connectivity until a client has interacted with the portal
{{?I-D.ietf-capport-architecture}}. If these networks do not use encrypted DNS for their own resolution, a client will need to
perform unencrypted DNS queries in order to get out of captivity. Many operating systems have specific client code responsible
for detecting and interacting with Captive Portals; these system components may be good candidates for failing open, since they
do not generally represent user traffic.

Other networks may not allow any use of encrypted DNS, or any use of encrypted DNS to resolvers other than a network-provisioned
resolver. Clients should not silently fail open in these cases, but if these networks are trusted by or administered by the user,
the user may want to specifically follow the network's DNS policy instead of what the client would do on an unknown or untrusted
network.


# Statement of Requirements {#requirements-summary}

This section lists requirements that flow from the above sections.

| Requirement | Description |
| R1.1 | Discovery MUST provide a client the ability to learn the set of, or absence of, equivalent resolvers from any instance of a resolver. |
| R1.2 | Discovery MUST support DNS-over-TLS {{?RFC7858}}. |
| R1.3 | Discovery MUST support DNS-over-HTTPS {{?RFC8484}}. |
| R1.4 | Discovery SHOULD support working-group adopted drafts for encrypted DNS, such as DNS-over-QUIC {{?I-D.ietf-dprive-dnsoquic}}. |
| R1.5 | In an ISP network the upstream ISP MUST be able to announce the support of encrypted DNS service and this information MUST be available via a query sent to a local network forwarder. |
| R1.6 | This discovery MUST NOT require software features that cannot be found in existing widely deployed DNS forwarders. |
| R2.1 | Networks using MUST be able to announce one or more equivalent encrypted DNS resolvers using existing mechanisms such as DHCP, IPv6 Router Advertisement, and the Point-to-Point Protocol. The format for resolver information MUST be specified such that provisioning mechanisms defined outside of the IETF can advertise encrypted DNS resolvers. |
| R3.1 | When discovery is instantiated from a resolver, that resolver MAY be encrypted or not. |
| R3.2 | When discovery is instantiated from a resolver, that resolver MAY be locally or globally reachable. Both options MUST be supported. |
| R3.3 | Discovery of an encrypted resolver identified by another resolver SHOULD obtain some evidence that the two resolvers are operated by the same administrative domain. |
| R4.1 | Discovery MUST NOT worsen a client's security or privacy posture. |
| R4.2 | Threat modelling MUST assume that there is a passive eavesdropping attacker on the local network. |
| R4.3 | Threat modelling MUST assume that this attacker can actively attack from outside the local network. |
| R4.4 | Attackers MUST NOT be able to redirect secure DNS traffic to themselves when they would not otherwise handle DNS traffic. |
| R4.5 | Attackers MUST NOT be able to override or interfere with the resolver preferences of a user or administrator. |
| R4.6 | Attackers MUST NOT be able to cause clients to use a discovered resolver which has no authenticated delegation from a client-known entity. |
| R4.7 | Attackers MUST NOT be able to influence automatic discovery mechanisms such that a client uses one or more resolvers that are not otherwise involved with providing service to the client, including a network provider, a VPN server, a content provider being accessed, or a server that the client has manually configured. |
| R4.8 | Discovery MUST NOT place any requirements on clients to select particular resolvers over others. |

# Security Considerations

See {{priv-sec}}.


# IANA Considerations

This document has no IANA actions.



--- back

# Acknowledgments
{:numbered="false"}

This document was started based on discussion during the ADD meeting of IETF108, the subsequent interims,
on the list, and with text from draft-pauly-add-requirements. In particular this document was informed by contributions
from Martin Thomson, Eric Rescorla, Tommy Jensen, Ben Schwartz, Paul Hoffman, Ralf Weber, Michael Richardson,
Mohamed Boucadair, Sanjay Mishra, Jim Reid, Neil Cook, Nic Leymann and Andrew Campling.




