# ansi

## Ansible

Automate management of remote systems to control their state.

- Python-based
- Owned by RedHat (CentOS)
- Agentless
- No required client, just use SSH
- Push-based model

Ansible has two packages: `ansible-core` (minimal) and `ansible` (full).  
Using `pipx` to install ansible is pretty easy, but not required.

```bash
# Isolated global package
pipx install --include-deps ansible
# Or as a regular --user package
python3 -m pip install --user ansible
```

### The Control Node

- Ansible is installed on the control node
- Runs Ansible commands like `ansible` or `ansible-playbook`
- Most computers (even laptops) are capable of being the control node
- Multiple control nodes are possible (but see [AAP](https://www.redhat.com/en/resources/ansible-automation-platform-beginner-guide-ebook) instead)

In this project the control node is installed on the same bare-metal machine
that's running Vagrant, but could be modified so that Ansible is installed on
and controlled from a VM created via Vagrant. See [this](https://github.com/rhce)
repo I created that does just that.

### Managed Nodes (aka hosts)

- The remote systems/hosts that Ansible controls
- Ansible is not (usually) installed on managed nodes
- Requires Python 2.7 or 3.5+ to run Ansible library code

### Inventory

- Can be a single source file (aka `hostfile`)
- As a list of logically organized managed nodes
- Provides information like a host's IP address
- Describes how systems/hosts are deployed

### Plays & Playbooks

- A play maps managed nodes to tasks
- A play is a basic unit of Ansible execution, and an object of a playbook
- Each play runs one or more tasks, and each task calls an Ansible module
- A playbook is composed of multiple plays
- A playbook declares reusable configuration management tasks,
- and is suitable as a multi-machine deployment system

---

## Using Ansible (with Vagrant)

_Install Ansible/Vagrant on the same machine (the control node)._

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

You can also build multi-machine infrastructure prototyping with a single Vagrantfile.

- For example: modeling an accurate multi-server system, like web/db servers
- Don't assume a flattened topology on a single machine is accurate to production
- Test the interfaces of a distributed system or a web server's API endpoints
- Test and prepare for disasters: crashes, dead machines, slow network, etc.

Get Vagrant from [here](https://developer.hashicorp.com/vagrant/downloads).

Ensure **VirtualBox** (or another VM provider) is installed!

```bash
# Create Vagrantfile (in project directory):
vagrant init generic/ubuntu2004

# Start the virtual machine
vagrant up

# Note the VM's IP address
vagrant ssh -c "hostname -I | cut -d' ' -f2"
```

The `SharedFoldersEnableSymlinksCreate` option is enabled.  
https://www.virtualbox.org/manual/ch04.html#sharedfolders

Or disable it: `VAGRANT_DISABLE_VBOXSYMLINKCREATE=1`

```bash
# Explore your newly created VM!
vagrant ssh

# Logout or Ctrl+D
logout

# Suspend, halt, or destroy
vagrant suspend # Resume from where you left
# Gracefully shut down the guest OS + guest machine
vagrant halt
# Remove all traces of the guest machine
vagrant destroy
```

See `Vagrantfile` and `playbook.yml` for execution details on the VM.

Check if PostgreSQL and Docker are installed:

```bash
vagrant ssh

apt list postgresql
apt list python3-psycopg2
sudo ls -al /var/lib/postgresql/12/main/pg_hba.conf
systemctl status postgresql

docker --version
```

Manually execute a playbook:

```bash
ansible-playbook -i \
    .vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory \
    database.yml
```

```bash
ansible-playbook -i \
    .vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory \
    db_users.yml
```

Check if a database and a user are present:

```bash
vagrant ssh

sudo su
su - postgres
psql ansi_db

# \du

# Logout of postgres user
logout
# Exit su
exit
# Logout of ssh
logout
```

[More](https://developer.hashicorp.com/vagrant/docs/cli) Vagrant commands:

```bash
# Equal to halt + up
vagrant reload
# State of the machines Vagrant is managing
vagrant status
# State of all active Vagrant environments on the system
vagrant global-status
```

WARNING:

```log
Using world-readable permissions for temporary files Ansible needs
to create when becoming an unprivileged user. This may be insecure.
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

todo

---

## Problems

Running a playbook like `database.yml` manually results in a big error.
Comment out the last two entries in `~/.ssh/known_hosts`, which should have
been created from this Ansible project, and then redo the `ansible-playbook -i` command.
If I remember correctly: this error would occur if the VM's metadata (like its
name) were to change, but those changes were not reflected in `~/.shh/known_hosts`.

The abridged error:

```log
WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!
Add correct host key in /home/dan/.ssh/known_hosts to get rid of this message.
```

---

## Vagrant Multibox

`sudoedit /etc/vbox/networks.conf`

Append the following:

`* 10.0.0.0/8 192.168.0.0/16 172.16.0.0/12`

Use the hostname defined in the Vagrantfile to access either box.

```bash
vagrant ssh ubuntu
vagrant ssh stream
```

---

## Ruby, YAML, Jinja

A `Vagrantfile` is actually a Ruby file.  
Notice the mode markers for `emacs` and `vim`
