## Install Open vStorage and integrate it with OpenStack

### Introduction
This section provides a detailed step-by-step guide on how to setup Open vStorage together with OpenStack.

### Architecture Overview
Open vStorage can be used as storage platform for OpenStack through the [Open vStorage Cinder Plugin](https://github.com/openvstorage/framework-cinder-plugin). This plugin allows Open vStorage to deliver block storage for Open Stack.

In contrary to DevStack, where all OpenStack functionality runs on a single node, OpenStack has split up the functionality between different nodes. A typical setup will have 3 Controller nodes. On these Controller nodes Open vStorage should not be installed. Open vStorage should only be installed on the Nova nodes. The first 3 nodes will by default be setup as master Open vStorage nodes (GUI, API, databases, …) while additional nodes will be setup as extra nodes. For Open vStorage it is essential to also run the Cinder functionality (f.e. cinder-volume) on the Nova node.

![](../Images/openstacksetup.png)

The Compute nodes should have at least 1 SSD or PCI-flash card (min. 100GB). Per GB read cache on SSD/PCI-e flash, you will need to reserve 10 MB RAM of the Host for Open vStorage. In case you want to install Open vStorage Hyper-converged, at least 3 additional SATA disks are required for the Open vStorage Backend.


### Prerequisites before starting this guide:
-   Remove all partitions and remaining data on the physical disks. Execute for every disk other than the OS disk (replace x with the correct drive letter):
```
parted -s /dev/sdx mklabel gpt
```
-   Enable all C-states in the BIOS of the Hosts for optimal
    performance.

### Install OpenStack
- Install Ubuntu server 14.04 or CentOS7 (See OS specific parameters to set [here](kvm.md))
- Install OpenStack on the controller nodes.
- Add the Compute and Block Storage service to the Compute nodes.

* Please see the [OpenStack install instructions](http://docs.openstack.org/) for detailed steps.*


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

### Register a Hypervisor Managent Center
In the Administration section, **select Hypervisor Mgmt.**. Click Add new Center and complete the form. Next indicate at the bottom which Nova nodes you want to be managed by the OpenStack Controller Node. Do not create a vPool before you have registered the Hypervisor Management Center and added the nodes.


{% include "configurestoragerouter.md" %}

{% include "createbackend.md" %}

{% include "addnewvpool.md" %}

### Add more nodes to the Open vStorage Cluster

Once the Open vStorage software is installed on the first node, you can
add more nodes to the cluster. This requires the COmpute and Block Storage service to be added these nodes.

Execute in the shell of every Compute node (concurrent installations of multiple nodes isn't supported):

```
ovs setup
```

The initialization script will ask a couple of questions:

-   Enter the root credentials for the Compute Node.
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

{% include "finalremarks.md" %}


