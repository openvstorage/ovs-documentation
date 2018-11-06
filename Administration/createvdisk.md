### Create a vDisk on a vPool
A vDisks is a virtual disk served by Open vStorage. Open vStorage supports multiple ways to create vDisks:
* [KVM](#kvm)
* [QEMU](#qemu)
* [Block Device (Blktap)](#block-device)
* [Docker](#docker)
* [iSCSI](#iscsi)

> #### Info::Maximum vDisk size
>
> Currently the size of a vDisk is limited to 64TB.


#### <a name="kvm"></a>KVM
To create a vDisk you can use with KVM/libvirt based upon an existing qcow2 file execute following:
```
qemu-img convert -O raw /path/to/disk.qcow2 /mnt/<vpool_name>/<vdisk_name>.raw
virt-install --connect qemu:///system -n <vmachine_name> -r 512 --disk /mnt/<vpool_name>/<vdisk_name>.raw device=disk
    --noautoconsole --graphics vnc,listen=0.0.0.0 --vcpus=1 --network network=default mac=RANDOM model=e1000 --import
```

#### <a name="qemu"></a>QEMU
Open vStorage supports QEMU without libvirt. With this implementation IO bypasses the FUSE layer and removes the roundtrips from user to kernel space. Disks will pop up in the FUSE layer for ease of administration.
Currently the QEMU interface only supports a single vPool.

**Prerequisites**
* Download the voldrv-dev packages
```
sudo apt-get install volumedriver-dev
```
* The QEMU interface is built on top of the Shared Memory Server inside the Volume Driver. This Shared Memory Server is only supported as of Volume Driver 5.4. By default the Shared memory Server is disabled. To enable it, update the vPool json (`ovs config edit ovs/vpools/<vpool guid>/hosts/<vrouter id>/config`) and add under `filesystem` an entry  `"fs_enable_shm_interface": true,`. After adding the entry, restart the Volume Driver for the vPool (`restart ovs-volumedriver_<vpool_name>`).
* QEMU needs to be built from the source. You can download the source [here](https://github.com/openvstorage/qemu).

```
git clone https://github.com/openvstorage/qemu.git
cd qemu/
./configure
make
sudo make install
```

There are 2 ways to create a QEMU vDisk:
```
qemu-image create openvstorage:volume 10G
```
Alternatively create the disk in FUSE and start a VM by using the Open vStorage block driver.
```
truncate -s 10G /mnt/<vpool_name>/volume
qemu -drive file=openvstorage:volume,if=virtio,cache=none,format=raw ﻿...
```

#### <a name="block"></a>Block device
Open vStorage supports block devices as vDisks through [Blktap](http://wiki.xenproject.org/wiki/Blktap). Each blktap device corresponds to a single vDisk.
Do note: blktap is no longer the primary way to access a vDisk. Use our other interfaces wherever possible!

**Prerequisites**
* Blktap is only supported as of Volume Driver 5.4.
* The Block Device interface is built on top of the Shared Memory Server inside the Volume Driver. By default the Shared memory Server is disabled. To enable it, update the vPool json (`ovs config edit ovs/vpools/<vpool guid>/hosts/<vrouter id>/config`) and add under `filesystem` an entry  `"fs_enable_shm_interface": true,`. After adding the entry, restart the Volume Driver for the vPool (`restart ovs-volumedriver_<vpool_name>`).

To create a block device:
* Download and install the kernel module and userspace utils for Fedora 22 or CentOS respectively. For Ubuntu, the kernel module can be installed:
```
sudo apt-get install blktap-dkms
sudo apt-get install blktap-openvstorage-utils
```
* Load the kernel module by executing `sudo modprobe blktap`
* Create the block device by executing `tap-ctl create -a openvstorage:<volume_name>`. Almost instantly a new device will pop up under `/dev`.

#### <a name="docker"></a>Docker containers
Open vStorage supports Docker containers as vDisks through [Flocker](https://clusterhq.com/flocker/introduction/) and [Blktap](http://wiki.xenproject.org/wiki/Blktap). Currently our Flocker Plugin only supports a single vPool.

**Prerequisites**
* The Flocker Plugin is only supported as of Volume Driver 5.4.
* The Flocker integration is built on top of the Shared Memory Server inside the Volume Driver. By default the Shared memory Server is disabled. To enable it, update the vPool json (`ovs config edit ovs/vpools/<vpool guid>/hosts/<vrouter id>/config`) and add under `filesystem` an entry  `"fs_enable_shm_interface": true,`. After adding the entry, restart the Volume Driver for the vPool (`restart ovs-volumedriver_<vpool_name>`).

Install Blktap:
* Download and install the kernel module and userspace utils for Fedora 22 or CentOS respectively. For Ubuntu, the kernel module can be installed:
```
sudo apt-get install blktap-dkms
sudo apt-get install blktap-openvstorage-utils
```
* Load the kernel module by executing `sudo modprobe blktap`.

Install Flocker, see [the Flocker website](https://docs.clusterhq.com/en/1.9.0/install/index.html).

Install the Flocker Plugin:

* Install using python
**Be sure to use /opt/flocker/bin/python as this will install the driver into the right python environment.**
```
git clone https://github.com/openvstorage/openvstorage-flocker-driver
cd openvstorage-flocker-driver/
sudo /opt/flocker/bin/python setup.py install
```
* Install using pip
**Be sure to use /opt/flocker/bin/pip as this will install the driver into the right python environment.**
```
git clone https://github.com/openvstorage/openvstorage-flocker-driver
cd openvstorage-flocker-driver/
/opt/flocker/bin/pip install openvstorage-flocker-driver/
```

Configure the Flocker Plugin:
* Add the following section to the file `/etc/flocker/agent.yml`:
```
"dataset":
    "backend": "openvstorage_flocker_plugin"
    "vpool_conf_file": "arakoon://config/ovs/vpools/<vpool guid>/hosts/<vrouter id>/config?ini=%2Fopt%2FOpenvStorage%2Fconfig%2Farakoon_cacc.ini"
```

Create the Docker containers as usual. That's it!

#### <a name="iscsi"></a>iSCSI
Open vStorage supports exposing vDisks as iSCSI disks through [TGT](http://stgt.sourceforge.net/).

Install TGT and the Open vStorage TGT package. 
´´´
echo "deb http://apt.openvstorage.com unstable main" > /etc/apt/sources.list.d/ovsaptrepo.list
apt-get update
apt-get install tgt
apt-get install tgt-openvstorage
´´´

Create a new target device.
```
tgtadm --lld iscsi --mode target --op new --tid=1 --targetname iqn.2016-01.com.openvstorage:for.all
```

Add a logical unit (LUN)
`tgtadm --lld iscsi --op new --bstype openvstorage+tcp --mode logicalunit --tid 1 --lun 1 -b 10.100.188.31:26203/volumeName`

Replace 10.100.188.31 by the IP of the Storage Router on which the vPool is exposed containing the vDisk.
Replace 26203 by the edge port of the vPool on that Storage Router (see Storage Router detail page).
Replace volumeName by the name of the vDisk you want to expose. Note that the vDisk should already be createdas the Edge will not create the vDisk.

Execute `tgt-admin -s` to see if the vDisk is exposed. The result should be something like:

```
root@PHY-3N-188-31:~# tgt-admin -s
Target 1: iqn.2016-01.com.openvstorage:for.all
    System information:
        Driver: iscsi
        State: ready
    I_T nexus information:
    LUN information:
        LUN: 0
            Type: controller
            SCSI ID: IET     00010000
            SCSI SN: beaf10
            Size: 0 MB, Block size: 1
            Online: Yes
            Removable media: No
            Prevent removal: No
            Readonly: No
            SWP: No
            Thin-provisioning: No
            Backing store type: null
            Backing store path: None
            Backing store flags:
        LUN: 1
            Type: disk
            SCSI ID: IET     00010001
            SCSI SN: beaf11
            Size: 10737 MB, Block size: 512
            Online: Yes
            Removable media: No
            Prevent removal: No
            Readonly: No
            SWP: No
            Thin-provisioning: Yes
            Backing store type: openvstorage+tcp
            Backing store path: 10.100.188.31:26203/volumeName
            Backing store flags:
```











> ==== Info::Retrieveing the vPool guid & vrouter id
> 
> To retrieve the vPool guid you can use `ovs config list ovs/vpools` and to retrieve the vrouter id use `ovs config list ovs/vpools/<vpool guid>/hosts/`.
