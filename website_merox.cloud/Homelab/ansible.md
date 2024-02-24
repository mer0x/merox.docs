# Ansible

In the realm of DevOps and automation, I've embarked on a journey exploring various configuration management and orchestration tools. Over the years, I've worked with **Puppet** and **Salt** for personal and professional projects, each offering unique insights into the automation landscape. Recently, my homelab has seen a new addition: **Ansible**.

## Embracing Ansible in My Homelab 🛠
Ansible has become my go-to tool for automating and managing my homelab infrastructure. Its simplicity and agentless architecture make it an appealing choice for quick wins in automation without the overhead of managing agents on nodes. Here, I'll share insights into two of the playbooks I've employed to streamline my setup.

## A Glimpse into My Ansible Playbooks 📖

### 📦 System Upgrade Playbook

   For Debian/Ubuntu systems, it leverages the apt module to update the cache, upgrade all packages, and clean up.
   For CentOS/RHEL systems, it uses the yum module for similar tasks, ensuring my entire Linux fleet remains secure and efficient.

```yaml
---

- hosts: all
  tasks:
  - name: install all updates
    apt:
      upgrade: dist
      update_cache: yes
      autoremove: yes
      autoclean: yes
    register: result
  - name: List installed and updated packages
    shell: grep -E "^$(date +%Y-%m-%d).+ (install|upgrade) " /var/log/dpkg.log |cut -d " " -f 3-5
    register: result
  - name: Show Output
    debug: msg="{{ result.stdout_lines }}"
- hosts: centos
  tasks:
  - name: install all updates
    yum:
      name: '*'
      update_cache: yes
      state: latest
  - name: List updated packages
    shell: rpm -qa --last | grep "$(date +%a\ %d\ %b\ %Y)" |cut -f 1 -d " "
    register: result
    args:
      warn: no
  - name: Updates packages
    debug: msg="{{ result.stdout_lines }}"


```

### 🖥️ Windows Update Playbook
The upgrade_windows.yml playbook is tailored specifically for Windows servers in my homelab, utilizing the win_updates module to fetch and install updates across various categories, including security and critical updates. It's designed to run on a schedule, ensuring my Windows environments are always running the latest updates without manual intervention.

```yaml
- name:  Windows Update
  hosts: wserver.merox.cloud
  gather_facts: false
  tasks:
    - name: Running Windows Update
      win_updates:
        category_names: ['SecurityUpdates','CriticalUpdates','UpdateRollups', 'Updates', 'DefinitionUpdates']
        reboot: yes

```

### 🚀 VM Creation in Proxmox

Venturing into virtualization, the create_vm_proxmox.yml playbook automates the creation of VMs in Proxmox VE. This playbook defines a new VM with specific resources, like CPU, memory, and storage, and attaches an Ubuntu ISO for installation. It exemplifies how Ansible can interact with virtualization environments to streamline VM deployment.

```yaml
---
- name: Create VM in Proxmox
  hosts: localhost
  gather_facts: no
  tasks:
    - name: Create a new VM
      community.general.proxmox_kvm:
        api_user: root@pam
        api_password: YOUR_PASSWORD
        api_host: YOUR_PROXMOX_HOST
        validate_certs: no
        vmid: "100"
        name: "ansible-vm"
        node: "pve"
        memory: 2048
        cores: 2
        sockets: 1
        cpu: "host"
        net: '{"net0":"virtio,bridge=vmbr0"}'
        disk: '{"size":"30G"}'
        ostype: l26
        iso: "local:iso/ubuntu-22.04.1-live-server-amd64.iso"
        ide2: "local:iso/ubuntu-22.04.1-live-server-amd64.iso,media=cdrom"
        bootdisk: "virtio0"
        boot: "cdn"
        description: "Created by Ansible"
        state: "present"
```

### ⏰ Automation with Crontab

To ensure these playbooks run regularly and keep my systems in peak condition, I've scheduled them with crontab entries. This setup automates weekly updates for both Linux and Windows servers, with output and errors redirected to specific files for easy monitoring:
```bash
0 0 * * 0 /usr/local/bin/ansible-playbook /home/merox/playbooks/upgrade.yml -i /home/merox/playbooks/hosts.ini >> /home/merox/playbooks/last_update.txt 2>> /home/merox/playbooks/last_update.err
0 0 * * 0 /usr/local/bin/ansible-playbook -i /home/merox/playbooks/hosts_windows.ini -T 60 /home/merox/playbooks/upgrade_windows.yml >> /home/merox/playbooks/last_windows_update.txt 2>> /home/merox/playbooks/last_windows_update.err
```

## Continuous Learning and the Path Forward with Ansible 🌱

As I venture deeper into the world of DevOps with my homelab, I recognize that my journey with Ansible is just beginning. Despite the initial strides I've made, there's a vast landscape of knowledge and skills yet to be explored. Ansible, with its powerful capabilities and simplicity, has opened a new chapter in my automation endeavors, one where I'm both a learner and an explorer.


## The Road Ahead 🛣
As I continue to build, automate, and refine my homelab with Ansible, the journey is as much about acquiring new skills as it is about applying them. The beauty of Ansible lies not just in its technical prowess but in its community, resources, and the continuous evolution of its ecosystem.

The exploration doesn't end with mastering Ansible. The DevOps landscape is ever-changing, and with tools like Terraform on the horizon, the integration of configuration management and infrastructure provisioning is an exciting prospect. The journey ahead promises a blend of challenges and opportunities, pushing the boundaries of what's possible in my homelab and beyond.


