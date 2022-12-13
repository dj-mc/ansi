# ansi

## Ansible

Automate management of remote systems to control their state.

- Python-based
- Owned by RedHat (CentOS)
- Agentless
- No required client, just use SSH
- Push-based model

### Control Node

- Ansible is installed on the control node
- Runs Ansible commands like `ansible` or `ansible-playbook`
- Most computers (even laptops) are capable of being the control node
- Multiple control nodes are possible (but see [AAP](https://www.redhat.com/en/resources/ansible-automation-platform-beginner-guide-ebook) instead)

### Managed Nodes (aka hosts)

- The remote systems/hosts that Ansible controls
- Ansible is not (usually) installed on managed nodes

### Inventory

- Can be a single source file (aka `hostfile`)
- As a list of logically organized managed nodes
- Provides information like a host's IP address
- Describes how systems/hosts are deployed

---

## Using Ansible + Vagrant

- Vagrant builds and manages reproducible VM's using a config file
- Mimic remote machines in a local virtualized development environment
- Locally test Ansible playbooks, server provisioning, and deployments
- Integrates well with Ansible as a provisioner of VM's and cloud providers

Claims:

- Lowers development environment setup time
- Increases production parity; see #10 of [12factor](https://12factor.net/dev-prod-parity)
- Eliminates the infamous "works on my machine"

### What Vagrant Can Do

- Isolate deps/configs inside a single disposable, consistent env, _e.g._:
- `Vagrantfile` & `vagrant up`: then everything is installed/configured
- Consistent workflow developing and testing infrastructure scripts
- Test bash scripts, Puppet modules, etc. using local virtualized envs
- Test those same scripts (and configs) on remote clouds like AWS

Get Vagrant from [here](https://developer.hashicorp.com/vagrant/downloads).

Ensure **VirtualBox** (or another VM provider) is installed!

```bash
# Create Vagrantfile (in project directory):
vagrant init hashicorp/bionic64

# Start the virtual machine
vagrant up
```

The `SharedFoldersEnableSymlinksCreate` option is enabled.  
https://www.virtualbox.org/manual/ch04.html#sharedfolders

Or disable it: `VAGRANT_DISABLE_VBOXSYMLINKCREATE=1`

```bash
# Explore your newly created VM!
vagrant ssh

# Logout or Ctrl+D
logout
```

---

## Using VirtualBox

1. Get VirtualBox from [here](https://www.virtualbox.org/wiki/Linux_Downloads)

```bash
# Test if your system boots in EFI or BIOS
test -d /sys/firmware/efi && echo efi || echo bios

# WARNING:
# EFI will require that VirtualBox's
# external kernel modules to be signed

uname -r
# Find the version name of your kernel
uname -mrs # Full version name

# WARNING:
# Will fail without installing external kernel modules!
sudo dpkg -i virtualbox-7.0_7.0.4-154605~Ubuntu~jammy_amd64.deb
```

2. Get external kernel modules from [here](https://www.virtualbox.org/manual/UserManual.html#externalkernelmodules)

**Alternatively**: use the automatic [VirtualBox.run](https://www.virtualbox.org/wiki/Linux_Downloads) script.

Right-click and save the `All distributions` link (don't open in browser).

```bash
# Uninstall failed dpkg installation
dpkg -l virtualbox # See if it's actually installed
sudo dpkg -P virtualbox-7.0 # Purge all associated files
dpkg -l virtualbox # Confirm it's purged

# And instead install via the automatic script
chmod +x ./VirtualBox-7.0.4-154605-Linux_amd64.run
sudo ./VirtualBox-7.0.4-154605-Linux_amd64.run install

# Should successfully install to /opt/VirtualBox
# /opt/VirtualBox/UserManual.pdf

# If a user needs access to USB devices from VirtualBox
sudo usermod -a -G vboxusers <username>
```

---

## Using Linux Kernel Virtualization

**WARNING**: Skip this section. This is for research purposes!

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

---

## VM's Using KVM + QEMU + libvirt

**WARNING**: Skip this section. This is for research purposes!

- `qemu-system-x86`: QEMU full system emulation binaries
- `cpu-checker`: Tools to help evaluate certain CPU (or BIOS) features
- `bridge-utils`: Utilities for configuring the Linux Ethernet bridge
- `libvirt-clients`: Programs for the libvirt library
- `libvirt-daemon`: Virtualization daemon
- `libvirt-daemon-system`: Libvirt daemon configuration files
- `virt-manager`: Desktop application for managing virtual machines
- `virtinst`: Utilities to create and edit virtual machines

On the `control node` machine:

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

---

## Automation of Linux Servers

...
