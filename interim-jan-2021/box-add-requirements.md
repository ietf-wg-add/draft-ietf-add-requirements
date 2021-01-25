# Draft-box-add-requirements-02

An outline of the main changes vs -01

* Equivalence can mean many things, so we don't make it a requirement.

* Instead we concentrate on on the ability of an untrusted network or resolver to designate one or more resolvers.

* Designation is defined as an assertion by a network, or by a resolver, that one or more other resolvers are safe and appropriate to use without user intervention.

* Three subcases of resolver-identified: local to local, local to upstream and public to public.

* Clients still need to make their own decisions about whether and when to use designated resolvers (or not). Supplying additional information into that process would be useful.

* So we should ask ourselves which information could usefully be transported to the client to assist with that?




