### Replace a Compute node

In this example we have a cluster of 1 ovs node (`ovs-node01`) & 2 compute nodes (`cpu-node01` and `cpu-node02`) with active vPools running on them:
```
10.100.199.151 - ovs-node01

10.100.199.152 - cpu-node01
10.100.199.153 - cpu-node02
```

Suddenly `10.100.199.152 - cpu-node01` goes down due:
 - to a hardware failure
 - reforming existing architecture

**NOTE:** the compute node must be shutdown and offline.

#### Move virtual machines to new CPU node
* Move the virtual machines who lived on `10.100.199.152 - cpu-node01` to `10.100.199.153 - cpu-node02` through your hypervisor management tool (vCenter, OpenStack)
* Start the virtual machines

#### Remove CPU node
* Remove/replace or repair the compute node

#### Re-adding a compute node

* Start the compute node
* Install the required packages on the node

For more information on how to add a compute node to an Open vStorage cluster, see the [Installation section](../../Installation/README.md).

#### Live migrate the virtual machines

* Move the virtual machines back to the new compute node