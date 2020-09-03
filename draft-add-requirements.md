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

normative:
  RFC2119:

informative:



--- abstract

Adaptive DNS Discovery is chartered to define mechanisms that allow clients to discover and select encrypted DNS resolvers.
This document describes several use cases for discovering DNS resolvers that support encrypted transports,
and lists requirements that any proposed discovery and selection mechanisms should address.

--- middle

# Introduction

Several protocols for protecting DNS traffic with encrypted transports have been defined,
such as DNS-over-TLS (DoT) {{?RFC7858}} and DNS-over-HTTPS (DoH) {{?RFC8484}}.
Encrypted DNS can provide many security and privacy benefits for network clients.

While it is possible for clients to hard-code encrypted DNS resolvers to use, dynamic
discovery and provisioning of encrypted resolvers can expand the usefulness and
applicability of encrypted DNS to many more use cases.

The Adaptive DNS Discovery (ADD) Working Group is chartered to define mechanisms that allow clients to automatically discover
and select encrypted DNS resolvers in a wide variety of network environments. This document describes several use cases
for discovering DNS resolvers that support encrypted transports, and lists requirements that any proposed discovery and
selection mechanisms should address. They can do this either by providing a solution, or by explicitly stating why it
is not in scope.

Use cases are structured as follows:
Section {{associated}} describes use cases where discovery of resolvers is performed via initial resolver that has been configured. 
The motivation is essentially to leverage the existing provisioning of unencrypted resolvers and to enable DNS clients to upgrade to encrypted resolvers. 
Such resolvers are likely associated to the initially configured/provisioned resolver and as such belong to a given administrative domain which limits the horizon of the discovery. 

Section {{direct}} describes use cases where the discovery of a set of resolvers is performed directly, that is without the setting of a initial resolver ( encyrpted or unencrypted). Such discovery may result from configuration or performed opportunistically or may involve new signaling. 

The process of discovering consists in collecting multiple instances of resolvers, that will go though a selection process.
The selection process is not in scope of this document and the selection may result in the selection of a single or multiple resolvers.
However, the selection can only occurs when appropriated information is collected. Base don the use cases, the section {{ information }} describes appropriated information that needs to be associated to the resolvers.

<!--
Use cases are described between {{first-usecase}} and {{last-usecase}}.
Each use case contains a narrative and a set of requirements that apply in that case.
There are additional common requirements in {{priv-sec}}.
Each requirement is identified as "Ra.b" where a is the group number and b is the number within that group.
Both a and b are integers starting with 1.
-->

A summary of all requirements in listed in {{requirements-summary}}.

# Conventions and Definitions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in BCP 14 {{RFC2119}} {{!RFC8174}}
when, and only when, they appear in all capitals, as shown here.

<!-- {: #first-usecase}-->

# Discovery of associated resolvers {#associated}
A client may begin with information from the attached networks, and/or resolvers known from configuration.
This information may be used to then discover one or more associated encrypted resolvers.

Associated resolvers are defined as resolvers operated by the same entity or administrative domain that provides the resolver the client started with.
Such associated resolvers may come in two forms:

<!-- I do not see the need to specify the resolver is unencrypted. It seesm to me that Equivalent and Alternative applies to encrypted resolvers as well.
It is also difficult to define that responses are "identical"
-->
1. Equivalent - resolver a is equivalent to resolver B if an only if DNS responses received from resolver A and resolver B have the same behavior.
2. Alternative - these serve different responses to some queries. For example one entity may offer a set of encrypted resolvers with different levels of filtering (none, just malware, or malware & adult content), or different proximity (local or central).

The client may wish to select one or multiple equivalent associated resolver, and / or select one or multiple of the alternatives.

<!-- it seems to me that providing directly the encrypted version with new signaling is a bit outside teh purpose of discovering alternate resolvers from an existing one. 

Designs for resolver upgrade mechanisms can either add new parameters to existing provisioning mechanisms
(for example, adding necessary information to use DoT or DoH to options in DHCP, RAs, or IKEv2) or else provide a way
to communicate with a provisioned unencrypted DNS resolver and discover the associated encrypted DNS resolvers.
-->


<!--
I suggest the following additional requirements:

I suggested R1.1 also considered the discovery of resolvers or absence of of these resolvers.
"associated" is reflexive, symmetric and transitive, so the discovery should apply in my opinion with any instance of the class, and in our case encrypted or unencrypted resolver.
Maybe we can also turn the existence of the mechanisms into a property the discovery mechanisms ( or protocol) has.
So here are the changes I propose on R1.1

OLD:
There must be a mechanism for a client to learn the set of encrypted resolvers that are associated with an unencrypted resolver.

NEW:
Discovery MUST provide a client to learn the set or absence of associated from any instance of resolver.

I see R1.2 as reflecting the fact that the information that initiate the discover may beof local scope information. I think we may not restrict the information to a local IP address but to any local information that may include a local scope name ( home.arpa) for example.
I am also not quite sure R1.2 has a nit with encrypted resolver. So unless I am reading R1.2 in a wrong way I would write is as:

R1.2: When Discovery is instantiated from an resolver that resolver MAY be encrypted or not and MAY be globally or locally reachable.
| R1.1 | There must be a mechanism for a client to learn the set of encrypted resolvers that are associated with an unencrypted resolver. |
| R1.2 | Discovery must be possible even when the IP address of the encrypted resolver is only valid locally. |
| R1.3 | The discovery MAY be triggered by unsecured existing and well deployed information, but information related to the associated resolvers MUST be authenticated.    |
-->

The discovery from a initial resolver instance is impacted by:
* the channel by which that initial instance has been configured. Typically, this instance may have been configured via a trusted and secure channel (IKEv2) or via unsecured means such as DHCP, RA for example. 
* the initial resolver may support unencrypted DNS and/or encrypted DNS. Note that the current state of the art does not consider the case where only encrypted DNS is supported.
* the initial resolver MAY be reachable via a local or global IP address.
* the initial resolver MAY have a limited scope. It may only serve certain DNS clients with certain IP addresses, as well as certain FQDN. 

The various use cases combines these limitations which should not prevent the discovery of associated to be performed. 

The discovery of associated resolvers comes with the following generic requirements:

| Requirement | Description |
| R1.1. | Discovery MUST provide a client to learn the set or absence of associated resolver from any instance of resolver. |
| R1.2 | Discovery of associated resolver MUST provides some bounding evidence between at least the two associated resolvers |
| R1.3  | When Discovery is instantiated from an resolver, that resolver MAY be encrypted or not and MAY be globally or locally reachable. |
| R1.4 | When multiple associated resolvers are provided, the operator SHOULD be able to provide a preference on which resolvers are preferred |
| R 1.5 | the client MUST be able to determine the scope as well as necessary connectivity parameters associated to a specific resolver.  |
| R.1.6 | the presence of specific services associated to a resolver SHOULD be indicated by the operator |

## Network-provisioned resolvers {#local-network}

DNS servers are often provisioned by a network as part of DHCP options {{?RFC2132}},
IPv6 Router Advertisement (RA) options {{?RFC8106}}, Point-to-Point Protocol (PPP) {{?RFC1877}},
3GPP Protocol Configuration Options, or another mechanism. Historically this is usually one or
more DNS resolver IP addresses, to be used for traditional unencrypted DNS. However it could also
be a richer set of information.

Using an encrypted and authenticated resolver that is associated to the one provisioned by the network
can provide several benefits that are not possible if only unencrypted DNS is used:

<!--
I understand that authenticate that the DNS resolver.... is performed during the TLS handshake while DNS answers are coming after the handshake. Maybe it might ease the reading to place in a different order. At least, bullet 2 and 3 may be exchanged.
-->

- Prevent other devices on the network from observing client DNS messages
- Authenticate that the DNS resolver is the one provisioned by the network
- Verify that answers come from the selected DNS resolver

### Homenet and private enterprise name use case

Private networks such as enterprises or home networks generally provide resolution services that handle both the resolutions of the global DNS as well as resolution of internal names. 

From a client perspective it is hard to determine whether the resolving service is instantiate by a resolver or a forwarder. 
In the case of a resolver, upon receiving a DNS request, the resolver will perform the resolution on the internal domain or on the global internet. 
In the case of a forwarder, the request may be forwarded to resolver and as such work as an intermediary node. 

It is also important to note that the use of an encrypted DNS with a forwarder of the resolver traffic upstream to the forwarder of resolver is likely to be in clear text. 
Frequently, network-provisioned resolvers are forwarders running on a local router. The discovered
encrypted resolvers in these cases may either be local forwarders themselves, or an associated
resolver that is in the network (thus bypassing the router's DNS forwarder).
These site resolvers are mandatory to resolve internal names such as internal.example.com, .local, home.arpa.... 

<!-- I am leaving the section title unencrypted and encrypted, but we may decide to remove these subsubsections title as well -->

### Unencrypted forwarder

If the resolver announced by the network is a classic unencrypted forwarder, it is frequently the case that such
forwarders are difficult to upgrade to support encrypted operation. 
In this case, the internal DNS traffic cannot be encrypted.

The best that can be achieved is that resolution that can be performed by another encrypted resolver.
The next upstream local network that is the as close as possible to the site local resolver is the resolver provided by the ISP. 
In the case of a forwarder, both resolvers are equivalent and even equal. 

The main challenge of discovery is that site local resolvers are likely to use site local IP addresses which is provisioned to client in an unsecure way.


| Requirement | Description |
| R2.1 |  The client MUST be able to retrieve the resolver of the upstream ISP. 
| R2.2 | Resolver MUST provide some information of which traffic can be resolved so the client can determine which resolvers are eligible to a given DNS query |
| R2.3 | The client MUST be able to bound the information to the connectivity provider or site-local |
| R2.4 | An (upstream ISP) MUST be able to announce the support of ( encrypted ) resolver DNS service and this information MUST be accessed throughout the forwarder |
| R2.5 | The client SHOULD be able to determine the scope of the site-local.
Note that the determination of which traffic is eligible to one resolver is also subject to the client's interpretation as local scope FQDN MAY be served at any level in the network. 
Similarly split views makes some domains eligible at multiple level as well.|  

<!-- 

In such cases it is useful for the resolver
provider to be able to declare which encrypted resolvers they provide, and for the client to be able to discover
them.


If the client wishes to, it can then use one of those resolvers and bypass the local forwarder.

| Requirement | Description |
| R3.1 | Example requirement |
 -->

#### Encrypted forwarder

If a subset of local resolvers supports encrypted DNS, the client may not initially be aware that its local resolver
supports it. Discovering this may require communication with the local resolver, or an upstream resolver, over an
unencrypted transport. Once discovered, the local encrypted forwarder may be selected by the client, gaining the
benefits of encryption while retaining the benefits of a local caching forwarder with knowledge of the local topology.

Another benefit occurs with IoT devices. A common usage pattern for such devices is for it to "call home"
to a service that resides on the public Internet, where that service is referenced through a domain name
(A or AAAA record). As discussed in Manufacturer Usage Description Specification {{?RFC8520}}, because these devices
tend to require access to very few sites, all other access should be considered suspect. However, if the query
is not accessible for inspection, it becomes quite difficult for the infrastructure to suspect anything.

<!-- it seems to me that R2.x includes those associated to the use encrypted forwarder case. --> 
<!--
| Requirement | Description |
| R4.1 | Example requirement |
-->


### Client-selected resolvers {#client-selected}

Client devices often allow the device administrator to select a specific DNS resolver to use
on certain networks, or on all networks. Historically, this selection was specified only with an
IP address.

Discovering which equivalent encrypted resolvers are offered by the same entity allows the client
to "upgrade" connections to use encrypted DNS.
This can provide several benefits:

- Prevent devices along the network path to the selected resolver from observing client DNS messages
- Verify that answers come from the selected DNS resolver
- Authenticate that the DNS resolver is the one selected by the client

In doing so it is critical that the new resolver provides the same semantics as the original
unencrypted one. Switching to one that provides different answers would break the expectation
of the user who previously selected that resolver.

The main difference between this use case and the Homenet and private enterprise name use case are:
* It is assumed the client has configured the information of the initial resolver and as such that information is considered as trusted as opposed as received via an unprotected mechanism. This relaxes R2.3.  
* The resolver is usually on the Internet which makes it non eligible for the resolution that are site local scope. While the scope of the resolver is known the client, the client still needs to learn what the site local scope are cf R2.5.  This however relaxes R2.3. 
* Similarly these resolvers are usually on the internet and so the network proximity aspects do not apply which relaxes R2.1 and R2.4.

<!--
| Requirement | Description |
| R5.1 | Example requirement |
-->


### VPN resolvers {#vpn}

Virtual Private Networks (VPNs) also can provision DNS resolvers. In addition to being able to use
DHCP or RAs, VPNs can provision DNS information in an explicit configuration message. For example,
IKEv2 can provision DNS servers using Configuration Attributes {{?RFC7296}}.

VPNs can also configure Split DNS rules to limit the use of the configured resolvers to specific domain
names {{?RFC8598}}.

Discovering an encrypted resolver that is provisioned by a VPN can provide the same benefits
as doing so for a local network, but applied to the private network. When using Split DNS, it becomes
possible to use one encrypted resolver for private domains, and another for other domains.

This use is very similar to  the use case and the Homenet and private enterprise name use case. This only difference is that when IKEv2 is used the communication of the initial resolver has more trusted then it has when carried over non secured channels. 
<!--
| Requirement | Description |
| R6.1 | Example requirement |
-->

<!--

# Discovery of limited domain resolvers

Similar to how VPN DNS configurations can use Split DNS for private names, other network environments
can support resolution of names that are specific to the local environment. For example, an enterprise-managed
Wi-Fi network might be able to access both the Internet and a private intranet. In such a scenario, the private
domains managed by the enterprise might only be resolvable using a specific DNS resolver.

Discovering an encrypted resolver for a subset of names allows a client to perform Split DNS while maintaining
the benefits of encrypted DNS. For example, a client could use a client-selected encrypted resolver for most
domains, but use a different encrypted resolver for enterprise-private domains.

Such domain-specific resolver discovery mechanisms additionally need to provide some information about
the applicability and capabilities of encrypted resolvers. This information can either be provisioned or
can be discovered based on clients actively trying to access content.

| Requirement | Description |
| R7.1 | Example requirement |

## Discover a mapping between a locally-hosted domain and a resolver

Narrative required.

| Requirement | Description |
| R8.1 | Example requirement |

### Encrypted resolvers for local or home content {#local-content}

Accessing locally-hosted content can require the use of a specific resolver. For example, captive networks
or networks with walled-garden content like media on airplane Wi-Fi networks can rely on using a
resolver hosted on the local network.

In cases where a client is using an encrypted resolver provisioned by a network, and that encrypted resolver
is able to resolve names of local content, this can fall into the use case described in {{local-network}}. However,
it might be necessary to discover a local encrypted resolver along with specific domains if:

- the network-provisioned encrypted resolver is not able to resolve local-only names, or
- the client has a more-preferred encrypted resolver for generic traffic, and would otherwise not be able to access local content

The first point can occur in a hybrid deployment, e.g. when the local resolver is unencrypted but a central one is encrypted.
Clients choosing the encrypted resolver for most queries will need to be advised to refer to the local one for some names.

This case also include accessing content specific to a home network.

| Requirement | Description |
| R9.1 | Example requirement |

### Locally-cached content

Narrative required.

| Requirement | Description |
| R10.1 | Example requirement |

### Private enterprise names

As stated above, an enterprise-managed Wi-Fi network might be able to access both the Internet and a private
intranet. The private domains managed by the enterprise might only be resolvable using a specific DNS resolver,
hence use of that resolver is essential for such domains. However it does not necessarily mean that all queries
for all domains have to flow through that resolver.

Only sending the necessary queries through the enterprise resolver, and not generic Internet queries, has the privacy
benefit of only exposing traffic to the enterprise that fall within a limited set of domains.

Using encrypted DNS for private names also opens up the possibility of doing private name resolution outside
of the content of a VPN or managed network. If the DNS resolver authenticates clients, it can offer its resolver
for private names on a publicly accessible server, while still limiting the visibility of the DNS traffic.

| Requirement | Description |
| R11.1 | Example requirement |

-->


# Direct Discovery {#direct}

While section {{associated}} describes use cases where the discovery is initiated from an existing configuration setting or an existing infrastructure, this section considers use cases where the existence of alternate resolvers is performed though explicit and specific signalling. 

The main advantage of having a direct or explicit signalling is that the necessary inputs are provided in order to optimize the discovery and may address use cases that are not based on a pre-configured resolver.

Generic requirement for newly proposed mechanism are as follows:

| Requirement | Description |
| RXX.1 | Discovery mechanisms MUST NOT be disruptive for the legacy DNS client or infrastructure |
| RXX.2 | The resolver operator MUST make explicitly available the scope associated to the resolver, the necessary connectivity informations and SHOULD make explicitly any sort of information that MAY be used during the selection process. Information associated to the resolver are expected to evolve over time.  |
| RXX.3 | When multiple resolver are provided the operator MUST be able to indicate a preference |
| RXX.4 | Any information provided MUST be bound to the identity of the source providing the information as well as to the resolver itself. |


## Network configured resolvers

Traditional network provisioning may consider adding necessary information to use DoT or DoH to options in DHCP, RAs, or IKEv2)


## Enabling third parties to define subsets of Trusted resolvers

A client may hardly be able to select which resolvers are trusted and might delegate such selection to a third party. 

## Opportunist discovery 

A client MAY learn appropriated resolver while browsing. In such cases, it remains crucial the resolvers clearly indicates the scope associated to the resolver as it might be limited. 

It should be clear from the client perspective for a given domain if there is a clear advantage to use one resolver over a general purpose resolver. 
In other words, if a resolver has a "special relation" - such as serving of being authoritative for example.com - that should be clearly provided to the client.


### Encrypted resolvers for content providers {#cdn-content}

Content Delivery Networks (CDNs), and content-providers more broadly, can also provide encrypted DNS resolvers that can
be used by clients over the public Internet. These resolvers can either allow resolution of all public names (like
normal recursive resolvers), or be designed to serve a subset of names managed by the content provider (like an
authoritative resolver). Using these resolvers can allow the content provider to directly control how DNS answers are
used for load balancing and address selection, which could improve performance of connections to the content provider.

Using a content-provider's encrypted resolver can also provide several privacy and security benefits: 

- Prevent devices along the network path to the content-provider's resolver from observing client DNS messages
- Verify that answers come from the entity that manages the domains being resolved
- Reduce the number of entities able to monitor the specific names accessed by a client to only the client and the content provider, assuming that the content provider would already see the names upon a secure connection later being made based on the DNS answers (e.g., in the TLS SNI extension)

However doing so can create other issues. (To be described)

| Requirement | Description |
| R12.1 | Example requirement |


### Web browsing


# Information {#information}

## Scope 

* served client IPs
* served FQDN ( global resolution , private resolution, hosting web servers/authoritative...) 



# Privacy and security requirements {#priv-sec}

Encrypted DNS improves the privacy and security of DNS queries and answers in the presence of malicious
attackers. Such attackers are assumed to interfere with or otherwise impede DNS traffic and corresponding
discovery mechanisms. They may be on-path or off-path between the client and entities with which the client
communicates {{?RFC3552}}. These attackers can inject, tamper, or otherwise interfere with traffic as needed.
Given these capabilities, an attacker may have a variety of goals, including, though not limited to:

- Monitor and profile clients by observing unencrypted DNS traffic

- Modify unencrypted DNS traffic to filter or augment the user experience

- Block encrypted DNS

Clients cannot assume that their network does not have such an attacker unless given some means of authenticating or otherwise
trusting the communication with their DNS resolver.

Given this type of attacker, resolver discovery mechanisms must be designed carefully to not worsen a client's security or
privacy posture. In particular, attackers must not be able to:

- Redirect secure DNS traffic to themselves when they would not otherwise handle DNS traffic.

- Override or interfere with the resolver preferences of a user or administrator.

- Cause clients to use a discovered resolver which has no authenticated delegation from a client-known entity.

- Influence automatic discovery mechanisms such that a client uses one or more resolvers that are not
otherwise involved with providing service to the client, such as: a network provider, a VPN server, a
content provider being accessed, or a server that the client has manually configured.

Beyond these requirements, standards describing resolver discovery mechanisms must not place any requirements
on clients to select particular resolvers over others.

## On opportunistic encryption

Opportunistic encrypted DNS, when the client cannot authenticate the entity that provides encrypted DNS, does
not meet the requirements laid out here for resolver discovery. While opportunistic encryption can provide some
benefits, specifically in reducing the ability for other entities to observe traffic, it is not a viable solution
against an on-path attacker.

Performing opportunistic encrypted DNS does not require specific discovery mechanisms. Section 4.1 of {{?RFC7858}}
already describes how to use DNS-over-TLS opportunistically.

## Handling exceptions and failures

Even with encrypted DNS resolver discovery in place, clients must be prepared to handle certain scenarios where encrypted DNS
cannot be used. In these scenarios, clients must consider if it is appropriate to fail open by sending the DNS queries without
encryption, fail closed by not doing so, or presenting a choice to a user or administrator. The exact behavior is a
local client policy decision.

Some networks that use Captive Portals will not allow any Internet connectivity until a client has interacted with the portal
{{?I-D.ietf-capport-architecture}}. If these networks do not use encrypted DNS for their own resolution, a client will need to perform
unencrypted DNS queries in order to get out of captivity. Many operating systems have specific client code responsible for detecting
and interacting with Captive Portals; these system components may be good candidates for failing open, since they do not generally
represent user traffic.

Other networks may not allow any use of encrypted DNS, or any use of encrypted DNS to resolvers other than a network-provisioned
resolver. Clients should not silently fail open in these cases, but if these networks are trusted by or administered by the user, the user
may want to specifically follow the network's DNS policy instead of what the client would do on an unknown or untrusted network.

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
Additional considerations common to all of them are described in {{priv-sec}}.


# IANA Considerations

This document has no IANA actions.



--- back

# Acknowledgments
{:numbered="false"}

This document was started based on contributions during the ADD meeting of IETF108, on the list, and
from draft-pauly-add-requirements. (TODO: add names from all these sources).

More contributions are required! Please consider starting a github issue, submit a pull request, or simply
raise the topic on the ADD list.
