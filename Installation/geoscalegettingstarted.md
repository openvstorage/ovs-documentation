# Getting Started

To be able to get started with GeoScale a number of [Arakoon clusters](#arakoon-cluster) need to be setup.
In this document we'll show you how to set up the necessary Arakoon clusters.

### Before starting

<font color="red">SSH keys for `root` and `ovs` should have been exchanged between all nodes running an Arakoon instance.</font>


### <a name="arakoon-cluster"></a>Arakoon Cluster

#### Prerequisites

In order to be able to set up an Arakoon cluster, the [openvstorage-core package must be installed](installovs.md)

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
* Usage: [ALBA backend](createbackend.md)
* Limitation: If not provided, OVS will create 1 per ALBA backend

###### NSM
* Amount: Equal to or higher than amount of ALBA backends
* Usage: [ALBA backend](createbackend.md)
* Limitation: If not provided, OVS will create 1 per ALBA backend to start with and can create additional clusters based on load

#### Installation

##### <a name="arakoon-cluster-create"></a>Creating cluster FWK 

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

##### <a name="arakoon-cluster-extend"></a>Extending cluster FWK 

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

##### Creating cluster SD 

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

##### Extending cluster SD 

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

##### Creating cluster ABM 

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

##### Extending cluster ABM 

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

##### Creating cluster NSM

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

##### Extending cluster NSM 

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
