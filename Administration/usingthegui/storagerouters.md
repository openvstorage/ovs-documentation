### Storage Routers

#### Introduction

A Storage Router  is a Virtual Machine (VMware) or Host (KVM) running the
Open vStorage software. A single Storage Router can host multiple [vPools](vpools.md)
as datastore for vMachines. Each ESXi can only host a single Storage Router.

#### Storage Router Overview

The Storage Router overview lists all the Storage Routers in the Open vStorage Cluster.

![](../../Images/vsa_overview.png)


For each Storage Router following info is displayed:

-   Status: status of the Storage Router. The status can be online (green) or
    offline (red). In case the status is set to online, the Storage Router will be
    able to serve vDisks.
-   Name: Name of the Storage Router. You can click on the name of a Storage Router to see
    [more details](#details) and execute actions.
-   IP: IP address in the Public network of the Storage Router.
-   Host: Friendly name of the ESXi given during installation of the
    Storage Router. Hoover over the name to see the Public IP address of the Host.
-   Type: Type of Hypervisor.
-   RDMA Capable: Whether or not the Storage Router is RDMA capable.
-   vDisks: Amount of vDisks served by the Storage Router.
-   Stored Data: The total amount of Stored Data of all vDisks served by
    the Storage Router.
-   Cache: The current amount of Cache Hits (in percent) of all vDisks
    served by the Storage Router.
-   IOPS: The current amount of IOPS of all vDisks served by the Storage Router.
-   Read: The aggregated read speed of all vDisks served by the Storage Router.
-   Write: The aggregated write speed of all vDisks served by the Storage Router.


### <a name="details"></a>Storage Router Details

The Storage Router Details page displays the detailed performance statistics of a
single Storage Router, the actions and allows to configure vPools.

The details for a Storage Router are:

-   IP: IP address in the Public network of the Storage Router.
-   Host: Friendly name of the ESXi given during installation of the
    Storage Router.
-   Host IP: The Public IP address of the Host.
-   Type: Type of Hypervisor.
-   vPools: Amount of vPools served by the Storage Router.
-   vMachines: Amount of vMachines served by the Storage Router.
-   vDisks: Amount of vDisks served by the Storage Router.
-   Stored Data: Total size of the current data and the Snapshots
    without the overhead imposed by the Backend redundancy.
-   Cache: The current amount of Cache Hits (in percent) of all vDisks
    served by the Storage Router.
-   IOPS: The current amount of IOPS of all vDisks served by the Storage Router.
-   Read: The aggregated read speed of all vDisks served by the Storage Router.
-   Write: The aggregated write speed of all vDisks served by the Storage Router.

#### Storage Router Administration

##### vPools served by the Storage Router

See which vPools are served by the Storage Router.

#### Physical Disk Management
The Physical Disk Management tab will list all the physical disks (PCIe flash cards, SSDs and SATA drives) of the Storage Router. A physical disks can be assigned different roles:
-   DB: The DB role stores the distributed database and metadata of the volumes. The DB role must be assigned to an SSD. This will reserve 10% of the SSD for the distributed database. Each Storage Router should have one disk with a DB role. Note that this role can't be removed once set.
-   Scrub: The scrubber is the application which does the garbage collection of snapshot data which is out of the retention. This will reserve 300 GB of space. All storage Routers must have at least one disk with the scrubbing role. Note that this role can't be removed once set.
-   Read: This will allow to use the disk as read cache.
-   Write: This will allow to use the disk as write cache.

To assign a role to disk, click the gear icon and select the appropriate roles from the dropdown.
The read and write role can only be removed in case no vPool is using them. The DB and Scrub role can not be removed once assigned to a disk.

![](../../Images/physicaldiskmanagement.png)