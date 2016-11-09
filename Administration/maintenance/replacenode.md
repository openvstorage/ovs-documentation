### Replace an Open vStorage node

In this example we have a cluster of 3 ovs nodes with active vpools running on them:
```
10.100.199.151 - ovs-node01
10.100.199.152 - ovs-node02
10.100.199.153 - ovs-node03
```

Suddenly `10.100.199.151 - ovs-node01` goes down due:
 - to a hardware failure
 - reforming existing architecture

Important to know: the node has to be shutdown to be removed from OVS.

#### OVS node removal
* We know that `10.100.199.151 - ovs-node01` went down and is completely unreachable.
```
root@ovs-node2:~# ping 10.100.199.151
PING 10.100.199.151 (10.100.199.151) 56(84) bytes of data.
--- 10.100.199.151 ping statistics ---
2 packets transmitted, 0 received, 100% packet loss, time 1008ms

root@ovs-node2:~# ssh root@10.100.199.151
ssh: connect to host 10.100.199.151 port 22: No route to host
```

Go to the console of node `10.100.199.152 - ovs-node02` and type: `ovs remove nodes 10.100.199.151`

* Result:
```
root@ovs-node2:~# ovs remove node 10.100.199.151

+++ Remove node started +++


WARNING: Some of these steps may take a very long time, please check the logs for more logging information


Following nodes with IPs will be removed from the cluster: ['10.100.199.151']
Creating SSH connections to remaining master nodes
  Node with IP 10.100.199.152  successfully connected to
  Node with IP 10.100.199.151  is unreachable
  Node with IP 10.100.199.153  successfully connected to
Starting removal of nodes
  Marking all Storage Drivers served by Storage Router 10.100.199.151 as offline
  Cleaning up node with IP 10.100.199.151

+++ Demoting node +++

Leaving Arakoon ovsdb cluster
Leaving Arakoon config cluster
Update configurations
Restarting master node services
Removing/unconfiguring offline RabbitMQ node
Update existing vPools
Restarting services
  [10.100.199.152] watcher-framework stopped
  [10.100.199.153] watcher-framework stopped
  [10.100.199.152] memcached restarted
  [10.100.199.153] memcached restarted
  [10.100.199.152] watcher-framework started
  [10.100.199.153] watcher-framework started

+++ Running "demote" hooks +++

Restarting services
  [10.100.199.152] watcher-framework stopped
  [10.100.199.153] watcher-framework stopped
  [10.100.199.152] memcached restarted
  [10.100.199.153] memcached restarted
  [10.100.199.152] watcher-framework started
  [10.100.199.153] watcher-framework started
Demote complete
    Removing node from model

+++ Running "remove" hooks +++

  [10.100.199.152] watcher-framework stopped
  [10.100.199.153] watcher-framework stopped
  [10.100.199.152] memcached restarted
  [10.100.199.153] memcached restarted
  [10.100.199.152] watcher-framework started
  [10.100.199.153] watcher-framework started
    Successfully removed node


+++ Remove nodes finished +++
```

For more information in how to remove a node see the [remove node section](removenode.md).


#### Adding a OVS node

* Repair the hardware of the node or replace the node with new hardware
* Reinstall the node with a new O/S
* Start the node and install the OVS packages
* Run the setup on the node: `ovs setup`
* Re-add the node to the cluster through the `ovs setup`

For more information on how to install Open vStorage see the [Installation section](../../Installation/README.md).