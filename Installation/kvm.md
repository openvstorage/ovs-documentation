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
Open vStorage can be installed on various OS. Open vStorage has been tested with [Ubuntu](#ubuntu) and CentOS(#centos). The detailed steps for these Operating Systems can be found below.

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
##### Configure Ubuntu
-   Update the root password to allow direct root login via ssh. Login
    and type
```
sudo su -
passwd
```
-   Make sure that PermitRootLogin is set to yes (instead of
    without-password) in /etc/ssh/sshd_config and restart ssh by typing
    **restart ssh**.
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
-   Configure an NTP Server
*   Install ntpd:
```
sudo apt-get install ntp
```
*   Edit /etc/ntp.conf to add/remove NTP servers. By default these servers are configured:
```
# Use servers from the NTP Pool Project. Approved by Ubuntu Technical Board
# on 2011-02-08 (LP: #104525). See http://www.pool.ntp.org/join.html for
# more information.
server 0.ubuntu.pool.ntp.org
server 1.ubuntu.pool.ntp.org
server 2.ubuntu.pool.ntp.org
server 3.ubuntu.pool.ntp.org
```
 * After changing the config file you have to reload the ntpd:
```
sudo /etc/init.d/ntp reload
```
-   You can now go to the [Install the Open vStorage software](#installovs) section.

Repeat the above steps for all nodes in the Open vStorage Cluster.


#### <a name="centos"></a>Install CentOS on all hosts



### <a name="installovs"></a>Install the Open vStorage software on all Storage Routers

There are 2 options to install Open vStorage. The first option is to
install Open vStorage and the Open vStorage Backend packages. When you
install the Open vStorage Backend packages you can use the disks inside
the host as Tier 2 storage (hyperconverged). You will need a
license to enable these packages. There is free license with
restrictions available which can be requested from the License section
under [Administration](Administration/administration.md). The second
option is to only install the Open vStorage core packages. Without the
Open vStorage Backend packages you will need to set up a separate Tier 2
Storage Backend (Swift, Ceph, GlusterFS, ...). There is no license
required when installing only the Open vStorage core packages.
**All Open vStorage nodes should have the same packages (openvstorage-hc or openvstorage) installed.**

Execute the next steps in the shell of all KVM Nodes:

#### Ubuntu
-   Add the repo to your sources
```
echo "deb http://apt.openvstorage.org chicago-community main" > /etc/apt/sources.list.d/ovsaptrepo.list
```
-  Install the Open vStorage HyperConverged or not:
*   Install Open vStorage HyperConverged
```
apt-get update; apt-get install openvstorage-hc
```
*   Install the Open vStorage packages *without* the Open vStorage Backend packages
```
apt-get update; apt-get install  openvstorage
```



#### CentOS



### Initialize the first Storage Router

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

### Apply for a free license
**This step is only required in case you installed the Open Storage
Backend packages (openvstorage-hc). Without a license you will not be able to create an Open vStorage Backend. In case you only installed the Open vStorage core
packages you can skip to the [add vPool section](#addvpool).**

Apply for a free community license in the [Administration section](Administration/administration.md).  To apply for a license fill in the
form with your name, company name, email address, telephone number and
indicate that you accept the license agreement. You will receive a license key
by email on the provided email address. Click *Add license* to activate the license. A community license is limited to 4 hosts, 16 ASDs and 49 vDisks. [Contact us](contactus) in case you need a larger license.

### Configure the Storage Router disks
-   Open the [Open vStorage GUI](Administration/usingthegui.md) on the public IP of
    the Storage Router and enter with the default login and password:
    admin/admin.
-   Select from the menu *Storage Routers* and select the newly installed Storage Router from the list.
-   Select the Physical Disk Management tab. On this tab you can assign roles to the different detected physical disks. To assign a role to a disk click the gear icon and select the role from the dropdown.
-   Assign a DB role to one of the SSDs. This will reserve 10% of the SSD for the distributed database. Each Storage Router should have one disk with a DB role. Note that this role can't be removed once set.
-   Assign a scrub role to one of the disks. The scrubber is the application which does the garbage collection of snapshot data which is out of the retention. This will reserve 300 GB of space. All storage Routers must have at least one disk with the scrubbing role. Note that this role can't be removed once set.
-   Assign the read or write role to the SSDs or PCIe flash cards you want to use as caching devices for the vPools. A Storage Router must have at least one disk with a read role assigned and one with the write role assigned. A disk can have both the read and write role assigned at the same time. The read and write role can only be removed in case no vPool is using them.

### Create an Open vStorage Backend

**This step is only required in case you installed the Open Storage
Backend packages (openvstorage-hc). In case you only installed the Open vStorage core
packages you can skip to the [add vPool section](#addvpool).**

-   Open the [Open vStorage GUI](Administration/usingthegui.md) on the public IP of
    the Storage Router and enter with the default login and password:
    admin/admin.
-   Select from the menu **Backends** and click the **Add Backend** button.
![](../Images/addbackend.png)
-   Give the Backend a name and click **Finish**. Only the letters a-z,
    numbers and dashes (not as first or last character) are allowed. A
    minimum of 3 and a maximum of 50 characters has to be maintained.
-   Once the Backend is created in the model, the page will refresh and
    the Backend Details page will be featured.
-   By default all nodes with a Storage Router are shown. Additional
    nodes can be added by clicking the **Discover** button.
-   Select the Initialize all disks icon ( ![](../Images/heartbeat.png) ) of each ASD
    Node in the list. The initialization might take a while.
-   Once all disks are *Available* (dark blue), select the Claim all
    disks icon ( ![](../Images/bullseye.png) ) of each ASD Node.
-   You can now [create a vPool](#addvpool) which uses this Backend by
    selecting Open vStorage Backend as Backend Type.
-   Optionally you can create a new [preset](Administration/preset.md). Select the <i class="fa fa-flip-vertical fa-code-fork"></i>-icon on the Backend detail page to see the default policies or create a new one.

### <a name="addvpool"></a>Create the vPool

-   In case the GUI isn't open yet, open the [Open vStorage
    GUI](Administration/usingthegui.md) on the public IP of the Storage Router and
    enter with the default login and password: admin/admin.
-   Select from the menu *vPools* and click the *Add new vPool* button.

![](../Images/addnewvpool.png)


-   Enter a name for the vPool and select the type of Storage Backend:
    -   In case *S3 compatible* is selected you will need to provide an
        access key, a secret key and connection (url or IP) and port to
        access the S3 compatible Storage Backend.
    -   In case *Ceph S3* is selected you will need to provide an access
        key, a secret key and connection (url or IP) and port to access
        the Ceph Storage Backend.
    -   In case *SWIFT S3* is selected you will need to provide an
        access key, a secret key and connection (url or IP) and port to
        access the Swift Storage Backend.
    -   In case *Distributed FS* (distributed filesystem) is selected,
        no additional info is required. Select this option in case you
        want to use an external NFS share or a distributed file system.
        Please note that the file system or share must be mounted on
        each node.
    -   In case Open vStorage Backend is selected, you can select one of
        previously created local [Backends](Administration/backends.md). Leave the **Use
        local Open vStorage Backend** checked and press **Next** to load
        the available Backends. Use this option in case you want to run
        Open vStorage hyperconverged (using the SATA disk inside the
        Storage Router as Tier 2 storage). Select a Preset from  the dropdown. This Preset defines how data is stored on the backend (e.f. 3-way replication). You can add more Presets in the detail page of a [Backend](/doc/backends). **Once the vPool is created the Preset can't be changed.**
-   Select the Storage Router as Initial Storage Router. Click **Next** to continue.

-   On the second tab
    -   Specify the Read and write Cache size to be assigned for the vPool on the Storage Router.
    -   Select the Storage IP. Use 127.0.0.1 in case of KVM or the IP in Storage Network for ESXi.

-   On the third tab
    -   Define the Distributed Transaction Log mode: Currently you can set the DTL to on or off.
    -   Select the default Caching method for vDisks.
    -   Select the default deduped/non-deduped policy for vDisks. In case the value is set to deduped, the value can be overwritten on the vDisk detail page.
    -   Select the SCO size (a collection of writes which gets stored on the Backend).
    -   Select the Write Buffer (the amount of data that can be in the DTL but not available in the Backend).

-   On the fourth tab
    -   In case you have a [Hypervisor Management Center](Administration/hmc.md) (vCenter, OpenStack) configured, you can automatically configure the vPool on the hypervisor.
    -   In case you don't have a Hypervisor Management Center configured, you will not be able to check the checkbox. Some manual actions might have to be taken to correctly configure the host to use the vPool.

-   On the fifth tab
    -   Validate the values and click **Finish** to complete.

Additional vPools can be added to the Storage Router by executing the
same steps again.

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
    the [ESXi install documentation](/doc/ESXi%20Installation).
-   Select the public IP address of the Storage Router.
-   Enter the root password of the Storage Router to exchange the
    necessary SSH keys.

When the install is completed a message will be displayed and you can
now [extend the vPool](#extendvpool ) to this Storage Router.

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
    will ask for the size of the read and write cache and suse the same Storage IP as on the first one.

{% include "finalremarks.md" %}


