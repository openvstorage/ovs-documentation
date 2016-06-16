# Known Issues


The Known Issues lists the most common and general issues with the Open
vStorage software which are known but not yet addressed in the current
release. As they are known, there is no need to [create a
bug](https://www.openvstorage.org/contribute/) for these issues in the [GitHub bug
tracker](https://github.com/openvstorage/openvstorage/issues).

Feel free to solve any of these issues. You can find more information on
how to contribute to Open vStorage [here](https://www.openvstorage.com/contribute/).

## VMware ESXi
### Single vPool only
The current release of Open vStorage supports only one single vPool on ESXi.

### NFS mount issues

When the NFS mount was inaccessible for a while (f.e. due to a reboot of
the Storage Router) and is remounted, afterwards all Virtual Machines
which are powered off remain in inaccessible state. The solution to this
is to restart the management agents.

Login via ssh to the ESXi hypervisor:

```
/etc/init.d/hostd restart
/etc/init.d/vpxa restart
```

The issue should be resolved in [VMware ESXi 5.1 Update
1](https://www.vmware.com/support/vsphere5/doc/vsphere-esxi-51u1-release-notes.html).

### Storage Router freeze

In case the Storage Router freezes but the ESXi is still running, mark
the Storage Router offline in the Storage Router detail page and
shutdown the ESXi. Move the vMachine to another ESXi host and restart
the original ESXi and GSR. Mark the Storage Router as online and migrate the
vMachines back to ESXi.

## vStorage Driver

### After a clean boot the vStorage Driver will not start in case the ESXi Node is protected by HA

In case an ESXi Node is part of an HA Cluster and it is (re-)booted, the
vStorage Driver will fail to start as the vPool mountpoint isn't empty.
The VMware HA agent writes a folder .vSphere-HA in the directory of the
vPool while it isn't started yet. This prevents the vStorage Driver to
start.

*Work around:*

-   After booting the ESXI Node, execute for every vPool:

```
mount -l | grep /mnt/<name of the vPool>
```

-   Execute the following for each vPool which is *NOT* running. (The
    above command will give no output/result when the vPool isn't
    running.)

```
rm -rf /mnt/<name of the vPool> /* Note that this can take quite some time
start ovs-volumedriver_<name of the vPool>
```

Executing the above commands on a running vPool will delete all
vMachines on that vPool!

### -flat.vmdk files have metadata timestamps set to 0

The metadata timestamps (ctime (creation time), atime (access time) and
mtime (modification time)) are not supported for volume files at the
moment. An example:

```
-rw------- 0 root root 34359738368 Jan  1  1970 Hard disk 1-flat.vmdk
```

### The vStorage Driver may leak backend storage in non-happy path scenario's

Some scenarios can result in objects on the Storage Backend namespace
(filesystem / S3 / ...) for a particular volume, which are not
referenced/needed anymore by that volume. These object can be considered
garbage, but the vStorage Driver currently does not have a automated or
manual garbage collection policy to remove these objects.

In normal operation the vStorage Driver will not leave garbage on the
backend. Storage leaks can occur if

-   a scrubbing action fails before the apply scrub is executed, e.g.
    due to
    -   not enough free space available to proceed with the scrub
    -   unavailability of the storage backend
    -   power loss of the node on which the scrubber is running
-   an aborted rollback action of teh volume (e.g. due to power loss)

### System time resets / ntp time adjustments
The vStorage Driver might fail in case large time adjustments occur to the system time. It is advised to perform small changes all the time instead of doing a big time jump.

## Volumes
### Rename of a volume is not supported

The NFS storage offered by Open vStorage is currently not POSIX
compliant. Due to performance reasons the renaming of a volume is not
supported.

Other unsupported file operations on volumes files (-flat.vmdk files)
include (sym)link, chown and extending the file attributes.

### Volume migration might lead to IO timeouts

With Open vStorage the storage follows the compute as much as possible.
The data/volume will follow the VM (if possible) in that IO coming in on
a node triggers an automatic migration of a volume to that new node.
During this operation, ongoing and new IO requests are suspended and
resumed once the volume is up-and-running again on the new node.

A migration call consists of:

-   syncing all data to the backend on the old node
-   stopping the volume on the old node
-   restarting the volume on the new node

Syncing the data to the Storage Backend and restarting the volume can
take quite a long time, which might lead to timeouts at the upper IO
layers.

### Strange behavior of df -h

Executing df -h on the Storage Router will return incorrect values for
the size, free and used space of a vPool mountpoint. For each vPool the
used space will be 0% and the free space and total size will be 64TB.

### vDisks bigger than 2TB are not supported

Currently vDisks bigger than 2TB are not supported. This is both a
limitation of the vStorage Driver and the NFS implementation used by
VMware.

## Snapshots

Open vStorage will automatically take a snapshot of each vDisks every 15
minutes. Taking additional snapshot through the OpenStack GUI is
supported. Taking snapshots through the VMware GUI (ESXi, vSphere) or by
means of 3rd party applications (Veeam, ...) is not supported.

## vTemplate

Once a vMachine is turned into a vTemplate, you can not add, change or
remove any vDisks of the vTemplate. Making any changes to the vDisks
will break the clone functionality of the vTemplate. You are allowed to
change the non-vDisk info such as CPU and memory.

### No protection against deleting a vTemplate in ESXi

Currently there is no protection against a vTemplate being removed
through the vSphere GUI. In case there are clones based upon this
vTemplate and the vTemplate is deleted and accidentally the vTemplate is
removed in the vSphere GUI, execute the following.

-   Create a new Virtual Machine and attach the vTemplate disks (which
    are not removed) to this VM.
-   Set the Virtual Machine as vTemplate in the Open vStorage GUI.

### Creating multiple clones at once from a vTemplate

In case you deploy multiple Virtual Machines at once from a vTemplate
with data on its disks, some of the clone actions might fail due to
timeouts.

## vPools

### vPool naming

-   Do not use capital letters in vPool names.
-   Do not store on a storage backend vPools from different environments
    with the same vPool name. When deleting the vPool on one
    environment, the other vPools, on the other environments, will
    become unavailable!

### Removing a vPool

-   Removing a vPool only works in case all Storage Routers are online. You will be able to start the remove action in the GUI but the remove will fail.

### Re-adding a vPool on OpenStack Cinder fails for Juno and Kilo.

Due to a [bug](https://bugs.launchpad.net/cinder/+bug/1478929) in Cinder it is not possible to add a vPool with the name of a vPool which was previously deleted. The issue only occurs on Juno and Kilo but not on Liberty.

### Re-adding a vPool on OpenStack Cinder fails in case the manual changes were not undone.

The issue is caused by entries which are manually added when setting up
the vPool for use with Cinder the first time. Remove the Cinder vPool,
manually edit /etc/nova/nova.conf and remove the instances\_path. Next
restart the Nova services and follow the normal steps to add the vPool.

## OpenStack Evacuate
Due to a bug in OpenStack, the call to Evacuate a host when it is down goes to the host which is down. There is a manual work around which needs to be executed for every volume you want to evacuate.

Before clicking Evacuate host update the cinder DB:
```
mysql -u root -p <password> -e "update volumes set host='<host you want to evacuate to>@<vpoolname>#<vpoolname>' where display_name='<volume you want to evacuate>';"
```

Running Evacuate after updating the Cinder DB is now successful as the call to cinder-volume is routed to a host which is alive.

## SSD failure
In case an SSD of a host fails, there might be some consequences based upon the role of the failed SSD:
* Read role: performance loss can be expected.
* DB, write role: shutdown the node and move all VMs from the host. The host will need to be replaced and once repaired added again.
* Scrub: no impact on running VMs.

## Assigning roles in parallel
Assigning roles to a Storage Router sometimes fails in case multiple roles are set at the same time.