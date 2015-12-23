### Create a vDisk on a vPool
Open vStorage supports multiple ways to create vDisks.

#### ESXi

#### KVM

#### QEMU
Open vStorage supports QEMU without libvirt. With this implementation IO bypasses the FUSE layer and removes the roundtrips from user to kernel space. Disks will pop up in the FUSE layer for ease of administration.
Currently the block device interface only supports a single vPool.

**Prerequisites**
* Direct QEMu support is only supported as of Volume Driver 5.4.
* The QEMU interface is built on top of the Shared Memory Server inside the Volume Driver. By default the Shared memory Server is disabled. To enable it, update the vPool json (`/opt/OpenvStorage/config/storagedriver/storagedriver/<vpool_name>.json`) and add under `filesystem` an entry  `"fs_enable_shm_interface": true,'. After adding the entry, restart the Volume Driver for the vPool (`restart ovs-volumedriver_<vpool_name>`).

There are 2 ways to create a QEMU vDisk:
```
qemu-image create openvstorage://volume 10G
```
Alternatively
```
truncate -s10G /mnt/<vpool_name>/volume
qemu -drive file=openvstorage://volume,if=virtio,cache=none,format=raw ﻿...
```

#### Block device
Open vStorage supports block devices as vDisks through [Blktap](http://wiki.xenproject.org/wiki/Blktap). Currently the block device interface only supports a single vPool.

**Prerequisites**
* Blktap is only supported as of Volume Driver 5.4.
* The Block Device interface is built on top of the Shared Memory Server inside the Volume Driver. By default the Shared memory Server is disabled. To enable it, update the vPool json (`/opt/OpenvStorage/config/storagedriver/storagedriver/<vpool_name>.json`) and add under `filesystem` an entry  `"fs_enable_shm_interface": true,'. After adding the entry, restart the Volume Driver for the vPool (`restart ovs-volumedriver_<vpool_name>`).

To create a block device:
* Download and install the Blktab driver.
* Create the block device by executing `tap-ctl create -a openvstorage:<volume_name>`. Almost instantly a new device will pop up under `/dev`.
