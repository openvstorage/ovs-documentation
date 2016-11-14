## Quick install guide

### Introduction

This section provides a quick getting started guide on how to setup Open
vStorage on your servers. For detailed setup instructions see the [cluster setup guide](geoscale.md).
Note that the quick installation guide will use Ubuntu as base OS, KVM as hypervisor and will run the Open vStorage cluster hyperconverged.

The quick install guide for KVM:
-   Read through the [Essentials](essentials.md) to understand what is
    supported and what not.
-   Remove all partitions and remaining data on the physical disks.
-   Install [Ubuntu server 16.04 64 bit
    ISO](http://releases.ubuntu.com/16.04/ubuntu-16.04-server-amd64.iso)
    on the server. Install the Open SSH server and the Virtual Machine
    Host.
-   Install the KVM packages.
```
sudo apt-get install kvm libvirt0 python-libvirt virtinst
```
-   Update the root password to allow direct root login via ssh.
```
sudo su -
passwd
```
-   Decrease the swapiness of the OS:
```
echo 1 > /proc/sys/vm/swappiness
echo "vm.swappiness=1" >> /etc/sysctl.conf
```
-   Update the VM dirty_background_bytes:
```
echo "134217728" > /proc/sys/vm/dirty_background_bytes
echo "vm.dirty_background_bytes = 134217728" >> /etc/sysctl.conf
```
-   Install the Open vStorage software (Open vStorage core and backend
    packages) on all KVM nodes:
    - Latest Fargo version (For older versions please check [here](../olderreleases.md))

```
echo "deb http://apt.openvstorage.com fargo main" > /etc/apt/sources.list.d/ovsaptrepo.list
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 4EFFB1E7

cat <<EOF > /etc/apt/preferences
Package: *
Pin: origin apt.openvstorage.com
Pin-Priority: 1000
EOF

apt-get update
apt-get install openvstorage-hc
apt-get install qemu

```

    - Latest unstable version

```
echo "deb http://apt.openvstorage.com unstable main" > /etc/apt/sources.list.d/ovsaptrepo.list
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 4EFFB1E7

cat <<EOF > /etc/apt/preferences
Package: *
Pin: origin apt.openvstorage.com
Pin-Priority: 1000
EOF

apt-get update
apt-get install openvstorage-hc
apt-get install qemu
```
-   Next execute following command in the shell of the first KVM and
    answer the configuration questions.
```
ovs setup
```
-   Open the GUI on the IP address of the KVM node and login with the
    default administrator credentials username:admin, password:admin.
-   Go to the Storage Router detail page and select the Physical Disk Management tab. Assign a DB and scrub role to at least one SSD disk. Assign a write role to at least one SSD you want to use as write buffer.
-   Create an Open vStorage Backend in the Backend section.
-   To add more nodes execute in the KVM shell of every node (concurrent installation of multiple nodes isn't supported):
```
ovs setup
```
-   Next, create your first vPool in the vPools section.
-   Upload a qcow2 image to one of the nodes.
-   Create the your first vDisk by executing on the node
```
qemu-img convert  <image name>.qcow2 openvstorage+tcp:/<vdisk name>
```
-   Feel free to [contribute](https://www.openvstorage.org/contribute/) to the Open vStorage code or
    [get help from the Open vStorage
    community](https://groups.google.com/forum/#!forum/open-vstorage).
    Keep in mind that only .raw disks are supported on KVM with Open
    vStorage.


