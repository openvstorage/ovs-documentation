This section is intended for (End-) Users who want to implement and manage Open vStorage. You can learn what is needed to setup Open vStorage, add your first Storage Backend and create your first vMachine. Once everything is setup you can use the GUI to follow up on how things are running and be amazed of the IOPS Open vStorage is giving you. In case you want to integrate your applications with Open vStorage, no problem as we have a well documented API. More acquainted with command line? No problem Open vStorage comes with an interactive python command line. Having an issue with Open vStorage? Check the Support section.

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
