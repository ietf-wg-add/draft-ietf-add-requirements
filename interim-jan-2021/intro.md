# Emerging Use Cases for Encrypted DNS
## Potential next steps for ADD

For the IETF ADD interim, January 2021

Rough consensus of:
* Chris Box
* Tommy Jensen
* Tiru Reddy
* Jim Reid
* Ben Schwartz

----
# Overview

* draft-box-add-requirements-02 describes what we need to upgrade from unencrypted to encrypted, in an untrusted network.
* DEER and draft-btw-add-home suggest mechanisms for part of this space.
* What's next for ADD?
* In these slides: three scenarios beyond simple designation, with proposed requirements
----
# Three scenarios

1. DNS configuration on explicitly trusted networks
2. Resolver behavior self-description
3. Publishing and using collections of encrypted resolvers
----
# Goals

* Solicit feedback on scope and requirements
* Gauge interest in possible next steps for the WG
* Proceed with proper requirements drafts where there is interest
* Support compliance and compatibility with other IETF standards
  - e.g. [Unknown RR types](https://tools.ietf.org/html/rfc3597), [DNSSEC](https://tools.ietf.org/html/rfc4035), [Extended DNS Errors](https://tools.ietf.org/html/draft-ietf-dnsop-extended-error)
----
# Non-goals

* Requiring the WG to solve all three scenarios
* Taking control away from the client
* Communicating policy
  - Resolvers indicate their own behavior, not policies to impose on the client
  - Resolver selection is always left to the client
  - Policies of a managed device are controlled by the administrator
