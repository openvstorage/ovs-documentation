#### Distributed Block Layer

The Distributed Block Layer is the core of the Open vStorage solution. It is the layer between the [Virtual File System Layer](../VirtualFileSystem/README.md) and the [Distributed Storage Layer](../ALBA/README.md). It provides the [Read Cache](#readcache) functionality, the [Write Buffers](#writebuffer), the [Distributed Transaction Log](#DTL) and the [metadata](#metadata).

##### Location-based vs log -structured
Open vStorage is the first storage system in the world to combine the location-based storage approach and the log-structured storage approach:

* **Location-based storage** stores the exact location where the data is placed. This means it stores in the metadata for each address the exact location in storage system where the actual value is stored. The advantage of this strategy is that it is very fast for read operations as you know the exact location of the data, even if data frequently changes. The drawback is you don’t have a history: when an address gets overwritten, the location of the old value is lost as the address will contain the location of the new data. You can find this strategy in most of the storage solutions like SANs.

* **Log-structured storage** uses time to identify when data was written as it stores the writes by appending all data writes to the log as a sequence. Whenever you have new data to write, instead of finding a suitable location, you simply append it to the end of the log. The advantage is that you always have the complete history of the volume (all writes are appended) and snapshots are also very easy to implement by ending the log and starting a new log file. The drawback is after a while data gets spread across different log files (snapshots) so reads become slower. To find the latest value written for an address, you need to go from the last log file to the first to identify the last time an address was written. This process can be very time-consuming in case data was written a long time ago. A second problem is that the always append strategy can’t be followed indefinitely. A garbage collection process must be available to reclaim the space of data which is longer needed when it is for example out of the retention period.

Open vStorage combines the best of both worlds as it has great read and write performance and stores the complete history of the volumes. So easy snapshots, zero-copy cloning and other features come out of the box. Taking a single one of the above strategies was not an option as both have benefits but more importantly have drawbacks. To achieve great performance Open vStorage uses the SSDs or PCIe flash cards inside the host for caching. The cache is implemented using a location-based approach while the storage backend is implemented using a log-structured approach by aggregating writes which occurred together in time. This combined approach gives us features like unlimited zero copy snapshots, cloning, and easy replication.

#### The different components of the Volume Driver

![](../../../Images/blocklayer_components.png)


{% include "writebuffer.md" %}

{% include "DTL.md" %}

{% include "readcache.md" %}

{% include "albaproxy.md" %}

{% include "shm.md" %}
