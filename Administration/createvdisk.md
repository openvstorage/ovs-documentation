### Create a vDisk on a vPool
Open vStorage supports multiple ways to create vDisks.

#### <a name="esxi"></a>ESXi
You can create a vMachine with multiple vDisks straight from the vCenter or ESXi GUI. For more details, check the [VMware documentation](https://pubs.vmware.com/vsphere-51/index.jsp#com.vmware.vsphere.vm_admin.doc/GUID-7834894B-DD17-4D59-A9BF-A33D02478521.html). When selecting a Datastore for the disks of the VM, select the exposed vPool (NFS) as Datastore.
Currently only a single vPool is supported


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
* The QEMU interface is built on top of the Shared Memory Server inside the Volume Driver. This Shared Memory Server is only supported as of Volume Driver 5.4. By default the Shared memory Server is disabled. To enable it, update the vPool json (`/opt/OpenvStorage/config/storagedriver/storagedriver/<vpool_name>.json`) and add under `filesystem` an entry  `"fs_enable_shm_interface": true,`. After adding the entry, restart the Volume Driver for the vPool (`restart ovs-volumedriver_<vpool_name>`).
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
Open vStorage supports block devices as vDisks through [Blktap](http://wiki.xenproject.org/wiki/Blktap). Currently the block device interface only supports a single vPool.

**Prerequisites**
* Blktap is only supported as of Volume Driver 5.4.
* The Block Device interface is built on top of the Shared Memory Server inside the Volume Driver. By default the Shared memory Server is disabled. To enable it, update the vPool json (`/opt/OpenvStorage/config/storagedriver/storagedriver/<vpool_name>.json`) and add under `filesystem` an entry  `"fs_enable_shm_interface": true,`. After adding the entry, restart the Volume Driver for the vPool (`restart ovs-volumedriver_<vpool_name>`).

To create a block device:
* Download and install the kernel module and userspace utils for Fedora 22 or CentOS respectively. For Ubuntu, the kernel module can be installed:
```
sudo apt-get install blktap-dkms
```
* Load the kernel module by executing `sudo modprobe blktap`
* Create the block device by executing `tap-ctl create -a openvstorage:<volume_name>`. Almost instantly a new device will pop up under `/dev`.

#### <a name="docker"></a>Docker containers
Open vStorage supports Docker contaiers as vDisks through [Flocker](https://clusterhq.com/flocker/introduction/) and [Blktap](http://wiki.xenproject.org/wiki/Blktap). Currently our Flocker Plugin only supports a single vPool.

**Prerequisites**
* The Flocker Plugin is only supported as of Volume Driver 5.4.
* The Flocker integration is built on top of the Shared Memory Server inside the Volume Driver. By default the Shared memory Server is disabled. To enable it, update the vPool json (`/opt/OpenvStorage/config/storagedriver/storagedriver/<vpool_name>.json`) and add under `filesystem` an entry  `"fs_enable_shm_interface": true,`. After adding the entry, restart the Volume Driver for the vPool (`restart ovs-volumedriver_<vpool_name>`).

Install Blktap:
* Download and install the kernel module and userspace utils for Fedora 22 or CentOS respectively. For Ubuntu, the kernel module can be installed:
```
sudo apt-get install blktap-dkms
```
* Load the kernel module by executing `sudo modprobe blktap`

Install Flocker, see (the Flocker website](https://docs.clusterhq.com/en/1.9.0/install/index.html).



Install the Flocker Plugin:
* Install using python
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
    "vpool_conf_file": "opt/OpenvStorage/config/storagedriver/storagedriver/<vpool_name>.json"
```

Create the Docker containers as usual. That's it!