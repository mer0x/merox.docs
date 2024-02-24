# Hypervisors 

Welcome to the heart of our homelab's virtualization infrastructure at Merox Cloud! Here, we're excited to share the details of our powerful Proxmox hypervisors that keep our virtual machines (VMs) and containers (LXCs) running smoothly. Dive into the core of PulsarDC, our Proxmox cluster, and discover the technology that powers our homelab network.


 Proxmox environment is bolstered by three robust servers, each housed in its own mini-PC, known as Citadel, Helix, and Nexus. These servers are the pillars of the PulsarDC cluster, bringing high availability and flexibility to our virtualized infrastructure.
{.is-info}
## Cluster Overview 🌟
![Proxmox cluster](/images/content/proxmoxenv.png "Proxmox cluster")


## HA
-    **High Availability Proxmox Servers**: Citadel, Helix, and Nexus form the resilient backbone of our PulsarDC cluster, ensuring our applications and services are always up and running.


-    **Dedicated Network Segmentation**: Our Proxmox servers are part of a distinct network segment, isolated from other devices in our homelab. This setup enhances security and performance, ensuring that our virtualization infrastructure operates in an optimized environment.

![ha.png](/ha.png =600x)
## Storage
-    **NVME Storage with LVM**: Each server boasts an NVME drive for the operating system, utilizing Logical Volume Management (LVM). This setup offers fast boot times and efficient storage management, key for high-performance virtualization.

-    **ZFS Replication**: For data integrity and disaster recovery, our servers utilize ZFS Replication. This technology ensures our data is mirrored across the cluster, providing an extra layer of protection against data loss.

-    **NFS Backup Storage on Synology NAS**: A crucial aspect of our virtualization infrastructure is our backup strategy. We utilize an NFS-mounted storage location on our Synology NAS for all our backup needs. This setup allows us to store backups of VMs and containers according to our defined policies, ensuring data safety and quick recovery in case of any failure.

![storage.png](/storage.png =600x)

## Network
-    **1 Gb/s Ethernet Connection**: Connectivity is key in a homelab environment. Each of our Proxmox servers is equipped with a 1GB/s Ethernet connection, ensuring speedy and reliable network communication.
-
-    **VM and LXC Support**: Our cluster hosts a variety of Virtual Machines (VMs) and Linux Containers (LXCs), catering to a wide range of applications and services. These are seamlessly integrated into our network through bridge connections, allowing direct IP allocation from our DHCP pool.
-
-    **Seamless pfSense Integration**: Connection to our pfSense gateway is handled with ease, thanks to the bridge mode setup. This allows for straightforward management of network traffic and security, providing IPs directly from our DHCP pool to VMs and LXCs.

![networkproxmox.png](/networkproxmox.png =600x)




# Configs
- [See configs *from VMs and LXCs*](https://merox.cloud/en/hypervisors/configs)
{.links-list}
