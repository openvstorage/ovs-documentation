### vDisks


#### Introduction

A vDisks is a virtual disk served by Open vStorage. vDisks can be part
of a vMachine or stand-alone. By default all vDisks are snapshotted once
per hour. This snapshot schedule can not be changed for the moment.

#### vDisk Overview

The vDisk overview lists all the vDisks in the Open vStorage Cluster.

![](../../Images/vdisk_overview.png)


For each vDisk following info is displayed:

-   Name: Name of the vDisk. You can click on the name of a vDisk to see
    [more details](#vdisk_details) and execute actions.
-   vMachine: The vMachine to which the vDisk is attached.
-   vPool: The vPool the vDisks is stored on.
-   VSA: The Virtual Storage Appliance (VSA) to which the vDisk connects
    to access its storage.
-   Size: Size of the vDisk.
-   Stored Data: Total size of the current data and the Snapshots
    without the overhead imposed by the Backend redundancy.
-   Cache : The current amount of Cache Hits on the vPool by the vDisk.
-   IOPS: The current amount of IOPS delivered by the vPool to the
    vDisk.
-   Read: The current read speed of the vDisk.
-   Write: The current write speed of the vDisk.
-   FOC: Status of the FailOver Cache, a copy of the write cache of the
    vDisk, on another Storage Router to prevent dataloss. The status can be healthy
    of degraded.

### <a name="vdisk_details"></a>vDisk Details

The vDisk Details page displays the detailed performance statistics of a
single vDisk.

Following information about the vMachine is displayed on the detail
page:

-   vMachine: The vMachine to which the vDisk is attached.
-   vPool: The vPool the vDisks is stored on.
-   Storage Router: The Storage Router to which the vDisk connects to
    access its storage.
-   Size: Size of the vDisk.
-   Stored Data: Total size of the current data and the Snapshots
    without the overhead imposed by the Backend redundancy.
-   DTL: Status of the Distributed Transaction Log, a copy of the write cache of the
    vDisk, on another Storage Router to prevent data loss. The status can be healthy
    of degraded.
-   \# Snapshots: The amount of snapshots stored of the vDisks.
-   Cache : The current amount of Cache Hits on the vPool by the vDisk.
-   IOPS: The current amount of IOPS delivered by the vPool to the
    vDisk.
-   Read: The current read speed of the vDisk.
-   Write: The current write speed of the vDisk.

#### vDisk Actions

##### Rollback

Rollback the vDisk and to a previous
snapshot. This action can only be executed on stopped vMachines and the
rollback can not be undone.

##### Snapshots

At the bottom of the vDisk Details page all snapshots of the vDisk are
listed.

#### Management actions
Under Management actions you can define the settings for the VDisk:
-   Distributed Transaction Log mode: Currently you can set the DTL to on or off.
-   The Caching method: Cache on Read/Cache on Write.
-   Deduped/non-deduped policy for vDisks. In case teh setting is set to deduped it can be changed to non-deduped. Changing a vDisk from non-deduped to deduped isn't supported. It is advised to limit to the size the non-deduped vDisk can take in the read cache.
-   The SCO size: a collection of writes which gets stored on the Backend.
-   Size of the Write Buffer: the amount of data that can be in the DTL but not available in the Backend.

![](../../Images/vdiskconfigsettings.png)