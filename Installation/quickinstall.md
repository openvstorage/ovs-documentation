## Quick install guide

### Introduction

This section provides a quick getting started guide on how to setup Open
vStorage on your servers. Depending on the hypervisor [KVM](#kvm) or [VMware](#vmware) the steps will be different. A detailed guide on how to setup Open vStorage
can be found [in the Installation section](Installation). Note that the quick installation guide will use Ubuntu as base OS. For other Operation Systems see [Install OS](Installation/installos.md)

### <a name="kvm"></a> KVM

The quick install guide for KVM:
-   Read through the [Essentials](essentials.md) to understand what is
    supported and what not.
-   Remove all partitions and remaining data on the physical disks.
-   Install [Ubuntu server 14.04.2 64 bit
    ISO](http://releases.ubuntu.com/14.04.2/ubuntu-14.04.2-server-amd64.iso)
    on the server. Install the Open SSH server and the Virtual Machine
    Host.
-   Install the KVM packages (see below instructions) or install
    OpenStack Juno or above (See [OpenStack
    documentation](http://docs.openstack.org))
    on all nodes.
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
echo "deb http://apt.openvstorage.org fargo main" > /etc/apt/sources.list.d/ovsaptrepo.list
apt-get update
apt-get install volumedriver-no-dedup-server
apt-get install openvstorage-hc
```
    - Latest unstable version
```
echo "deb http://apt.openvstorage.org unstable main" > /etc/apt/sources.list.d/ovsaptrepo.list
apt-get update
apt-get install volumedriver-no-dedup-server
apt-get install openvstorage-hc
```
-   Next execute following command in the shell of the first KVM and
    answer the configuration questions.
```
ovs setup
```
-   Open the GUI on the IP address of the KVM node and login with the
    default administrator credentials username:admin, password:admin.
-   Go to the Storage Router detail page and select the Physical Disk Management tab. Assign a DB and scrub role to at least one SSD disk. Assign read and write roles to SSDs you want to use as cache.
-   In case you want to run Open vStorage hyperconverged, create an Open
    vStorage Backend in the Backend section.
-   To add more nodes execute in the KVM shell of every node (concurrent installations of multiple nodes isn't supported):
```
ovs setup
```
-   If you installed OpenStack, configure the Cinder Plugin.
-   Next, create your first vPool in the vPools section.
-   Feel free to [contribute](https://www.openvstorage.org/contribute/) to the Open vStorage code or
    [get help from the Open vStorage
    community](https://groups.google.com/forum/#!forum/open-vstorage).
    Keep in mind that only .raw disks are supported on KVM with Open
    vStorage.


### <a name="vmware"></a> VMware

The quick install guide for ESXi:

-   Read through the [Essentials](essentials.md) to understand what is
    supported and what not.
-   Download the [Ubuntu server 14.04.2 64 bit
    ISO](http://releases.ubuntu.com/14.04.2/ubuntu-14.04.2-server-amd64.iso)
    and the
    [deployOVS](https://github.com/openvstorage/openvstorage/blob/master/scripts/deployment/deployOvs.py)
    script.
-   On all ESXi nodes, upload the Ubuntu 14.04.2 Server ISO and the
    [deployOvs.py
    script](https://github.com/openvstorage/openvstorage/blob/master/scripts/deployment/deployOvs.py)
    to the ESXi Datastore and execute "./deployOvs.py --image
    /vmfs/volumes/datastore1/ubuntu-14.04.2-server-amd64.iso".
-   Select a Public Network and a Storage Network and follow the
    on-screen install instructions.
-   Install Ubuntu inside the Storage Router (a VM on VMware running the
    Open vStorage software) of each ESXi node. Next to the Public IP
    address, give each Storage Router the IP address in the Storage
    Network.
-   Install the Open vStorage software (Open vStorage core and backend
    packages) inside each Storage Router.
   - Latest Fargo version (For older versions please check [here](../olderreleases.md))
```
echo "deb http://apt.openvstorage.org fargo main" > /etc/apt/sources.list.d/ovsaptrepo.list
apt-get update
apt-get install volumedriver-no-dedup-server
apt-get install openvstorage-hc
```
    - Latest unstable version
```
echo "deb http://apt.openvstorage.org unstable main" > /etc/apt/sources.list.d/ovsaptrepo.list
apt-get update
apt-get install volumedriver-no-dedup-server
apt-get install openvstorage-hc
```

-   Next execute following command in the shell of the first Storage
    Router and answer the configuration questions.
```
ovs setup
```
-   Open the GUI on the IP address of the Storage Router and login with the
    default administrator credentials username:admin, password:admin.
-   Go to the Storage Router detail page and select the Physical Disk Management tab. Assign a DB and scrub role to at least one SSD disk. Assign read and write roles to SSDs you want to use as cache.
-   In case you want to run Open vStorage hyperconverged, create an Open
    vStorage Backend in the Backend section.
-   Create your first vPool in the vPools section.
-   Add the vPool as Datastore to ESXi. Enter as Server the *Storage* IP
    address of the Storage Router,as Folder /mnt/name of the vPool and
    give the Datastore the name of the vPool.
-   Create a new Virtual Machine in the vSphere GUI with vDisks on the
    vPool.
-   To add more Storage Routers, execute in the shell of every Storage Router (concurrent installations of multiple nodes isn't supported):
```
ovs setup
```
-   Extend the vPool across the newly created Storage Routers in the
    Open vStorage GUI.
-   Feel free to [contribute](https://www.openvstorage.org/contribute/) to the Open vStorage code or
    [get help from the Open vStorage
    community](https://groups.google.com/forum/#!forum/open-vstorage).

