# Open vStorage concepts

## Introduction
This section of explains some terminology and concepts used when talking about Open vStorage.

## ALBA
[Alba](https://github.com/openvstorage/alba) is the native backend in Open vStorage.  It is an object storage solution which is designed and optimized to be used with Open vStorage. A typical Open vStorage cluster will have multiple open vstorage backends defined:
* One or more local capacity ALBA backends in each datacenter which consists of the SATA drives located in the datacenter.
* One or more local performance ALBA backends in each datacenter which consists of the SSD/NVMe drives located in the datacenter. The performance backends is used as local cache for the global backend.
* One or more global backends which combine local backends from different datacenters into a backend spread across multipke datacenters.

It is possible to use ALBA as an all flash backend. It is actually a special case of the local capacity backend where SSDs are used instead of SATA drives. ALBA uses ASDs as location to effectively store data. In order to use ALBA as backend it is required that the `openvstorage-hc` package is installed.

## ASD
ALBA Storage Deamon. A process which turns a storage device (SATA, SSD, NVMe, ...) into a disk you can use in an ALBA backend. It is possible to have multiple ASDs, hence multiple processes, which expose the same device. Each process exposes (part of ) the disk over an IP and port.
The ASD manager is the software component which takes care of creating, starting and stopping the ASD processes.

## Backend
A backend is the location where data get stored. Open vStorage can use multiple types of backend (S3 compatible object stores, ALBA, ...).

For an ALBA backend you need to define a preset (policy, encryption, compression). The policy defines the resilience against failures.

## Distributed Transaction Log (DTL)
A copy of the outstanding data in the write buffer of a vDisks on another Storage Router to safeguard data against node or SSD failure.

## Domains
Open vStorage allows to group components together by means of domains. Domains are used to identify failure zones. For example Storage Routers in the same datacenter should receive the same domain tag. Components can be part of multiple domains. As an example a Storage Router could have the domain tags daatacenter1, Rack1 and PowerfeedA.
Next to Storage Routers, ALBA backends can also be labeled with a domain. This allows to quickly identify the backends which are in the same datacenter (f.e local backend and the performance backend).

## Edge
The Edge is a lightweight software component which can be installed on a Linux host. It exposes a block device (vDisk) and connects to the Storage Router across the network (TCP/IP or RDMA).

## NC-ECC
NC-ECC (Network and Clustered Error Correction Codes) is the algorithm used by Open vStorage to make sure data is securely saved across multiple datacenters. NC-ECC consists of 2 levels of presents: a global preset used by the global backend and local presets used in each local datacenter.

## Preset & Policy
ALBA backends require you to define a preset for the backend. A preset defines whether data on the backend and encrypted and compressed on the backend and how it protected, the policy. The policy is actually a hierarchy of policies.  When you create a new policy, you can  define the amount of fragments an object is split in, the amount of parity fragments, the minimal amounts of fragments before a write is executed and the maximal amount of fragments per node. In case the top policy of the hierarchy can't be met f.e. as there are not enough ASDs left, new data will be written with a lower policy. Once enough ASDs are available again the data which was written sub-optimal will be rewritten with the optimal policy.

## Storage Router
A Storage Router is the term given to a host which runs the core Open vStorage software (the Volume Driver).

## RDMA
Open vStorage supports remote direct memory access (RDMA), a low latency network protocol. If using RDMA hardware, it is important to enable RDMA during setup when asked so.

RDMA can be enabled or disabled for the complete cluster, so if RDMA is enabled, one needs to be sure that all nodes have
properly installed and configured RDMA capable hardware.

## vPool
A vPool is a Virtual Storage Pool used to deploy vDisks. a vPool can span multiple Storage Routers and connects to a single storage backend. This backend can be an S3 compatible object storage or in case the data needs to be split across multiple datacenters a global ALBA backend.