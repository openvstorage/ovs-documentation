#### The Open vStorage Interface
The interface is how a user or application (hypervisor, database, ...) can consume the storage offered by the Open vStorage cluster. One of the reasons why Open vStorage is unique in the storage market is due to the fact that these interfaces are specif and tuned for per use case.

![](OpenvStorageInterfaces.png)


##### VM-Centric Virtual File System
The VM-Centric Virtual File System is exposed as NFS (VMware) or FUSE mountpoint (KVM) and is optimized to be used by Virtual Machines. The File System is for example configured to detected newly created disks of Virtual Machines and converts these files directly in block devices. All other files are labeled as non-volumes files and are treated differently. By making the split-up the file system becomes VM-centric and allows to define storage actions at a much higher level. As an example, instead of taking a snapshot across a whole LUN, the VM-Centric Virtual File System offers functionality to take VM based snapshots. The same goes for replication, instead of replication a whole LUN, you define a replication policy per VM.

Next to offering advanced storage functionality it also offers best-in-class performance.

This interface can be best compared with a high-end SAN or All Flash Array but with additional functionality.


##### The Virtual NAS
The Virtual NAS interface offers a scalable NAS for end users. You could compare this interface with an unlimited scalable, high performance and always available NAS.

##### Block interface
Next to the VM-Centric Virtual File System interface also offers an alternative, more traditional block interface through iSCSI or iSER. This interface is typically used by application which directly tap into iSCSI storage or hyperviors which are (not yet) supported by the VM-Centric Virtual File System.

The performance of this interface is inline with the one as offered by the VM-Centric Virtual File System but offers less high-level administrative functionality.

This interface can be compared to that of a scalable, distributed iSCSI appliance.


##### Distributed DB interface
The Distributed DB interface offers a native unlimited scalable, high performance key/value interface which can be offered as DB as a Service. As it build on top of the Distributed Block layer, the high-availability features are build-in and no longer rely on the DB application to offer clustering.
This interface is still under development.
