# ansi

## ansible

Automate management of remote systems to control their state.

- Python-based
- Owned by RedHat (CentOS)
- Agentless
- No required client, just use SSH
- Push-based model

### control node

- Ansible is installed on the control node
- Runs Ansible commands like `ansible` or `ansible-playbook`
- Most capable computers (even laptops) can be the control node
- Multiple control nodes are possible (but see AAP instead)

### managed nodes (aka hosts)

- The remote systems/hosts that Ansible controls
- Ansible is not (usually) installed on managed nodes

### inventory

- Can be a single source file (aka `hostfile`)
- As a list of logically organized managed nodes
- Provides information like a host's IP address
- Describes how systems/hosts are deployed

## using vagrant

Vagrant builds and manages reproducible VM's using a config file.

## using Linux kernel virtualization

- Hypervisor provisions hardware's resources for use in a VM
- KVM (kernel-based VM) is the Linux kernel's (2.6.20+) built-in VM solution
- Verify your CPU's virtualization extension (Intel: vmx or AMD: svm)
- Verify KVM's modules are loaded on your machine's kernel

```bash
sudo /usr/sbin/kvm-ok
# Should return "KVM acceleration can be used"
# Otherwise enable virtualization in BIOS

# Count threads available
grep --count vmx /proc/cpuinfo

# Cores per socket
lscpu | grep "socket"
```

## VM's using KVM + QEMU + libvirt

- `qemu-system-x86`: QEMU full system emulation binaries
- `cpu-checker`: Tools to help evaluate certain CPU (or BIOS) features
- `bridge-utils`: Utilities for configuring the Linux Ethernet bridge
- `libvirt-clients`: Programs for the libvirt library
- `libvirt-daemon`: Virtualization daemon
- `libvirt-daemon-system`: Libvirt daemon configuration files
- `virt-manager`: Desktop application for managing virtual machines
- `virtinst`: Utilities to create and edit virtual machines

```bash
sudo apt install \
    qemu-system-x86 cpu-checker bridge-utils \
    libvirt-clients libvirt-daemon libvirt-daemon-system \
    virt-manager virtinst
```

```bash
systemctl enable libvirtd
systemctl restart libvirtd
```

## automation of linux servers

...
