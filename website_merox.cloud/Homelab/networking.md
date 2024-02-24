# Networking

#### Networking Infrastructure Overview

Gateway to the Internet 🌐: The setup begins with a Huawei Optical Network Terminal (ONT) from Orange, in bridge mode, forwarding a public IP address to the pfSense router, marking the gateway to the internet.


#### Core Network Services


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

![Traefik](/images/content/traefik2.png "Traefik")

!!!danger A virtual machine running Kali Linux features for network scanning and vulnerability assessments, indicating a strong focus on security. This setup will be detailed further in the Security section.
!!!

!!!light In summary, the network is designed for security, efficiency, and scalability. Integrating both traditional and modern technologies, such as Traefik, ensures the lab is prepared for current needs and future growth.
!!!



## Configs
>See the network configurations of my homelab

+++ DNS

#### Unbound DNS


```bash #
[2.7.0-RELEASE][merox@fw]/root: cat /var/unbound/unbound.conf
##########################
# Unbound Configuration
##########################

##
# Server configuration
##
server:

chroot: /var/unbound
username: "unbound"
directory: "/var/unbound"
pidfile: "/var/run/unbound.pid"
use-syslog: yes
port: 53
verbosity: 1
hide-identity: yes
hide-version: yes
harden-glue: yes
do-ip4: yes
do-ip6: yes
do-udp: yes
do-tcp: yes
do-daemonize: yes
module-config: "iterator"
unwanted-reply-threshold: 0
num-queries-per-thread: 4096
jostle-timeout: 200
infra-keep-probing: yes
infra-host-ttl: 900
infra-cache-numhosts: 10000
outgoing-num-tcp: 10
incoming-num-tcp: 10
edns-buffer-size: 1432
cache-max-ttl: 86400
cache-min-ttl: 0
harden-dnssec-stripped: yes
msg-cache-size: 4m
rrset-cache-size: 8m

num-threads: 2
msg-cache-slabs: 2
rrset-cache-slabs: 2
infra-cache-slabs: 2
key-cache-slabs: 2
outgoing-range: 4096
#so-rcvbuf: 4m

prefetch: no
prefetch-key: no
use-caps-for-id: no
serve-expired: no
aggressive-nsec: no
# Statistics
# Unbound Statistics
statistics-interval: 0
extended-statistics: yes
statistics-cumulative: yes

# TLS Configuration
tls-cert-bundle: "/etc/ssl/cert.pem"

# Interface IP addresses to bind to
interface-automatic: yes

# Outgoing interfaces to be used
outgoing-interface: X.X.X.X

# DNS Rebinding
# For DNS Rebinding prevention
private-address: 127.0.0.0/8
private-address: 10.0.0.0/8
private-address: ::ffff:a00:0/104
private-address: 172.16.0.0/12
private-address: ::ffff:ac10:0/108
private-address: 169.254.0.0/16
private-address: ::ffff:a9fe:0/112
private-address: 192.168.0.0/16
private-address: ::ffff:c0a8:0/112
private-address: fd00::/8
private-address: fe80::/10



# Access lists
include: /var/unbound/access_lists.conf

# Static host entries
include: /var/unbound/host_entries.conf

# dhcp lease entries
include: /var/unbound/dhcpleases_entries.conf



# Domain overrides
include: /var/unbound/domainoverrides.conf
# Forwarding
forward-zone:
        name: "."
        forward-addr: 1.1.1.1
        forward-addr: 8.8.8.8
        forward-addr: X.X.X.188


# Unbound custom options
server:include: /var/unbound/pfb_dnsbl.*conf


###
# Remote Control Config
###
include: /var/unbound/remotecontrol.conf
```

+++ DHCP
#### DHCP
```bash #

[2.7.0-RELEASE][admin@fw.merox.cloud]/root: cat /var/dhcpd/etc/dhcpd.conf 

option domain-name "merox.cloud";
option ldap-server code 95 = text;
option domain-search-list code 119 = text;
option arch code 93 = unsigned integer 16; # RFC4578

default-lease-time 7200;
max-lease-time 86400;
log-facility local7;
one-lease-per-client true;
deny duplicates;
update-conflict-detection false;
authoritative;
class "s_lan" {
        match pick-first-value (option dhcp-client-identifier, hardware);
}
subnet X.X.X.X.0 netmask 255.255.255.0 {
        pool {
                option domain-name-servers X.X.X.X.188,X.X.X.X.1;

                range X.X.X.X.202 X.X.X.X.254;
        }

        option routers X.X.X.X.1;
        option domain-name-servers X.X.X.X.188,X.X.X.X.1;
        ping-check true;

}


subclass "s_lan" 1:bc:24:11:f8:37:bd;
host s_lan_7 {
        hardware ethernet bc:24:11:68:c2:c6;
        fixed-address X.X.X.X.80;
        option host-name "k3s-admin";

}
subclass "s_lan" 1:bc:24:11:68:c2:c6;
host s_lan_8 {
        hardware ethernet bc:24:11:e0:a7:02;
        fixed-address X.X.X.X.81;
        option host-name "k3s-master-01";

}

subclass "s_lan" 1:90:09:d0:50:08:4b;
class "s_opt1" {
        match pick-first-value (option dhcp-client-identifier, hardware);
}
subnet X.X.X.Y.0 netmask 255.255.255.0 {
        pool {
                option domain-name-servers X.X.X.X.1;

                range X.X.X.Y.100 X.X.X.Y.200;
        }

        option routers X.X.X.Y.1;
        option domain-name-servers X.X.X.X.1;
        ping-check true;

}
host s_opt1_0 {
        hardware ethernet 14:4f:8a:dc:13:ba;
        option dhcp-client-identifier "merox_lenovo_laptop";
        fixed-address X.X.X.Y.10;

}
subclass "s_opt1" 1:14:4f:8a:dc:13:ba;
subclass "s_opt1" "merox_lenovo_laptop";

subclass "s_opt1" 1:c4:c1:7d:a5:13:8e;
subclass "s_opt1" "iphone_merox";
```
+++ INGRESS
#### Traefik

### values.yaml

```yaml #

globalArguments:
  - "--global.sendanonymoususage=false"
  - "--global.checknewversion=false"

additionalArguments:
  - "--serversTransport.insecureSkipVerify=true"
  - "--log.level=INFO"

deployment:
  enabled: true
  replicas: 3 # match with number of workers
  annotations: {}
  podAnnotations: {}
  additionalContainers: []
  initContainers: []

nodeSelector: 
  worker: "true" # add these labels to your worker nodes before running

ports:
  web:
    redirectTo:
      port: websecure
      priority: 10
  websecure:
    tls:
      enabled: true
      
ingressRoute:
  dashboard:
    enabled: false

providers:
  kubernetesCRD:
    enabled: true
    ingressClass: traefik-external
    allowExternalNameServices: true
  kubernetesIngress:
    enabled: true
    allowExternalNameServices: true
    publishedService:
      enabled: false

rbac:
  enabled: true

service:
  enabled: true
  type: LoadBalancer
  annotations: {}
  labels: {}
  spec:
    loadBalancerIP: X.X.X.100 # this should be an IP in the Kube-VIP range
  loadBalancerSourceRanges: []
  externalIPs: []
```
### middleware.yaml
```yaml
apiVersion: traefik.containo.us/v1alpha1
kind: Middleware
metadata:
  name: default-headers
  namespace: traefik
spec:
  headers:
    browserXssFilter: true
    contentTypeNosniff: true
    forceSTSHeader: true
    stsIncludeSubdomains: true
    stsPreload: true
    stsSeconds: 15552000
    customFrameOptionsValue: SAMEORIGIN
    customRequestHeaders:
      X-Forwarded-Proto: https
```
## Traefik Dashboard

### traefik_ingress.yaml
```yaml
apiVersion: traefik.containo.us/v1alpha1
kind: IngressRoute
metadata:
  name: traefik-dashboard
  namespace: traefik
  annotations: 
    kubernetes.io/ingress.class: traefik-external
spec:
  entryPoints:
    - websecure
  routes:
    - match: Host(`traefik.merox.cloud`) 
      kind: Rule
      middlewares:
        - name: traefik-dashboard-basicauth
          namespace: traefik
      services:
        - name: api@internal
          kind: TraefikService
  tls:
    secretName: X-tls 
```
### secret-dashboard.yaml

```yaml


---
apiVersion: v1
kind: Secret
metadata:
  name: traefik-dashboard-auth
  namespace: traefik
type: Opaque
data:
  users: 2YW3sSRtaW4nOIfbenjenwfewipo732tdSadUuRU0wRzZSLg==

  ```
  
### middleware.yaml
```yaml
apiVersion: traefik.containo.us/v1alpha1
kind: Middleware
metadata:
  name: traefik-dashboard-basicauth
  namespace: traefik
spec:
  basicAuth:
    secret: traefik-dashboard-auth
```
+++

