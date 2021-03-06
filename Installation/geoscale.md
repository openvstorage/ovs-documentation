## GeoScale


### Introduction
It is possible to install Open vStorage across multiple datacenters. Open vStorage supports to stretch the vPool and the storage backend across multiple locations in case there is a low-latency link between the datacenters and enough bandwidth is available.
When you encounter issues or are stuck somewhere, do not hesitate to ask for help in the public [Open vStorage
Forum](https://groups.google.com/forum/#!forum/open-vstorage).

### Prerequisites
* Each datacenter should be equipped with enough SSD capacity to contain most of the active dataset of the volumes running in the datacenter.
* A layer 3 network between the different datacenters.
* Each server has the latest version of  [Ubuntu server 16.04 64 bit ISO](http://releases.ubuntu.com/16.04/ubuntu-16.04-server-amd64.iso) installed.
* In case a node has 2 or more IPs, ssh must be enabled for all the IPs.
* Make sure the `ovs` user and group are deleted before starting the Open vStorage installation.

### Cluster Topology
This guide provides a step by step approach to install a GeoScale cluster across multiple datacenters. The cluster consists out of  4 types of nodes:
* Compute hosts: these hosts run the Virtual Machines.
* Performance nodes: these hosts run the Storage Routers. They are equipped with SSDs which act as local cache layer within the datacenter.
* Capacity nodes: these hosts, across all the datacenters, form the capacity tier. They are equipped with SATA drives.
* Management (Controller) nodes: these nodes run the master services, the GUI, the API, distributed databases, the scrubbing process and the monitoring. There is one of these in each of the 3 datacenters.

More info on the topology can be found [here](../OpenvStorage/topology.md)

### OS changes
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
- Optionally, configure your OS to keep more than one crash dump per application by updating `/etc/sysctl.conf` with the following lines:
```
kernel.core_pattern=/mnt/sandboxtmp/coredumps/core-%e-%p-%t
kernel.core_uses_pid=1
```
Execute `sysctl -p` afterwards to load the settings.


### Installing the packages
The latest version of Open vStorage will be installed. For instructions to install an older version, please check [here](../olderreleases.md).


#### Controller nodes
The GeoScale cluster is spread across 3 datacenters. As a first step setup a controller node in each datacenter by execute the next steps in the shell of the controller nodes:

-   Add the repo to your sources.
    - Latest  Fargo version
	
```
echo "deb http://apt.openvstorage.com fargo-updates-1 main" > /etc/apt/sources.list.d/ovsaptrepo.list
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 4EFFB1E7

printf 'Package: *\nPin: origin apt.openvstorage.com\nPin-Priority: 1000\n' > /etc/apt/preferences

apt-get update
```
    - Latest experimental version
```
echo "deb http://apt.openvstorage.com unstable main" > /etc/apt/sources.list.d/ovsaptrepo.list
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 4EFFB1E7

printf 'Package: *\nPin: origin apt.openvstorage.com\nPin-Priority: 1000\n' > /etc/apt/preferences

apt-get update
```

-  Install the Open vStorage software:
```
apt-get install openvstorage-backend
```
-   Initialize the Open vStorage software on each controller by executing in the shell:
```
ovs setup
```

> #### Note::Concurrent installs are not supported
>
> Do not run the `ovs setup` on different nodes at the same time. Wait until the setup has finished before starting the setup on another node.

- The initialization script will ask a couple of questions:
    -   Enter the root credentials for the host.
	-   Select the Public IP address of the node.
    -   It will search for existing Open vStorage Clusters in the network.
    In case it has found a Cluster, select the option *Don't join any of
    these clusters.*.
    -   Enter a name for the Open vStorage Cluster.
    -   Select whether to use an external configuration cluster for the configuration files or if Open vStorage should setup a cluster.
    -   Indicate if the cluster is RDMA capable. All nodes in the cluster must have RDMA capable hardware in order to have a working setup..

- When the install is completed a message will be displayed.

```
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+++ Setup complete. +++
+++ Point your browser to http://<IP of the Storage Router> to start using Open vStorage +++
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
```

> #### Note::Install the Controller nodes first
>
> Install the 3 controller nodes, one in each datacenter, before continuing with the installation of the performance nodes.


#### Performance nodes
Execute the next steps in the shell of all Performance nodes:

-   Add the repo to your sources. For older versions please check [here](../olderreleases.md).
    - Latest  Fargo version
	
```
echo "deb http://apt.openvstorage.com fargo-updates-1 main" > /etc/apt/sources.list.d/ovsaptrepo.list
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 4EFFB1E7

printf 'Package: *\nPin: origin apt.openvstorage.com\nPin-Priority: 1000\n' > /etc/apt/preferences

apt-get update
```
    - Latest experimental version
```
echo "deb http://apt.openvstorage.com unstable main" > /etc/apt/sources.list.d/ovsaptrepo.list
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 4EFFB1E7

printf 'Package: *\nPin: origin apt.openvstorage.com\nPin-Priority: 1000\n' > /etc/apt/preferences

apt-get update
```

-  Install the Open vStorage software:
```
apt-get install openvstorage-hc
```
-   Initialize the Open vStorage software on each controller by executing in the shell:
```
ovs setup
```

> #### Note::Concurrent installs are not supported
>
> Do not run the `ovs setup` on different nodes at the same time. Wait until the setup has finished before starting the setup on another node.

- The initialization script will ask a couple of questions:
    -   Enter the root credentials for the host.
    -   Select the Public IP address of the Node.
    -   It will search for existing Open vStorage Clusters in the network. Join the cluster created in the previous step.
    -   Enter a name for the Open vStorage Cluster.
    -   Indicate if the cluster is RDMA capable. All nodes in the cluster must have RDMA capable hardware in order to have a working setup.

- When the install is completed a message will be displayed.

```
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+++ Setup complete. +++
+++ Point your browser to http://<IP of the Storage Router> to start using Open vStorage +++
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
```

- Next configure the ASD manager on each node:
    -   Select the public IP address to use for the ASDs.
    -   Select the start port to be used by the ASDs.
- When the ASD manager setup is completed a message will be displayed:

```
+++++++++++++++++++++++++++++++++++++
+++  ASD Manager setup completed  +++
+++++++++++++++++++++++++++++++++++++
```

#### Capacity nodes:

-   Add the repo to your sources. For older versions please check [here](../olderreleases.md).
    - Latest  Fargo version
	
```
echo "deb http://apt.openvstorage.com fargo-updates-1 main" > /etc/apt/sources.list.d/ovsaptrepo.list
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 4EFFB1E7

printf 'Package: *\nPin: origin apt.openvstorage.com\nPin-Priority: 1000\n' > /etc/apt/preferences

apt-get update
```
    - Latest experimental version
```
echo "deb http://apt.openvstorage.com unstable main" > /etc/apt/sources.list.d/ovsaptrepo.list
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 4EFFB1E7

printf 'Package: *\nPin: origin apt.openvstorage.com\nPin-Priority: 1000\n' > /etc/apt/preferences

apt-get update
```

- Execute following to setup the ASD Manager which allows to manage the disks of the capacity node.
```
apt-get install openvstorage-sdm
```
- Run the ASD Manager Setup:
```
asd-manager setup
```
-   Select the public IP address to use for the ASDs.
-   Select the start port to be used by the ASDs.

> #### Note::Arakoon config file
>
> Copy the Arakoon config file from one of other nodes (`/opt/OpenvStorage/config/arakoon_cacc.ini`) to `/opt/asd-manager/config/arakoon_cacc.ini`.

- When the ASD manager setup is completed a message will be displayed:
```
+++++++++++++++++++++++++++++++++++++
+++  ASD Manager setup completed  +++
+++++++++++++++++++++++++++++++++++++
```
- Repeat the above steps for each capacity node.

#### Compute Nodes
For this installation procedure, KVM is assumed to be the hypervisor of choice.

-   Add the repo to your sources. For older versions please check [here](../olderreleases.md).
    - Latest  Fargo version
	
```
echo "deb http://apt.openvstorage.com fargo-updates-1 main" > /etc/apt/sources.list.d/ovsaptrepo.list
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 4EFFB1E7

printf 'Package: *\nPin: origin apt.openvstorage.com\nPin-Priority: 1000\n' > /etc/apt/preferences

apt-get update
```
    - Latest experimental version
```
echo "deb http://apt.openvstorage.com unstable main" > /etc/apt/sources.list.d/ovsaptrepo.list
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 4EFFB1E7

printf 'Package: *\nPin: origin apt.openvstorage.com\nPin-Priority: 1000\n' > /etc/apt/preferences

apt-get update
```

- Install KVM and the Open vStorage QEMU package:
```
apt-get install ntp kvm libvirt0 python-libvirt virtinst
apt-get install qemu
```

- The libvirt-qemu user need to be member of the ovs group on all nodes:
```
usermod -a -G ovs libvirt-qemu
```


### Configuring the cluster
-   Open the [Open vStorage GUI](Administration/usingthegui/) on the public IP `https://<Ip of the Controller node>` of
    one of the Controller nodes and enter with the default login and password:
    admin/admin.

#### Configure the Domains
- Under Administration > Domains, create a Domain per datacenter where compute nodes are running.
- For each Storage Router (Controller and performance nodes), on the Storage Router detail page, assign the Storage Router to the correct domain by clicking the `Edit/Save` action.
- For the Recovery Domain there are 2 options:
    - Leave it empty: in this case the DTL and a slave Metadataserver will be created in the same Domain.
    - Assign a Recovery Domain: in this case there will be a local slave Metadataserver and a slave in the Recovery Domain. The DTL will also be located in the Recovery Domain.

![](../Images/setfailuredomain.png)

A Domain can only be used as a Recovery Domain entry in case ut has been used on a Storage Router as primary  Domain. The benefit of a Recovery Domain is that there is no data loss in case the primary datacenter goes offline.

#### Adding Roles to the Storage Routers
For each Storage Router select roles (write, DB, scrub) for the physical disks:
-   Select from the menu **Storage Routers** and select the Storage Router from the list.
-   Select the Physical Disk Management tab. On this tab you can assign roles to the different detected physical disks. To assign a role to a disk click the gear icon and select the role from the dropdown.
-   Assign a DB role to one of the SSDs. This will reserve 10% of the SSD for the database. Each Storage Router should have one disk with a DB role. Note that this role can't be removed once set.
-   Assign write role to the SSDs or PCIe flash cards you want to use as write buffer for the vPools. A Storage Router must have at least one disk with the write role assigned. The write role can only be removed in case no vPool is using them.
-   Optionally assign a scrub role to one of the disks. The scrubber is the application which does the garbage collection of snapshot data which is out of the retention. This will reserve 300 GB of space. In a cluster there must be at least 1 Storage Router with one disk with the scrubbing role. Note that this role can't be removed once set.

#### Create the Backends
Per datacenter an all flash backend using the disks of the Performance nodes will be created and an all HDD backend will be created using the HDDs of the capcity nodes. On top a geoscale backend is created which uses the HDD backends in each datacenter to store the data.

Per datacenter execute the following:
- To create the all flash backend, open the Backends page in the the Open vStorage GUI.
- Click **+ Add Backend**
    - Give the Backend a name
    - Select **Open vStorage Backend** as Backend type
    - Select **Local**
- Once the Backend is created and the Backend Detail page is available, locate the performance nodes of the datacenter and click the "Initialize all disks"-icon and once all SSD disk are initialized click the "Claim all disks"-icon.
- Edit the Backend to add the Domain tag of the datacenter.

- To create the HDD capacity backend, open the Backends page in the the Open vStorage GUI.
- Click **+ Add Backend**
    - Give the Backend a name
    - Select **Open vStorage Backend** as Backend type
    - Select **Local**
- Once the Backend is created and the Backend Detail page is available, locate the capacity nodes located in that datacenter and click the "Initialize all disks"-icon and once all HDD disk are initialized click the "Claim all disks"-icon.
- Edit the Backend to add the Domain tag of the datacenter.
- The default policy for a Backend has compression turned on and features a (5,4,8,3) and (2,2,3,4) policy. Additional presets can be added on the Preset tab.

Next, create GeoScale Backend by taking following actions:
- To create the geoscale backend, open the Backends page in the the Open vStorage GUI.
- Click **+ Add Backend**
    - Give the Backend a name
    - Select **Open vStorage** Backend as Backend type
    - Select **Global**
- Once the Backend is created and the Backend Detail page is available, link the 3 HDD backends previously created by clicking **Link Backend**.
- Edit the Backend to add the 3 datacenter tags.
- Click **Presets** tab and select **+ Add preset**. Enter a name, select whether data needs to be encrypted or/and compressed. Add at least the preset (2,1,2,1) which spread data across 3 datacenters (2 data, 1 parity) and acknowledges the write as soon as 2 datacenters are written.

> #### Note:: Backends
>
> More info on Backends can be found [here](../Administration/usingthegui/backends.md).


#### Create the first vPool
Once the Backends are correctly setup, it is time to create the first vPool.
-   Select from the menu **vPools** and click the **Add new vPool** button.

![](../Images/addnewvpool.png)

- On the first tab
    -   Enter a name for the vPool.
    -   Select the GeoScale backend previously created and a Preset from the dropdown. This Preset defines how data is stored on the backend (e.f. 3-way replication). You can add more Presets in the detail page of a [Backend](Administration/usingthegui/backends.md#presets). **Once the vPool is created, you can not select a different Preset. The policy of the Preset can be updated.**
    -   Select the Storage Router as Initial Storage Router. Click **Next** to continue.

-   On the second tab
    - Select the amount of ALBA Proxies per node. The default is 2. 
    - Select the mode you want the distributed read cache, the fragment cache, to be setup.
    - Select **Use another ALBA Backend as Fragment Cache**,  press **Reload** to load
        the available Backends. Select the all flash backend  which is located in the same datacenter  as the Initial Storage Router.

-   On the third tab
    -   Define the Distributed Transaction Log mode and Transport mode: Currently you can set the DTL to Disabled, Asynchronous and synchronous. The transport mode can be TCP or RDMA.
    -   Define how much space to reserve for the write buffer for this vPool.
    -   Select the SCO size (a collection of writes which gets stored on the Backend).
    -   Select the cluster size (blocksize of the vDisks). This setting can't be overruled.
    -   Select the default Volume Write Buffer size. This is the maximal amount of data per vDisk that can be in the DTL but not available in the Backend.

-   On the fourth tab
    -   Validate the values and click **Finish** to complete.

Additional vPools can be added to the Storage Router by executing the
same steps again.

> #### Note:: vPools
>
> More info on vPools can be found [here](../Administration/usingthegui/vpools.md).


### Create the first vDisk
To create the first vDisks, log in into one of the compute hosts and execute:
```
qemu-img convert  <disk to import>.qcow2 openvstorage+<protocol>:<ip of the performance node>:<vPool port>/<vDisk name>
```

More ways (iSCSI, Blktap, ...) to create a vDisk can be found [here](../Administration/createvdisk.md).

Some remarks:
* Protocol can be tcp or rdma.
* Replace the IP with the storage  IP of the Storage Router (performance node) which should host the vDisks.
* vPool port: to get the port of the vPool, open the Storage Router detail page and check the Edge port for the vPool listed under **Management actions**.
* vDisk name: name of the vDisks in the Open vStorage GUI.

### Setup the monitoring server
Optionally setup a server to monitor the Open vStorage cluster by following [these steps](https://openvstorage.gitbooks.io/ovs-monitoring/content/docs/deploy_with_ansible.html).

> #### Note:: Monitoring
>
> The monitoring is part of the commercial Open vStorage license. [Contact us](http://www.openvstorage.com/contact_us/) for more information.