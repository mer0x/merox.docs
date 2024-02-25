# Virtual Machines


## Virtual Machines Features

Virtual Machines Features

-    Built off of [QEMU](https://www.qemu.org/), an open-source emulator which interoperates with [KVM](https://www.linux-kvm.org/) at near native performance
-    Windows, Linux, and BSD operating systems support
-    QEMU Guest Agent support allowing for quiesced snapshots and reporting additional information such as OS and kernel versions to the vm dashboard.
-    Console access through VNC (native), Spice Client, or Serial Console
-    Virtio paravirtualization device support for disks and network interface cards
-    Hotplug support for disk drives and network interface cards
-    Host processor and GPU passthrough support


## Virtual Machines Cluster

##### Overview

- In PulsarDC, I harness the power of virtual machines (VMs) for everything from Kubernetes clusters to secure web browsing and network management. Below is a compact overview of the VM setup:
{.grid-list}
###  Ubuntu VMs

-    Purpose: Serve as worker nodes for our K3S Kubernetes production cluster.
-    Specs: 3 VMs, each with 16GB RAM, 4 CPU cores, and 100GB SSD.
-    Tech: Utilizes cloud-init for seamless setups and Longhorn for distributed storage within Kubernetes.

###  KASM VM

-    Purpose: Provides secure, containerized web browsing.
-    Specs: 6GB RAM, 2 CPU cores, 64GB SSD.
-    Details: KASM ensures web isolation and secure access to web apps.

###  Windows Server 2019 VM

-    Purpose: Manages Active Directory (AD), DNS, and GPOs across the network.
-    Specs: 6GB RAM, 4 CPU cores, 32GB SSD.
-    Function: Centralizes network identity, access, and policy enforcement.

###  Networking & Backup

-    DHCP: IP management via pfSense.
-    Backup: Bi-weekly to Synology DS223 (RAID1) using Hyper Backup.

###  Monitoring & Alerts

-    Monitoring: With prometheus-node-exporter for real-time metrics.
-    Alerts: Email notifications via Alertmanager for any operational issues.


| id       | type | cpu   | disk   | maxcpu | maxdisk   | maxmem    | mem     | name        | node    |
|----------|------|-------|--------|--------|-----------|-----------|---------|-------------|---------|
| qemu/103 | qemu | 0.00% | 0.00 B | 4      | 64.00 GiB | 6.00 GiB  | 0.00 B  | kasm        | citadel |
| qemu/105 | qemu | 4.84% | 0.00 B | 4      | 32.00 GiB | 4.00 GiB  | 2.02 GiB| winserver   | nexus   |
| qemu/304 | qemu | 13.36%| 0.00 B | 4      | 125.20 GiB| 16.00 GiB | 5.18 GiB| k3s-01      | citadel |
| qemu/305 | qemu | 12.63%| 0.00 B | 4      | 125.20 GiB| 16.00 GiB | 6.58 GiB| k3s-02      | helix   |
| qemu/306 | qemu | 17.99%| 0.00 B | 4      | 125.20 GiB| 14.00 GiB | 8.83 GiB| k3s-03      | nexus   |
| qemu/800 | qemu | 0.00% | 0.00 B | 4      | 25.20 GiB | 4.00 GiB  | 0.00 B  | ubuntu-cloud| helix   |

