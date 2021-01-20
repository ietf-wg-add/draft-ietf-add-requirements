# Resolver behavior self-description

## Defining local-only namespaces
* Express namespaces which only this resolver can resolve
    * Authoritatively if the namespace collides with global namespaces
* Ex 1: Enterprise resolvers serving corporation-specific namespaces
* Ex 2: Public Wi-Fi or cellular networks offering network-local services

## Defining per-namespace optimization
* Express namespaces for which this resolver provides preferable resolutions
* Ex 1: ISP routes public content requests to network caches
* Ex 2: Cellular provider charges less for content from specific servers
* Ex 3: Public resolver designated to serve a namespace to limit parties privy to resolution

## Defining answer modification behavior
* Express how query answers are modified by the resolver (but not necessarily _why_)
* Ex 1: Will not resolve names breaching resolver policy 
    * May or may not define applicable namespaces
* Ex 2: Will drop specific record types
    * Helps client distinguish errors from by-design failures

## Defining protocol support
* Express what optional DNS-related functionality is supported 
* Ex 1: DNS Extended Errors and which codes to expect
* Ex 2: Which encryption protocols are supported
    * DoH and DoT, DoT only, etc.
    * Should be extensible to accommodate future protocols
* Ex 3: Access-controlled resolvers describing their properties outside of access control