# Getting Started

To be able to get started with GeoScale, an [Etcd cluster](#etcd-cluster) is required and 0 to many [Arakoon clusters](#arakoon-cluster).
In this document we'll show you how to set up both.

### Before starting

<font color="red">SSH keys for `root` and `ovs` should have been exchanged between all nodes running an Etcd and/or Arakoon instance.</font>

The Etcd cluster **should be deployed** on nodes **where OVS** will **NOT** be **running**.
Additional steps are required if the Etcd cluster should be on the same nodes as where OVS will be running.

##### <a name="steps"></a>Required Steps
Etcd cluster nodes apart from Ovs nodes (**Recommended**)
1. [Create the Etcd cluster](#etcd-cluster-create)
1. [Extend the Etcd cluster](#etcd-cluster-extend) (Repeatable)
1. **Create** Arakoon cluster on node which is part of the Etcd cluster?
    * Yes:
        1. [Create the Arakoon cluster](#arakoon-cluster-create)
    * No:
        1. [Create an Etcd proxy](#etcd-proxy-create)
        2. [Create the Arakoon cluster](#arakoon-cluster-create)
1. **Extend** Arakoon cluster on node which is part of the Etcd cluster?
    * Yes:
        1. [Extend the Arakoon cluster](#arakoon-cluster-extend) (Repeatable)
    * No:
        1. [Create an Etcd proxy](#etcd-proxy-create)
        1. [Extend the Arakoon cluster](#arakoon-cluster-extend) (Repeatable)

Etcd cluster nodes mixed with Ovs nodes (**Not recommended**)
1. [Create the Etcd cluster with non-default client port](#etcd-cluster-create-non-default)
1. [Create an Etcd proxy](#etcd-proxy-create)
1. [Extend the Etcd cluster with non-default client port](#etcd-cluster-extend-non-default) (Repeatable)
1. [Create an Etcd proxy](#etcd-proxy-create) (Repeatable)
1. **Create** Arakoon cluster on node which is part of the Etcd cluster?
    * Yes:
        1. [Create the Arakoon cluster](#arakoon-cluster-create)
    * No:
        1. [Create an Etcd proxy](#etcd-proxy-create)
        2. [Create the Arakoon cluster](#arakoon-cluster-create)
1. **Extend** Arakoon cluster on node which is part of the Etcd cluster?
    * Yes:
        1. [Extend the Arakoon cluster](#arakoon-cluster-extend) (Repeatable)
    * No:
        1. [Create an Etcd proxy](#etcd-proxy-create)
        1. [Extend the Arakoon cluster](#arakoon-cluster-extend) (Repeatable)
1. [Remove the Etcd proxy](#etcd-proxy-remove)

### <a name="etcd-cluster"></a>ETCD Cluster

#### Prerequisites

1. In order to be able to set up an Etcd cluster, the [openvstorage-core package must be installed](#installovs)
1. Always execute the code on the node you wish to create / extend the cluster on. Eg: I want an Etcd cluster on node1, node2, node3
    1. Node1: On this node execute **create** cluster code
    1. Node2: On this node execute **extend** cluster code
    1. Node3: On this node execute **extend** cluster code

#### Installation

##### <a name="etcd-cluster-create"></a>Creating cluster

Execute this code on the node which should be running the Etcd instance

Open the OVS ipython shell, by typing `ovs` on commandline.
Replace the required parameters below and execute the code

```
from ovs.extensions.db.etcd.configuration import EtcdConfiguration
from ovs.extensions.db.etcd.installer import EtcdInstaller
from ovs.extensions.generic.system import System
from subprocess import check_output

cluster_name = <my_cluster_name>
ip = <local_public_ip_of_this_node>

EtcdInstaller.create_cluster(cluster_name, ip)
machine_id = System.get_my_machine_id()
EtcdConfiguration.initialize_host(machine_id)
check_output("""etcdctl set /ovs/framework/stores '{"persistent": "pyrakoon", "volatile": "memcache"}'""", shell=True)
```

##### <a name="etcd-cluster-extend"></a>Extending cluster (optional, but advised)

Execute this code on the node which should be extending the 1st Etcd instance

Open the OVS ipython shell, by typing `ovs` on commandline.
Replace the required parameters below and execute the code

```
from ovs.extensions.db.etcd.configuration import EtcdConfiguration
from ovs.extensions.db.etcd.installer import EtcdInstaller
from ovs.extensions.generic.system import System

master_ip = <public_ip_of_1st_etcd_cluster_node>
new_ip = <local_public_ip_of_this_node>
cluster_name = <my_cluster_name>

EtcdInstaller.extend_cluster(master_ip, new_ip, cluster_name)
machine_id = System.get_my_machine_id()
EtcdConfiguration.initialize_host(machine_id)
```

##### <a name="etcd-cluster-create-non-default"></a>Creating cluster with non-default client port `2378`

Execute this code on the node which should be running the Etcd instance

Open the OVS ipython shell, by typing `ovs` on commandline.
Replace the required parameters below and execute the code

```
from ovs.extensions.db.etcd.installer import EtcdInstaller

cluster_name = <my_cluster_name>
ip = <local_public_ip_of_this_node>

EtcdInstaller.create_cluster(cluster_name, ip, client_port=2378)
```

##### <a name="etcd-cluster-extend-non-default"></a>Extending cluster with non-default client port `2378` (optional, but advised)

Execute this code on the node which should be extending the 1st Etcd instance

Open the OVS ipython shell, by typing `ovs` on commandline.
Replace the required parameters below and execute the code

```
from ovs.extensions.db.etcd.installer import EtcdInstaller

master_ip = <public_ip_of_1st_etcd_cluster_node>
new_ip = <local_public_ip_of_this_node>
cluster_name = <my_cluster_name>

EtcdInstaller.extend_cluster(master_ip, new_ip, cluster_name, client_port=2378)
```

#### Validation

To verify the Etcd cluster has been set up properly, you can do the following on all nodes on which the Etcd cluster was created and/or extended

##### Etcd with default client_port `2379`

```
root@node1:~# etcdctl cluster-health
member 735ec357654e8bc5 is healthy: got healthy result from http://x.x.x.1:2379
member 1a8245d01c989a15 is healthy: got healthy result from http://x.x.x.2:2379
cluster is healthy
```

##### Etcd w/o default client_port, port `2378` instead

```
root@node1:~# etcdctl --peers=<my_etcd_server_ip>:2378 cluster-health
member 735ec357654e8bc5 is healthy: got healthy result from http://x.x.x.1:2378
member 1a8245d01c989a15 is healthy: got healthy result from http://x.x.x.2:2378
cluster is healthy
```

### ETCD Proxy

#### Prerequisites

Setting up an Etcd proxy is only mandatory in certain use-cases. Please check the [Required Steps](#steps) to validate whether this is required.

##### <a name="etcd-proxy-create"></a>Creating proxy

Execute this code on the node which should be running the Etcd proxy instance

Open the OVS ipython shell, by typing `ovs` on commandline.
Replace the required parameters below and execute the code

```
from ovs.extensions.db.etcd.configuration import EtcdConfiguration
from ovs.extensions.db.etcd.installer import EtcdInstaller
from ovs.extensions.generic.system import System
from subprocess import check_output

cluster_name = <my_etcd_cluster_name>
proxy_name = <my_proxy_service_name>
master_ip = <public_ip_of_1st_etcd_cluster_node>
slave_ip = <local_public_ip_of_this_node>

external = '{0}=http://{1}:2380'.format(cluster_name, master_ip)
EtcdInstaller.use_external(external, slave_ip, proxy_name)
machine_id = System.get_my_machine_id()
EtcdConfiguration.initialize_host(machine_id)
check_output("""etcdctl set /ovs/framework/stores '{"persistent": "pyrakoon", "volatile": "memcache"}'""", shell=True)
```

##### Validation of the Proxy

To verify the Etcd proxy has been set up properly, you can do the following on the node which is running the proxy

```
root@node1:~# etcdctl cluster-health
member 735ec357654e8bc5 is healthy: got healthy result from http://x.x.x.x:2379
cluster is healthy
```

##### <a name='etcd-proxy-remove'></a>Removing the Etcd proxy

Execute this code on the node where the Etcd proxy should be removed<br>
<font color="red">Only execute this on nodes which have an Etcd server instance running AND where you want to install OVS</font>

Open the OVS ipython shell, by typing `ovs` on commandline.
Replace the required parameters below and execute the code

```
from ovs.extensions.db.etcd.installer import EtcdInstaller

proxy_name = <my_local_proxy_name>
ip = <local_public_ip_of_this_node>

EtcdInstaller.remove_proxy(proxy_name, ip)
```

##### Validation of the Proxy removal

To verify the Etcd proxy has been removed properly, you can do the following on the node where the proxy was removed


### <a name="arakoon-cluster"></a>Arakoon Cluster

#### Prerequisites

In order to be able to set up an Arakoon cluster, the [openvstorage-core package must be installed](KVM.md#installovs)

Always execute the code on the node you wish to create / extend the cluster on
Eg: I want Arakoon clusters on node1, node2, node3
* Node1: On this node execute **create** cluster code
* Node2: On this node execute **extend** cluster code
* Node3: On this node execute **extend** cluster code

#### Information

OVS supports 4 types of Arakoon clusters
* SD
* FWK
* ABM
* NSM

###### SD
* Amount: 1
* Usage: StorageDriver
* Limitation: If not provided, OVS will create it

###### FWK
* Amount: 1
* Usage: Framework
* Limitation: If not provided, OVS will create it

###### ABM
* Amount: Equal to amount of ALBA backends
* Usage: [ALBA backend](KVM.md#createbackend)
* Limitation: If not provided, OVS will create 1 per ALBA backend

###### NSM
* Amount: Equal to or higher than amount of ALBA backends
* Usage: [ALBA backend](#createbackend)
* Limitation: If not provided, OVS will create 1 per ALBA backend to start with and can create additional clusters based on load

#### Installation

##### <a name="arakoon-cluster-create"></a>Creating cluster FWK (Optional)

Open the OVS ipython shell, by typing `ovs` on commandline.
Replace the required parameters below and execute the code

```
from ovs.extensions.db.arakoon.ArakoonInstaller import ArakoonInstaller
from subprocess import check_output

cluster_name = <my_framework_arakoon_cluster_name>
ip = <local_public_ip_of_this_node>
base_dir = <absolute_path_where_db_should_be_stored>

ArakoonInstaller.create_cluster(cluster_name, 'FWK', ip, base_dir, locked=False, internal=False)
check_output("""etcdctl set /ovs/framework/arakoon_clusters '{{"ovsdb": "{0}"'}}""".format(cluster_name), shell=True)
check_output('service ovs-arakoon-{0} start'.format(cluster_name), shell=True)
```

##### <a name="arakoon-cluster-extend"></a>Extending cluster FWK (Optional)

Open the OVS ipython shell, by typing `ovs` on commandline.
Replace the required parameters below and execute the code

```
from ovs.extensions.db.arakoon.ArakoonInstaller import ArakoonInstaller

cluster_name = <my_framework_arakoon_cluster_name>
master_ip = <public_ip_of_1st_arakoon_cluster_node>
new_ip = <local_public_ip_of_this_node>
base_dir = <absolute_path_where_db_should_be_stored>
current_ips = [<public_ip_of_1st_arakoon_cluster_node>, <public_ip_of_2nd_arakoon_cluster_node>, ...]

ArakoonInstaller.extend_cluster(master_ip, new_ip, cluster_name, base_dir, locked=False)
ArakoonInstaller.restart_cluster_add(cluster_name, current_ips, new_ip)
```

##### Creating cluster SD (Optional)

Open the OVS ipython shell, by typing `ovs` on commandline.
Replace the required parameters below and execute the code

```
from ovs.extensions.db.arakoon.ArakoonInstaller import ArakoonInstaller
from subprocess import check_output

cluster_name = <my_storagedriver_arakoon_cluster_name>
ip = <local_public_ip_of_this_node>
base_dir = <absolute_path_where_db_should_be_stored>

ArakoonInstaller.create_cluster(cluster_name, 'SD', ip, base_dir, locked=False, internal=False)
check_output('service ovs-arakoon-{0} start'.format(cluster_name), shell=True)
```

##### Extending cluster SD (Optional)

Open the OVS ipython shell, by typing `ovs` on commandline.
Replace the required parameters below and execute the code

```
from ovs.extensions.db.arakoon.ArakoonInstaller import ArakoonInstaller

cluster_name = <my_storagedriver_arakoon_cluster_name>
master_ip = <public_ip_of_1st_arakoon_cluster_node>
new_ip = <local_public_ip_of_this_node>
base_dir = <absolute_path_where_db_should_be_stored>
current_ips = [<public_ip_of_1st_arakoon_cluster_node>, <public_ip_of_2nd_arakoon_cluster_node>, ...]

ArakoonInstaller.extend_cluster(master_ip, new_ip, cluster_name, base_dir, locked=False)
ArakoonInstaller.restart_cluster_add(cluster_name, current_ips, new_ip)
```

##### Creating cluster ABM (Optional)

WARNING: Only applicable if `openvstorage-backend` package has been installed

Open the OVS ipython shell, by typing `ovs` on commandline.
Replace the required parameters below and execute the code

Optionally repeat this code as many time as the amount of ALBA backends you wish to support, using a different `cluster_name` each time

```
from ovs.extensions.db.arakoon.ArakoonInstaller import ArakoonInstaller
from subprocess import check_output

cluster_name = <my_alba_manager_arakoon_cluster_name>
ip = <local_public_ip_of_this_node>
base_dir = <absolute_path_where_db_should_be_stored>

ArakoonInstaller.create_cluster(cluster_name, 'ABM', ip, base_dir, ['albamgr_plugin'], locked=False, internal=False)
check_output('ln -s /usr/lib/alba/albamgr_plugin.cmxs {0}/arakoon/{1}/db'.format(base_dir, cluster_name), shell=True)
check_output('service ovs-arakoon-{0} start'.format(cluster_name), shell=True)
```

##### Extending cluster ABM (Optional)

WARNING: Only applicable if `openvstorage-backend` package has been installed

Open the OVS ipython shell, by typing `ovs` on commandline.
Replace the required parameters below and execute the code

Optionally repeat this code as many time as the amount of ALBA backends you wish to support, using a different `cluster_name` each time

```
from ovs.extensions.db.arakoon.ArakoonInstaller import ArakoonInstaller
from subprocess import check_output

cluster_name = <my_alba_manager_arakoon_cluster_name>
master_ip = <public_ip_of_1st_arakoon_cluster_node>
new_ip = <local_public_ip_of_this_node>
base_dir = <absolute_path_where_db_should_be_stored>
current_ips = [<public_ip_of_1st_arakoon_cluster_node>, <public_ip_of_2nd_arakoon_cluster_node>, ...]

ArakoonInstaller.extend_cluster(master_ip, new_ip, cluster_name, base_dir, locked=False)
check_output('ln -s /usr/lib/alba/albamgr_plugin.cmxs {0}/arakoon/{1}/db'.format(base_dir, cluster_name), shell=True)
ArakoonInstaller.restart_cluster_add(cluster_name, current_ips, new_ip)
```

##### Creating cluster NSM (Optional)

WARNING: Only applicable if `openvstorage-backend` package has been installed

Open the OVS ipython shell, by typing `ovs` on commandline.
Replace the required parameters below and execute the code

Optionally repeat this code based on amount of possible ALBA backends and their load, using a different `cluster_name` each time

```
from ovs.extensions.db.arakoon.ArakoonInstaller import ArakoonInstaller
from subprocess import check_output

cluster_name = <my_namespace_manager_arakoon_cluster_name>
ip = <local_public_ip_of_this_node>
base_dir = <absolute_path_where_db_should_be_stored>

ArakoonInstaller.create_cluster(cluster_name, 'NSM', ip, base_dir, ['nsm_host_plugin'], locked=False, internal=False)
check_output('ln -s /usr/lib/alba/nsm_host_plugin.cmxs {0}/arakoon/{1}/db'.format(base_dir, cluster_name), shell=True)
check_output('service ovs-arakoon-{0} start'.format(cluster_name), shell=True)
```

##### Extending cluster NSM (Optional)

WARNING: Only applicable if `openvstorage-backend` package has been installed

Open the OVS ipython shell, by typing `ovs` on commandline.
Replace the required parameters below and execute the code

Optionally repeat this code based on amount of possible ALBA backends and their load, using a different `cluster_name` each time

```
from ovs.extensions.db.arakoon.ArakoonInstaller import ArakoonInstaller
from subprocess import check_output

cluster_name = <my_namespace_manager_arakoon_cluster_name>
master_ip = <public_ip_of_1st_arakoon_cluster_node>
new_ip = <local_public_ip_of_this_node>
base_dir = <absolute_path_where_db_should_be_stored>
current_ips = [<public_ip_of_1st_arakoon_cluster_node>, <public_ip_of_2nd_arakoon_cluster_node>, ...]

ArakoonInstaller.extend_cluster(master_ip, new_ip, cluster_name, base_dir, locked=False)
check_output('ln -s /usr/lib/alba/nsm_host_plugin.cmxs {0}/arakoon/{1}/db'.format(base_dir, cluster_name), shell=True)
ArakoonInstaller.restart_cluster_add(cluster_name, current_ips, new_ip)
```
