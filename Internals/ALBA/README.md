### ALBA
ALBA(ALternative BAckend) is a distributed object store, like Swift, Ceph and Amplistor. ALBA is highly optimised for Open vStorage and aggregates a set of OSDs like Alba's ASDs or Seagate's Kinetic Drives into a pool of storage that you can use as a storage backend for Open vStorage.

Alba supports the following high-end set of features:
* erasure coding
* x-way replication
* (optional) compression
* (optional) encryption
* partial reads
* namespaces
* [self healing](selfhealing.md) (repair, rebalancing)
* ...

Unlike most alternatives, Alba is a consistent store. Since it is optimised for the types of objects Open vStorage's volume driver needs to store and retrieve, Alba has become the default object storage backend for Open vStorage.

#### Architecture


#### OSDs, ASDs and Seagate Kinetic drives
ALBA supports 2 types of devices: Seagate Kinetic drives and ASDs. The Seagate Kinetic drives are ethernet-connected HDD with an open source object API designed specifically for hyperscale and scale-out environments. An ASD (ALBA Storage Daemon) is a our own pure software based implementation of the same concept on top of normal SATA disks: a Socket Server on top of RocksDb, offering persistence for meta data and small values, while large values reside on the file system. Every atomic multi-update is synced before the acknowledgement is returned. To have acceptable performance, the updates for multiple clients are batched and at the end of the batch, the ASD calls syncfs to sync the whole filesystem (files, directories, and rocksdb) at once. This is effectively a tradeof, increasing latency for throughput, which is perfectly acceptable for our use case (writes by the VM are already acknowledges when they hit the write buffer).

ASD’s follow the same strategy as the Kinetic drives, advertising themselves via a UDP multicast of a json config file. A single parser can be used to retrieve connection information of for ASD and Kinetic drive alike.

OSDs (Object Storage Daemon) are generic abstraction over ASDs and Seagate Kinetic drives.