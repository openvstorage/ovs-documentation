### Shutdown and restart an Open vStorage cluster

#### Shutdown an environment
* Stop all Virtual Machines on the compute nodes.
* Make sure the outstanding data is flushed to the backend. Start an OVS shell

```
#ips = [<ips of the Storage Routers you want to shutdown]
ips = None #Restart this Storage Router

#general packages
import time

#ovs packages
from ovs.dal.hybrids.vdisk import VDisk
from ovs.extensions.generic.system import System
from ovs.dal.lists.storagerouterlist import StorageRouterList

#log output

if ips is None:
   ips = [System.get_my_storagerouter().ip]
   print ips

for ip in ips:
	sr_info = StorageRouterList.get_by_ip(ip)
    vdisks_by_guid = sr_info.vdisks_guids
	print "[INFO] Fetched %s amount of disks on storagerouter '%s'" % (len(vdisks_by_guid), ip)

	for vdisk_guid in vdisks_by_guid:
		disk = VDisk(vdisk_guid)
		snapshot_name = "flush_snapshot"

		print "[INFO] Commencing flush to backend for volume '%s'..." % (disk.name)
		disk.storagedriver_client.create_snapshot(str(disk.volume_id), snapshot_name)

		while disk.storagedriver_client.info_snapshot(str(disk.volume_id), snapshot_name).in_backend == False:
            print "[INFO] Sleeping 5 seconds until NON-disposable SCO's for volume '%s' are flushed..." % (disk.name)
			time.sleep(5)

		disk.storagedriver_client.delete_snapshot(str(disk.volume_id), snapshot_name)
		print "[SUCCEEDED] Flush for volume '%s' has succeeded!" % (disk.name)
```

* Shutdown the individual compute nodes by using the normal Linux commands such as `shutdown -h now`. Start with the extra nodes and power down the master nodes as last.

**Note: The above procedure can be used to stop individual servers also.  Do make sure there are always more than half of the master servers online.**



#### Restart an environment
* Restart the backend storage nodes (if applicable).
* Start all master compute nodes (by default the first 3 compute nodes) **one by one**.
* Start all extra nodes.
* Login with ssh into each node and check whether all services are running. To check the services execute:
```
ovs monitor services
service memcached status
service nginx status
service rabbitmq-server status
rabbitmqctl cluster_status
rabbitmqctl list_queues
```
* In case there is an issue with one of the services, these need to fixed in a certain sequence:
    * First fix the issues with Nginx, Memcached and RabbitMQ by checking the relevant logs.
        * In case the RabbitMQ service isn't started, start the service on all nodes at the same time using `service rabbitmq-server start` (or similar on CentOS).
    * Next, address issues with the Arakoon databases by checking the log. A typical issue is the lack of a master.
    ```
    arakoon --who-master -config etcd://127.0.0.1:2379/ovs/arakoon/<DB_name>/config
    ```
    * In case there is an issue with Celery, go to the support page in the GUI and check if the node ID is displayed. If needed, restart the workers.
    ```
    restart ovs-workers
    ```
* In case there isn't a service issue, move down the stack to verify if the vPool is functioning. To check execute:
```
truncate -s 10G /mnt/<vpool_name>/test.raw
touch /mnt/<vpool_name>/myvm.xml
```
* In case there are issues, check the Volume Driver logs under `/var/log/ovs/volumedriver`, `/var/log/upstart/ovs-volumedriver_*` and `/var/log/upstart/ovs-volumerouter-consumer*`.
* In case you can't solve the issue based upon the log, please create a [bug](https://github.com/openvstorage/home/issues) and attach the error.