#DNS configuration on explicitly trusted networks

* Client can authenticate the identity of the network (or pre-existing relationship with the network)
    - BYOD devices joining Enterprise network without any MDM and configuration profile (e.g., using EAP-pwd, EAP-PSK).
    - IoT devices joining Enterprise network without a device management tool

#Goals

* Standardized discovery mechanism for BYOD and IoT devices.
* Discover local names to use Enterprise DNS server (similar to split DNS configuration in IKEv2)
   - Discover if the Enterprise network offers a split DNS configuration
* Secure Discovery of Enterprise DNS server 
    - Bootstrapping Remote Secure Key Infrastructures (BRSKI) discussed in ANIMA WG for IoT devices.
    - Leverage existing secure discovery mechanisms like IKEv2 for VPN

#Non-Goals

* IT-managed devices and IoT devices (using device management tool) are out of scope
* BYOD managed by MDM
* BYOD provisioned with configuration profile (e.g., Over-The-Air enrollment).
