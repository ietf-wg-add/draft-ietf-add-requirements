# Resolver behavior self-description

## Defining local-only namespaces
* Express namespaces which only this resolver can resolve
    * Authoritatively if the namespace collides with any global names
* Ex 1: Enterprise resolvers serving corporation-specific namespaces
* Ex 2: Public Wi-Fi or cellular networks offering network-local services

## Defining per-namespace optimization
* Express namespaces for which this resolver provides preferable resolutions
* Ex 1: ISP routes public content requests to network caches
* Ex 2: Public resolver designated to serve a namespace to limit parties privy to resolution

## Defining resolver identity
* Express information consumable by humans describing the resolver's identity
* Ex 1: Provide human-legible documentation
    * Most likely a web page link to explain server identity, terms of use, etc.
	* Not used for decision making by any protocol peer; communicated to clients for display to users
* Ex 2: Provide human-friendly description of the resolver identity
    * Friendly name and/or iconography for display in client UI identifying configured resolver

## Defining protocol support
* Express what optional DNS-related functionality is supported 
* Ex 1: DNS Extended Errors and which codes to expect
      * Not exhaustive: server can still return any code
      * Codes 15-17 indicate kinds of filtering the resolver implements
* Ex 2: Access-controlled resolvers describing their properties outside of access control
