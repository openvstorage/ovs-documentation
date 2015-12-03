### Arakoon

[Arakoon](http://arakoon.org/) is a distributed key-value store that guarantees consistency above anything else. It was created by the Open vStorage team due to a lack of existing solutions fitting our requirements, and is available as Open Source software.

Arakoon aims to be easy to understand and use, whilst at the same time taking the following features into consideration:
* Consistency: The system as a whole needs to provide a consistent view on the distributed state. This stems from the experience that eventual consistency is too heavy a burden for a user application to manage. A simple example is the retrieval of the value for a key where you might receive none, one or multiple values depending on the weather conditions. The next question is always: Why don’t a get a result? Is it because there is no value, or merely because I currently cannot retrieve it?
* Conditional and Atomic Updates: We don’t need full blown transactions (would be nice to have though), but we do need updates that abort if the state is not what we expect it to be. So at least an atomic conditional update and an atomic multi-update are needed.
* Robustness: The system must be able to cope with failure of individual components, without concessions to consistency. However, whenever consistency can no longer be guaranteed, updates must simply fail.
* Locality Control: When we deploy a system over 2 datacenters, we want guarantees that the entire state is indeed present in both datacenters. This is something we could not get from distributed hash tables using consistent hashing.
* Healing & Recovery: Whenever a component dies and is subsequently revived or replaced, the system must be able to guide that component towards a situation where that node again fully participates. If this cannot be done fully automatically, then human intervention should be trivial.
* Explicit Failure: Whenever there is something wrong, failure should propagate quite quickly.

### Where is Arakoon used in Open vStorage?
Arakoon is as database for various components of the Open vStorage cluster.
* The [Framework](../Framework/README.md) model is stored in the DB named **ovsdb**. It stores the details or Storage Routers, vDisks, vMachine, vPools.
* The [Volume Driver](../VolumeDriver/README.md) model is stored in the DB named **voldrv**. It stores the Volume Driver cluster config, the metadata of the File System (mapping from /path/to/file -> object ID (volumedriver or filedriver object) exposed by the Volume Driver, the Object Registry - the location of objects in the cluster (which VolumeDriver in the cluster runs ("owns") the file/volume), the family tree of a volume (clone-parent relation), replication , distributed locking and scrub management.
* The metadata of the [ALBA backend](../ALBA/README.md) are stored in a ABM DB and multiple NSM DBs. The NSM DBs (NameSpace Manager DBs) contain the meta of each stored fragment (ASD, manifest). A single NSM DB can contain data for different namespaces (volumes). The ABM DB (ALBA Manager) contains which NSM DB contains the metadata for which namespace/volume.

### An Arakoon deployment
An Arakoon cluster consists of a small set of nodes that contain the full range of key-value pairs. Each node carries all the data, yet one node is assigned to be the master node.

The master node manages all the updates for all the clients. Clients only talk to the master node, but may contact other nodes to discover who the master node is; they do so because nodes can assume different roles.

The diagram below shows 3 clients connected to an Arakoon cluster that consists of 3 Arakoon nodes.
![](../Images/ArakoonDeployment.png)

### Arakoon config files
The Arakoon config files for Open vStorage are stored under **/opt/OpenvStorage/config/arakoon/database_name/**. See the [Config section](../../Administration/Configs/arakoon.md) for more details.

### Arakoon Log files
The log files for the different Arakoon databases can be found under **/var/log/arakoon**.

#### Basic Arakoon commands
##### Start an Arakoon cluster
```
/opt/OpenvStorage/ovs/extensions/db/arakoon/ArakoonManagement.py --start --cluster <clustername>
```

##### Starting a specif node Node
To start a specific node (nodename) from a cluster execute
```
/usr/bin/arakoon -daemonize -config /root/cfg/my_cluster.cfg --node nodename
```

##### Stop an Arakoon cluster
```
/opt/OpenvStorage/ovs/extensions/db/arakoon/ArakoonManagement.py --stop --cluster <clustername>
```

### Check if an Arakoon cluster has a master
An Arakoon cluster always has one master node. Arakoon uses Multi-Paxos to decide which node becomes the master node. In the below code sample we will check if the ovsdb is functioning correctly and has a master node selected.
```
/usr/bin/arakoon --who-master -config /opt/OpenvStorage/config/arakoon/ovsdb/ovsdb.cfg
```
The result will be the id of the selected master node.

### Grow the cluster
When you intend to grow your cluster, it might be interesting to clone the existing node prior to changing the cluster configuration. Once the cluster configuration is changed to two nodes, both nodes need to accept an update before it will be accepted. If the first node contains a lot of data, the second node must perform a large catchup operation (to make it in sync with the original node). To minimize the catchup window, you can clone the first node in the cluster before the configuration is changed. This way the catchup procedure is limited to the updates that were added since the moment of cloning.

To add a clone, add a new node to the configuration file and start it this way:
```
arakoon -config /root/cfg/my_cluster.cfg --node extranode -catchup-only
```
**Important: Make sure that the node (extranode) is added to the cluster parameter in the configuration file.**

When the cloning is finished, you can start the new node as a normal node.