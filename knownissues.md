# Known Issues


The Known Issues lists the most common and general issues with the Open
vStorage software which are known but not yet addressed in the current
release. As they are known, there is no need to create a
bug for these issues in the [GitHub bug
tracker](https://github.com/openvstorage/openvstorage/issues).

Feel free to solve any of these issues. You can find more information on
how to contribute to Open vStorage [here](https://github.com/openvstorage/home/blob/master/CONTRIBUTING.md).


## vStorage Driver

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
-   an aborted rollback action of the volume (e.g. due to power loss)

### System time resets / ntp time adjustments
The vStorage Driver might fail in case large time adjustments occur to the system time. It is advised to perform small changes all the time instead of doing a big time jump.

## Volumes
### Rename of a volume is not supported
Due to performance reasons the renaming of a volume is not
supported.

Other unsupported file operations on volumes files (.raw files)
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

### vDisks bigger than 64TB are not supported

Currently vDisks bigger than 64TB are not supported.

### DTL Status
The DTL status of a vDisk will be labeled green/ok in case the vDisk is configured to have no DTL.  


## vTemplate

Once a vDisk is turned into a vTemplate, you can not add or change its settings. Making any changes to the vDisks
will break the clone functionality of the vTemplate. A cloned vDisk can not be set as vTemplate.

## vPools

### vPool naming

-   Do not use capital letters in vPool names.
-   Do not store on a storage backend vPools from different environments
    with the same vPool name. When deleting the vPool on one
    environment, the other vPools, on the other environments, will
    become unavailable.

### vPool creation/extension

-   The available space for the write buffer can be incorrect displayed in the wizard. When creating the vPool, the creation might fail with `Too much space requested for WRITE cache`.
-   The mandotary DB role isn't checked in the wizard. In case there is no DB role on the Storage Router configured, the vPool creation/extension will fail. 
-   The vPool creation/extension can sometimes fail due to an issue passing the variables (write_cache size is incorrectly set to 0) from the GUI to the API. Delete the vPool from the Storage Router and retry adding the vPool.

	
### Removing a vPool

-   Removing a vPool only works in case all Storage Routers are online. You will be able to start the remove action in the GUI but the remove will fail. One option is to first remove any node which is down from the cluster before removing the vPool.


## Storage Routers
### SSD failure
In case an SSD of a host fails, there might be some consequences based upon the role of the failed SSD:
* DB: shutdown the node and move all VMs from the host. The host will need to be replaced and once repaired added again.
* Write: follow the steps as defined [here](Administration/maintenance/replacewrite.md) to replace the disk.
* Scrub: no impact on running VMs.

### Assigning roles in parallel
Assigning roles to a Storage Router sometimes fails in case multiple roles are set at the same time.


## Backends
### Creating multiple backends
Creating multiple backends at once fails and leaves the backends in a permanent Installing status.

### Software raid
Md member devices can be used as a backend disk. Selecting it as backend disk will render the md device useless.

## OpenStack
### OpenStack Evacuate

Due to a bug in OpenStack, the call to Evacuate a host when it is down goes to the host which is down. There is a manual work around which needs to be executed for every volume you want to evacuate.

Before clicking Evacuate host update the Cinder DB:
```
mysql -u root -p <password> -e "update volumes set host='<host you want to evacuate to>@<vpoolname>#<vpoolname>' where display_name='<volume you want to evacuate>';"
```

Running Evacuate after updating the Cinder DB is now successful as the call to cinder-volume is routed to a host which is alive.

### Re-adding a vPool on OpenStack Cinder fails for Juno and Kilo.

Due to a [bug](https://bugs.launchpad.net/cinder/+bug/1478929) in Cinder it is not possible to add a vPool with the name of a vPool which was previously deleted. The issue only occurs on Juno and Kilo but not on Liberty.


## vDisk Migration
### GUI hangs
During the migration of a vDisks between hosts, the GUI may hang for a short period of time.