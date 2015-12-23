## Install Open vStorage on KVM

### Introduction
This section provides a detailed step-by-step guide on how to setup Open
vStorage on 3 or more nodes running the KVM hypervisor. In case you want to install OpenvStorage in combination with Openstack following the [OpenStack installation guide](openstack.md) At the end of
the guide you will be ready to deploy a Virtual Machine on the Open
vStorage Cluster. When you encounter issues or are stuck somewhere, do
not hesitate to ask for help in the public [Open vStorage
Forum](https://groups.google.com/forum/#!forum/open-vstorage). **Only
.raw disks are supported on KVM with Open vStorage.**

### What do you need to install Open vStorage on multiple nodes?
-   At least 3 KVM compliant servers. For performance reasons at least 1
    SSD or PCI-flash card (min. 100GB) is required.
-   In case you want to install Open vStorage Hyper-converged, at least 3 additional SATA disks are required for the Open vStorage Backend.
-   Per GB read cache on SSD/PCI-e flash, you will need to reserve 10 MB RAM of the Host for Open vStorage.

### Prerequisites before starting this guide:
-   Remove all partitions and remaining data on the physical disks. Execute for every disk other than the OS disk (replace x with the correct drive letter):
```
parted -s /dev/sdx mklabel gpt
```
-   Enable all C-states in the BIOS of the Hosts for optimal
    performance.

### Install the OS and KVM
Open vStorage can be installed on various OS. Open vStorage has been tested with [Ubuntu](#ubuntu) and [CentOS](#centos). The detailed steps for these Operating Systems can be found below.

#### <a name="ubuntu"></a> Ubuntu
##### Install Ubuntu on all hosts
-   Download the [Ubuntu server 14.04.2 64 bit ISO](http://releases.ubuntu.com/14.04.2/ubuntu-14.04.2-server-amd64.iso).
-   Install [Ubuntu server 14.04.2](http://releases.ubuntu.com/14.04.2/ubuntu-14.04.2-server-amd64.iso)
    on the host, select your language and select **Install Ubuntu
    Server**.
-   Confirm your language, select your location and configure your
    keyboard.
-   Configure the network of the node. Select the NIC which is connected
    to the Public port group. Enter the IP address, netmask, gateway and
    DNS servers. For the first 3 installed nodes, the GUI will be made available on the set IP address .
-   Set a hostname, create a new user (do not use ovs as username) and
    enter a password. There is no need to encrypt the home directory.
-   Select your timezone and partition the disks of the server. Write
    the changes to the disk.
-   Leave the HTTP proxy information blank and select **No automatic
    updates**.
-   Install the Open SSH server and the Virtual Machine Host.
-   Install the GRUB boot loader.
-   Finish the Ubuntu installation by selecting **<Continue>**.

![](../Images/finish_install.png)

##### Install KVM on all nodes
-   Boot the server and install additional KVM packages.
```
sudo apt-get install kvm libvirt0 python-libvirt virtinst
```

{% include "configureubuntu.md" %}
-   You can now go to the [Install the Open vStorage software](#installovs) section.

Repeat the above steps for all nodes in the Open vStorage Cluster.

{% include "installcentos.md" %}

{% include "installovs.md" %}



### <a name="initialize"></a>Initialize the first Storage Router

-   You are now ready to initialize the first Storage Router. Execute in
    the shell:
```
ovs setup
```

The initialization script will ask a couple of questions:

-   Enter the root credentials for the host.
-   It will search for existing Open vStorage Clusters in the network.
    In case it has found a Cluster, select the option *Don't join any of
    these clusters.*.
-   Enter a name for the Open vStorage Cluster.
-   Select the Public IP address of the KVM Node.
-   Select KVM as hypervisor. In case VMware is used as hypervisor, use
    the [ESXi install documentation](esxi.md).
-   Select the public IP address of the Storage Router.
-   Enter the root password of the Storage Router to exchange the necessary SSH
    keys.

When the install is completed a message will be displayed and you can
start using Open vStorage.

```
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+++ Setup complete. +++
+++ Point your browser to http://<IP of the Storage Router> to start using Open vStorage +++
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
```

{% include "registration.md" %}

{% include "configurestoragerouter.md" %}

{% include "createbackend.md" %}

{% include "addnewvpool.md" %}

### Add more nodes to the Open vStorage Cluster

Once the Open vStorage software is installed on the first node, you can
add more nodes to the cluster. This requires Ubuntu and KVM to be installed and configured on these nodes.

Execute in the KVM shell of every node (concurrent installations of multiple nodes isn't supported):

```
ovs setup
```

The initialization script will ask a couple of questions:

-   Enter the root credentials for the KVM Node.
-   It will search for existing Open vStorage Clusters in the network.
    Select the Cluster created earlier.
-   Select the Public IP address of the KVM Node.
-   Select KVM as hypervisor. In case VMware is used as hypervisor, use
    the [ESXi install documentation](esxi).
-   Select the public IP address of the Storage Router.
-   Enter the root password of the Storage Router to exchange the
    necessary SSH keys.

When the install is completed a message will be displayed and you can
now [extend the vPool](#extendvpool) to this Storage Router.

```
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+++ Setup complete. +++
+++ Point your browser to https://<IP of the KVM> to start using Open vStorage +++
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
```

Repeat the above commands for all Storage Routers you want to add to the
Open vStorage Cluster.

### <a name="extendvpool"></a> Extending a vPool across multiple nodes

-   Configure the roles for the physical disks of the Storage Routers.
-   Extend the vPool from the first node to additional nodes by going to
    the vPool detail page and selecting the nodes where you want the
    vPool to be available. Adding the vPool to another Storage Router
    will ask for the size of the read and write cache and use the same Storage IP as on the first one.

### <a name="adduser"></a> Libvirt-qemu user settings
The libvirt-qemu user need to be member of the ovs group on all nodes:
```
usermod -a -G ovs libvirt-qemu
```

{% include "finalremarks.md" %}


