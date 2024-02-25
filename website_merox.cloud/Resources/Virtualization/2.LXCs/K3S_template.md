### LXC K3S 💼
``` bash #
arch: amd64
cores: 4
features: fuse=1
hostname: lxc-k3s-ct-ready
memory: 4096
net0: name=eth0,bridge=vmbr0,firewall=1,hwaddr=BC:24:11:78:33:4B,ip=dhcp,type=veth
ostype: ubuntu
rootfs: Storage:basevol-210-disk-0,size=50G
swap: 0
template: 1
lxc.apparmor.profile: unconfined
lxc.cgroup.devices.allow: a
lxc.cap.drop: 
lxc.mount.auto: "proc:rw sys:rw"
```


!!!warning
Note: It's important that the container is stopped when you try to edit the file, otherwise Proxmox's network filesystem will prevent you from saving it.
!!!

In order, these options **(1) disable AppArmor**, **(2) allow the container's cgroup** to access all devices, **(3) prevent dropping any capabilities** for the container, and **(4) mount /proc and /sys** as read-write in the container.

Next, we need to publish the kernel boot configuration into the container. Normally, this isn't needed by the container since it runs using the host's kernel, but the Kubelet uses the configuration to determine various settings for the runtime, so we need to copy it into the container. To do this, first start the container using the Proxmox web UI, then run the following command on the Proxmox host:
```bash
pct push <container id> /boot/config-$(uname -r) /boot/config-$(uname -r)
```

Finally, in each of the containers, we need to make sure that /dev/kmsg exists. Kubelet uses this for some logging functions, and it doesn't exist in the containers by default. For our purposes, we'll just alias it to /dev/console. In each container, create the file /usr/local/bin/conf-kmsg.sh with the following contents:
```bash
#!/bin/sh -e
if [ ! -e /dev/kmsg ]; then
    ln -s /dev/console /dev/kmsg
fi
mount --make-rshared /
```

This script symlinks /dev/console as /dev/kmsg if the latter does not exist. Finally, we will configure it to run when the container starts with a SystemD one-shot service. Create the file /etc/systemd/system/conf-kmsg.service with the following contents:
```bash #
[Unit]
Description=Make sure /dev/kmsg exists

[Service]
Type=simple
RemainAfterExit=yes
ExecStart=/usr/local/bin/conf-kmsg.sh
TimeoutStartSec=0

[Install]
WantedBy=default.target
```

Finally, enable the service by running the following:
```bash
chmod +x /usr/local/bin/conf-kmsg.sh
systemctl daemon-reload
systemctl enable --now conf-kmsg
```

Special thanks to:  https://garrettmills.dev/