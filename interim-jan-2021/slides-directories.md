# Scenario 3: Directories of Encrypted Resolvers

Three Parties:
* Publisher: Curates a list of distinct resolvers
* Client: Fetches the list from a trusted source
* Resolvers: Identified in the list, ready for access by the client
------------------------------------------------
# Example use cases

* An application (e.g. web browser) that provides users with a list of resolvers to consider, curated by a trusted third party
* An OS vendor wants to keep its list of trusted resolvers current without requiring a software update
* A user wants to choose a resolver from a list offered by a network operator who they trust
------------------------------------------------
# Requirements

* A list can be published by a trusted network
* A list can be published at an HTTP URL
* Each resolver controls its own self-description
* Suitable for use in an onscreen interactive menu
* Can be used as an additional safeguard for untrusted upgrade instructions
* Uses the same protocols as the previous scenarios
------------------------------------------------
# Non-requirements

* Defending against a malicious or inept publisher
* Defending against a malicious or inept resolver
* Support for extremely long lists (e.g. >1000 resolvers)
* Combining multiple resolvers that are not sufficient independently
* Grouping related resolvers
* Enable connection without use of a bootstrap resolver
