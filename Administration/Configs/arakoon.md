#### Arakoon Config files
There are different Arakoon config files used within an Open vStorage cluster as each [Arakoon cluster](../../Internals/Arakoon/README.md) has its own config file.

The Arakoon config files for Open vStorage are stored under **/opt/OpenvStorage/config/arakoon/\<database_name\>/\<database_name\>.cfg**.
```
root@cmp02:/opt/OpenvStorage/config/arakoon# ls -als
total 0
0 drwxrwxr-x 1 ovs ovs  98 Dec  2 11:40 .
0 drwxr-xr-x 1 ovs ovs 228 Dec  2 11:23 ..
0 drwxrwxr-x 1 ovs ovs 166 Dec  2 11:32 ovs-bknd-poc01-abm
0 drwxrwxr-x 1 ovs ovs  48 Dec  2 11:32 ovs-bknd-poc01-nsm_0
0 drwxrwxr-x 1 ovs ovs  18 Dec  1 17:29 ovsdb
0 drwxr-xr-x 1 ovs ovs  20 Dec  2 11:40 voldrv
```

In the above case you can see ovsdb, voldrv and a ABM manager (for a backend named ovs-bknd-poc01) and a first NSM for that backend (ovs-bknd-poc01). There will be a ABM per created backend and multiple NSM databases. The amount of NSM databases scales automatically with every 50 created volumes in the cluster.

Inside the directory of the DB you can find the cluster configuration file. This configuration file describes the setup of the cluster and should be available on all nodes where the DB is running. The config contains the cluster ID and all nodes which are part of the Arakoon cluster.
```
root@cmp02:/opt/OpenvStorage/config/arakoon/ovsdb# ls -als
total 4
0 drwxrwxr-x 1 ovs ovs  18 Dec  1 17:29 .
0 drwxrwxr-x 1 ovs ovs  98 Dec  2 11:40 ..
4 -rw-rw-r-- 1 ovs ovs 971 Dec  1 17:50 ovsdb.cfg
```
The content of ovsdb.cfg:
 ```
[global]
cluster = JDyVRCypsKVzbXH2,cRNyY9B3hKUTQsHp,tGLpPNHEDxNOOqOZ
cluster_id = ovsdb
plugins =

[tGLpPNHEDxNOOqOZ]
tlog_compression = snappy
client_port = 26400
messaging_port = 26401
name = tGLpPNHEDxNOOqOZ
fsync = true
home = /opt/OpenvStorage/db/arakoon/ovsdb/db
ip = 172.19.5.31
log_level = info
tlog_dir = /opt/OpenvStorage/db/arakoon/ovsdb/tlogs
log_dir = /var/log/arakoon/ovsdb

[JDyVRCypsKVzbXH2]
tlog_compression = snappy
client_port = 26400
messaging_port = 26401
name = JDyVRCypsKVzbXH2
fsync = true
home = /opt/OpenvStorage/db/arakoon/ovsdb/db
ip = 172.19.5.32
log_level = info
tlog_dir = /opt/OpenvStorage/db/arakoon/ovsdb/tlogs
log_dir = /var/log/arakoon/ovsdb

[cRNyY9B3hKUTQsHp]
tlog_compression = snappy
client_port = 26400
messaging_port = 26401
name = cRNyY9B3hKUTQsHp
fsync = true
home = /opt/OpenvStorage/db/arakoon/ovsdb/db
ip = 172.19.5.33
log_level = info
tlog_dir = /opt/OpenvStorage/db/arakoon/ovsdb/tlogs
log_dir = /var/log/arakoon/ovsdb
```

A typical Arakoon config file consists out of 2 parts
* Global settings for the cluster
* Settings per node of the cluster

** Note: Modifying the config files incorrectly might result in a corrupt database and data loss.

#### Global Settings
The global section of the configuration file defines the Arakoon setup. These are the possible parameters:

* cluster: A comma-separated list with the names of the nodes in the setup
* cluster_id: The name of the cluster
* master (optional): The name of a node which must be the master of the cluster, preferred to use in combination with the preferred_master parameter.
* preferred_master (optional): True/false value. When the parameter is set to true, Arakoon uses the node, defined in the master parameter, as master when available. When it is set to false, the cluster goes down if the master node is not available.
* lease_period (optional): Set another lease period than the default value of five seconds. See also Cluster Nodes.
* readonly (optional): Allows you to set a single node Arakoon cluster in read only mode. Make sure that you restart your Arakoon cluster after changing the read-only flag!
* quorum (optional): In normal situations, the master needs half plus one acknowledgments of the nodes (see also Arakoon Deployment) before writing data in the database. By using this parameter you can set the number of acknowledgments.

#### Node Settings
Node Section
The node section of the configuration file defines each node in the Arakoon setup. These are the possible parameters:

* ip: IP address of the node
* client_port: Port via which a client connects to the node
* messaging_port: Port via which the nodes of the cluster communicate with each other. If the nodes run on different IP addresses, they can all use the same port; if for example all nodes run on localhost, then you need a port per node
* home: Location home directory of the node on the file system. The home directory must exist on the file system. It is recommended to locate the home directory on fast disks because it has a lot of random access operations
* tlog_dir (optional): Location of the node’s transaction logs on the file system. By default this is the same directory as the home location. If you locate your home directory on fast disks, you may want to reserve the fast and expensive disks to only the node database and not the transaction log file.
* log_dir (optional): Location of the node’s log files on the file system. By default this is the same directory as the home location but you may choose another location.
* log_config (optional): The log config to be used for this node.
* log_level: The level of logging on the node, possible options are:
    * fatal: contains only errors with a fatal result for the setup
    * error: contains only errors and errors with a fatal result for the setup
    * warning: contains only warnings, errors, and errors with a fatal result for the setup
    * notice: normal but significant condition
    * info: the recommended level, informational messages, for example connect/disconnect of a client
    * debug: includes all logging, only used for debugging purposes
* disable_tlog_compression (optional): A transaction log is by default compressed when it has reached its maximum of entries (100.000). If you don’t want to compress these log, set the parameter to true.