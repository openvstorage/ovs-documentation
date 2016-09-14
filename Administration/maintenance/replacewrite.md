### Replace an SSD with the write role in a Storage Router

In this example we have a cluster of 3 Open vStorage nodes with an active vPool (`<vpoolname>`):
```
10.100.199.151 - ovs-node01
10.100.199.152 - ovs-node02
10.100.199.153 - ovs-node03
```

On node `10.100.199.151 - ovs-node01` we have a disk that is broken, to be more specific the SSD with the WRITE role on mountpoint `/mnt/hdd3`.
As a result the VolumeDriver has stopped serving vDisks from this node.

#### Stop the VolumeDriver and remove the broken SSD


* Stop the VolumeDriver and the vPool service: `stop ovs-volumedriver_<vpoolname>`.
* Remove the broken disk, and replace it.

#### Replacing the SSD

* Detect the new disk in the system: `df -h` or `lsblk`
* Format, partition and put a filesystem on the disk
* Detect the newly created filesystem in directory: `ls /dev/disk/by-id/ -l`
* Edit `/etc/fstab` so `/mnt/hdd3` is mapped on the new disk
* Perform command `mount -a`
* Check if the directory is mounted: `df -h | grep hdd3`
* Create directories on `/mnt/hdd3`: 
   - <vpoolname>_write_dtl_1  
   - <vpoolname>_write_fcache_1  
   - <vpoolname>_write_fd_1  
   - <vpoolname>_write_sco_1

#### FINISHING UP


* Resync the GUI its disk roles on the storage router page
* Start the VolumeDriver services:
   - ovs-dtl_<vpoolname>
   - ovs-volumedriver_<vpoolname>
* Execute `ovs monitor services` to verify all services are back up and running.
* Run the [healthcheck](https://github.com/openvstorage/openvstorage-health-check).