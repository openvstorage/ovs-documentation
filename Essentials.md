+++
#Don't remove title!
title = "Essentials"
+++


<a name="Introduction" class="internal-ref"></a>
### Introduction

This section should be read by everyone that wants to install or use
Open vStorage. In case the below Essentials are not followed, Open
vStorage may not function, or may not function as desired and data loss
might occur. It contains recommended/supported hardware configurations
and supported Storage Backends.

<a name="1 or 3+ nodes" class="internal-ref"></a>
### 1 or 3+ nodes

Open vStorage can be setup on a single Node or 3+ Nodes. It is not
advised to have a 2 Node setup as it can lead to split brain situations.

<a name="Hardware requirements" class="internal-ref"></a>
### Hardware requirements

-   Minimal requirements
    -   ESXi or KVM compatible hardware with 1 SATA disk for the OS and a disk for the cache (min. 100GB). In case you want to install Open vStorage Hyper-converged, at least 3 additional SATA disks are required for the Open vStorage Backend.
    -   Gigabit ethernet
    -   In case you want to use the Open vStorage Backend, the switch must support IGMP (igmp snooping and igmp snooping querying must be enabled).
-   Advised configuration
    -   ESXi or KVM compatible hardware with at least 2 SATA disks (OS). 
    -   An enterprise SSD or PCI Flash card (min. 100GB) to hold the
        database and the caches.
    -   10 GB ethernet

<a name="Supported Hypervisors" class="internal-ref"></a>
### Supported Hypervisors

-   VMware ESXi 5.1 P01 (experimental, single vPool only)
-   VMware ESXi 5.5 (experimental, single vPool only)
-   VMWare ESXi 6 (experimental, single vPool only)
-   KVM (raw disks only)

<a name="ISO files" class="internal-ref"></a>
### ISO files

Currently it is strongly advised not to store ISO files on the vPool.
Performance might be severely impacted when storing large non-volume
files on the vPool.

<a name="VMware" class="internal-ref"></a>
#### VMware

<a name="VMware unsupported" class="internal-ref"></a>
#### Unsupported VMware functionality
Currently not all functionality as offered by the VMware hypervisor is supported. Following features are not supported:

-   Snapshots taken through the VMware interface
-   Distributed Power Management
-   Storage Replication Adapter
-   VMware storage policy

<a name="Swap files" class="internal-ref"></a>
#### Swap files

ESXi creates by the default the vMachine memory swap file within the
vMachine directory \<vPool Datastore\>/\<VM Name\>. This means this big
swapfile ends up on the distributed filesystem mountpoint which is being
synchronised between the different GSR's. For the swap file this is not
required. All functionality is still available when relocating this type
of files to a Datastore which is local to your hypervisor node (f.e.
Datastore1).

How to change this default behavior can be seen in the screenshots below
for the vSphere Client and the vSphere Web Client.

![](images/swapfile\_vsphere\_client.png)
![](images/swapfile\_vsphere\_web.png)

<a name="OS for the VM Storage Router" class="internal-ref"></a>
### OS for the VM Storage Router

The Storage Router has been tested with Ubuntu 14.04.2 64 bit as
Operating System. Other Linux OS (linux-image >= 3.15) might also work. The Open vStorage
software is not installable on a 32 bit OS.

<a name="Limit to the amount of vDisks/Storage Router" class="internal-ref"></a>
### Limit to the amount of vDisks/Storage Router

<a name="RAM Limitation" class="internal-ref"></a>

#### RAM Limitation

The amount of RAM assigned to a Storage Router limits the amount of
vDisks which can be served by the Storage Router. The framework and
vStorage Driver need between 4 and 6GB to function. Each additional
vDisks requires 100MB. This means that on a Storage Router with 16GB RAM
assigned, 100 vDisks can be served.

<a name="Space Limitation" class="internal-ref"></a>

#### Space Limitation

Assuming sufficient resources (CPU/RAM) are available in the Storage
Router, the maximum number of vDisks per Storage Router is limited to
512.

The amount of volumes per Storage Router is also limited by the amount
of cache space in the Storage Router. The maximum can be calculated as
follow:

~~~~ {.sourceCode .python}
volumes < SCOcacheNetCapacity / 80 MiB
~~~~

In this calculation SCOcacheNetCapacity = (mountpoint1\_capacity - 1GB)
+ (mountpoint2\_capacity - 1GB) + ... The mountpoint capacity (size of
the scocache\_mount\_points) can be found for each vPool in the config
file which is stored at /opt/OpenvStorage/config/voldrv\_vpools/\<name
of the vPool\>.json

~~~~ {.sourceCode .python}
"scocache": {
    "trigger_gap": "1GB",
    "scocache_mount_points": [
      {
        "path": "/mnt/cache/sco_vpool1",
        "size": "10895360KiB"
      }
    ],
    "backoff_gap": "2GB"
  },
~~~~

<a name="Supported Storage Backends" class="internal-ref"></a>
### Supported Storage Backends

-   Open vStorage Backend (ALBA)
-   (Distributed) Filesystems
-   Amazon S3 compatible object stores:
    -   The Storage Backend must support both object and bucket methods.
    -   Ceph, Swift, ...

<a name="Hostnames" class="internal-ref"></a>
### Hostnames
Open vStorage currently works IP based. When browsing to the Open vStorage GUI, please use the ip address instead of the hostname.

<a name="Installation remarks" class="internal-ref"></a>
### Installation remarks

-   All Storage Routers or KVM Hosts should have the same OS and be
    configured with the same timezone.
-   Installing a third Node when the first or second Node is offline
    doesn't work.
-   Starting the Open vStorage configuration step of a Node when the
    previous Node is not completely configured is not supported.

<a name="Getting Help" class="internal-ref"></a>
### Getting Help

Open vStorage has a large community which is more than happy to provide
advice and help when you are having issues. The community is free to
join, but keep in mid that communities only work if you give back from
time to time. You can find the Open vStorage community
[here](https://groups.google.com/forum/#!forum/open-vstorage).
