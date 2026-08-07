**Languages:** [Spanish](01-el-fallo-del-portal-cautivo.md) | [English]

---

# Theoretical Analysis: Captive Portal Session Decoupling through Layer 2

## Introduction

Captive portals are network access control mechanisms designed to intercept traffic from unauthenticated users on Wireless Local Area Networks (WLANs). This document analyzes, from a network architecture perspective, the structural weakness of access control systems that rely exclusively on Layer 2 identifiers (MAC addresses) for session tracking.

## Architecture of a Traditional Captive Portal

A conventional captive portal system involves the interaction of multiple network components within the OSI model:

1. **Access Point (AP) and WLAN Controller:** Manages the 802.11 association and forwards client frames.
2. **DHCP Server :** Assigns dynamic IP addresses and maintains a lease table associated with the client's MAC address.
3. **Authentication Server / Captive Portal:** Intercepts unauthorized HTTP/HTTPS traffic through DNS redirection or HTTP 302 responses, serving the web authentication interface.
4. **Network Access Server (NAS) / Firewall:** Enforces Access Control Lists (ACLs) that allow or deny packet forwarding to the Wide Area Network (WAN).

## The Problem with Data Link Layer-Based Identity

The OSI reference model defines the data link layer as the level responsible for frame delivery between nodes within the same local network segment. Historically, captive portal architectures have used the MAC address contained in the 802.11 header as a static session identifier.

The state management workflow typically follows this logic:

- **Unauthenticated State:** The MAC address `AA:BB:CC:DD:EE:FF` does not exist in the NAS authorized client list. Outbound requests are intercepted and redirected to the portal's IP address.
- **Authenticated State:** After completing the access requirement (policy acceptance, trial timer, or payment), the NAS registers the MAC address `AA:BB:CC:DD:EE:FF` in the allowed state table for a period of time $T$.
- **State Expiration:** Once time $T$ has elapsed, the NAS removes the MAC address from the allowed list or transfers it to a temporary block list.

## Design Flaw Analysis

The theoretical vulnerability of this scheme lies in the incorrect assumption that a MAC address is a permanent and immutable hardware identifier.

In modern operating systems, the networking subsystem manages Layer 2 addressing through software. When operating system-level MAC randomization is enabled:

1. The wireless interface disassociates and reassociates with the Access Point.
2. The client sends a `DHCP DISCOVER` packet using a different MAC address in the Ethernet header.
3. The network controller and DHCP server process the request as a completely independent physical entity with no previous history in the NAS state tables.
4. Since the new MAC address does not exist in either the block list or the authorized list, the infrastructure applies the default rule for new clients, restarting the captive portal evaluation process.

## Remediation and Secure Design Considerations

To prevent access control systems from relying on ephemeral Layer 2 identifiers, cybersecurity engineering recommends implementing more robust defensive architectures:

1. **IEEE 802.1X-Based Authentication:** Deploy WPA2/WPA3 Enterprise networks, where authentication is performed using cryptographic user credentials before granting access to the Layer 2 network.
2. **Session Binding Using Cryptographic Tokens:** Associate service authorization with server-validated signed session tokens instead of relying on MAC or IP addresses.
3. **Strict Reauthentication and Short DHCP Lease Times:** Configure expiration policies that require users to re-enter their credentials regardless of whether the physical interface reconnects or changes its MAC address.
4. **Multilayer State Inspection:** Use detection systems that combine multiple network attributes to identify devices independently of their local MAC address.