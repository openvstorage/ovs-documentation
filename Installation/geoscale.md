## GeoScale


### Introduction
It is possible to install Open vStorage across multiple datacenters. Open vStorage supports to stretch the vPool and the storage backend across multiple locations in case there is a low-latency link between the datacenters and enough bandwidth is available.

In these type of setups the storage servers are located in different datacenters.

### Requirements
* The compute hosts should be equipped with enough SSD capacity to contain most of the active dataset.
* A layer 3 network between the different datacenters.

### Compute nodes
*  Install Open vStorage (apt-get install openvstorage-hc) as explained in the [KVM](kvm.md),[OpenStack](openstack.md) or [ESXi](esxi.md) section on the compute nodes and let all join the cluster.()


## Storage server:
* The storage server must have an OS disk and at least 3 empty disks for the storage backend.
* Setup the storage server with Ubuntu LTS 14.04.2. Make sure the storage server is in the same network as the compute host.
* Execute following to setup the program that will manage the disks of the storage server:
```
echo "deb http://apt.openvstorage.org <version as installed on the compute hosts> main" > /etc/apt/sources.list.d/ovsaptrepo.list
apt-get update
apt-get install openvstorage-sdm
```
Retrieve the automatically generated password from the config:
```
cat /opt/alba-asdmanager/config/config.json
...
"password": "u9Q4pQ76e0VJVgxm6hasdfasdfdd",
...
```
Repeat the above steps for each datacenter and every storage server.

### Add the ASD nodes to a backend
* Login into the Open vStorage GUI and go to Backends.
* Click the `add Backend` button, specify a name and select Open vStorage Backend as type. Click `Finish` and wait until the status becomes green.
* In the Backend details page of the freshly created Backend, click the `Discover` button. Wait until the storage server pops up and click on the + icon to add it. When asked for credentials use root as login (root) and the password retrieved above.
* Next follow the standard procedure to claim the disks of the storage server and add them to the Backend.

### Configure the Failure Domain
* Under Administration > Failure Domains, create a Failure Domain per datacenter where compute nodes are running.
* On the Storage Router detail page, assign the Storage Router to the correct Failure domain by clicking the `Edit/Save` action.
* For the backup Failure Domain there are 2 options:
    * Leave it empty: in this case the DTL and a slave Metadataserver will be created in the same Failure Domain.
    * Assign a Backup Failure Domain: in this case there will be a local slave Metadataserver and a slave in the backup Failure Domain. The DTL will also be located in the backup Failure Domain.

![](../Images/setfailuredomain.png)

A Failure Domain can only be used as a backup Failure Domain entry in case ut has been used on a Storage Router as primary Failure Domain. The benefit of a backup Failure Domain is that there is no data loss in case the primary datacenter goes offline.
It is advised to configure the hypervisor or the hypervisor management center so the Virtual Machines served by the Storage Router are started on a hypervisor in the Backup Failure Domain in case of a disaster.