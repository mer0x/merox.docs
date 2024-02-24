# Networking

## Networking Infrastructure Overview

Gateway to the Internet 🌐: The setup begins with a Huawei Optical Network Terminal (ONT) from Orange, in bridge mode, forwarding a public IP address to the pfSense router, marking the gateway to the internet.


## Core Network Services


###### Security and Management Core 🔐:


-    **Firewall**: Filters traffic based on security rules.
-    **pfBlockerNG**: Blocks ads and malicious sites 🛡️.
-    **DHCP & DNS Services**: Assigns IP addresses and resolves DNS queries, with Unbound as a secondary DNS resolver 🔄.
-    **Intrusion Detection with Snort**: Monitors for security threats.
-    **Remote Accessibility**: Enabled through WoL and VPNs (IPsec & OpenVPN), offering remote access 🌍.

###### VLAN Management:
A TP-Link managed switch facilitates VLAN segmentation, with devices organized within **VLAN57** for streamlined network management.


###### Kubernetes and Traefik 🚀

The Kubernetes Ingress Controller, using Traefik, is pivotal for external access management, simplifying service deployment and routing.

![traefik.png](/traefik.png)

> A virtual machine running Kali Linux features for network scanning and vulnerability assessments, indicating a strong focus on security. This setup will be detailed further in the Security section.
{.is-danger}

>In summary, the network is designed for security, efficiency, and scalability. Integrating both traditional and modern technologies, such as Traefik, ensures the lab is prepared for current needs and future growth.


## Configs
- [See configs *from pfSense and Traefik*](https://merox.cloud/en/networking/configs)
{.links-list}
