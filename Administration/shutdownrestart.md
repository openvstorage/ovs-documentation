### Shutdown and restart an Open vStorage cluster

#### Shutdown an environment
* Stop all Virtual Machines on the compute nodes.
* Make sure the outstanding data is flushed to the backend. Start an OVS shell
```
import volumedriver.storagerouter.storagerouterclient as src
client = src.LocalStorageRouterClient("/opt/OpenvStorage/config/storagedriver/storagedriver/<vpool_name>.json")
id = client.get_volume_id("/test_vm.raw")
snapshot_name = "testing_snapshot"
client.create_snapshot(id, snapshot_name)
client.info_snapshot(id, snapshot_name).in_backend
#If the last command is true, then the cache is flushed
client.delete_snapshot(id,snapshot_name)
```
* Shutdown the individual compute nodes by using the normal Linux commands such as `shutdown -h now`. Start with the extra nodes and power down the master nodes as last.

**Note: The above procedure can be used to stop individual servers also.  Do make sure there are always more than half of the master servers online to keep the cluster online.**



#### Restart an environment
* Restart the backend storage nodes if applicable.
* Start all master compute nodes (by default the first 3 compute nodes) one by one.
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
* In case there is an issue with one of the services, restart the service or check the log.
* In case there isn't a service issue, move down the stack to verify if the vPool is functioning. To check execute
```
truncate -s 10G /mnt/vpool/test.raw
touch /mnt/vpool/myvm.xml
```
* In case there are issues, check the  logs for the Volume Driver under `/var/log/ovs/volumedriver`, `/var/log/upstart/ovs-volumedriver_*` and `/var/log/upstart/ovs-volumerouter-consumer*`.
* In case you can't solve the issue based upon the log, please create a [bug](https://github.com/openvstorage/openvstorage/issues) and attach the error.