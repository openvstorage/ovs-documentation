### Upgrades

#### Automated upgrades via the GUI
The easiest and most reliable way to upgrade Open vStorage is through the GUI. Go to Administration > Updates to verify if updates are pending.
* Click the **Update Framework** button to update the Open vStorage Framework.
* Click the **Update Storage Driver** button to update the Volume Driver and Open vStorage Backend.


#### Manually upgrade an environment
The below procedure describes how to manually update all components of an Open vStorage environment. Do follow these steps closely as an incorrect update might result in data loss or an unrecoverable environment.

The packages of the different versions can be found on the repo's: [http://apt.openvstorage.org/](http://apt.openvstorage.org/) and [http://yum.openvstorage.org/](http://yum.openvstorage.org/).

The manual procedure consists out of 5 steps:
* Stop all virtual machines using any volumes
* [Stop Framework, Volume Driver and ALBA services](#stopservices)
* [Upgrade Alba](#upgradealba)
* [Upgrade the Volume Driver](#upgradevoldrv)
* [Upgrade Framework](#upgradeframework)

##### Stopr the services
To stop all services execute
* For the Framework
```
stop ovs-watcher-framework
stop memcached
stop arakoon-ovsdb
```
* For the Volume Driver
```
stop ovs-volumedriver_vpoolname
stop ovs-dtl_vpoolname
```
* For ALBA
```
stop ovs-albaproxy_vpoolname
stop alba-asd-*
stop ovs-alba-maintenance*
stop ovs-alba-rebalancer*
```

##### Upgrade ALBA
* Make sure you are also about to update [Arakoon](http://arakoon.org/) if required
* If you have less than 3 master nodes, make sure all volumes are stopped
* On all nodes, install the new alba package through apt or yum.
* On all nodes, restart each ASD
    * Find them with ls -al /etc/init/alba-asd-*
* On all nodes, restart all proxies by sending a kill signal kill <pid>
    * Find them with ls -al /etc/init/ovs-albaproxy* and for each entry status ovs-albaproxy_<vpool name>
* On all nodes where applicable, restart the alba arakoon clusters, foreach backend
    * Find them with ls -al /etc/init/ovs-arakoon-<backend name>*
        * If < 3: Stop them all, and start them all up again
        * If >= 3: Restart them one by one, waiting a minute in between
* On all nodes, restart the maintenance and rebalancer processes
    * Find them with ls -al /etc/init/ovs-alba-maintenance* and ls -al /etc/init/ovs-alba-rebalancer*

##### Upgrade the Volume Driver
* On all nodes, install new Volume Driver packages through apt or yum.
* On all nodes, for each running vPool execute restart ovs-volumedriver_vpoolname
    * Find them with ls -al /etc/ovs-volumedriver*

##### Upgrade the Framework
You simply can't upgrade this one without the GUI unless you manually touch /etc/ready_for_upgrade it will refuse to install.

* Stop all relevant services if not done before (watcher-framework, memcache, arakoon-ovsdb)
* Upgrade the packages through apt or yum.
* Start arakoon-ovsdb, memcached on the master nodes
    * On 1 node:
```
from ovs.dal.helpers import Migration}}
Migration.migrate()
```
    * On all nodes:
```
from ovs.extensions.migration.migrator import Migrator
Migrator.migrate()
```
* Restart memcache on all master nodes
* Start the watcher-framework again
```
start ovs-watcher-framework
```

**Do not forget to patch files you manually patched after the installation as they might have been overwritten by the package update.**
