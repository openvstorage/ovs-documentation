### Configuration Management
The [Open vStorage Framework](https://openvstorage.gitbooks.io/framework/) uses a distributed configuration management system.
There are 2 option as configuration management system:
* Arakoon, the preferred and advised system.
* ETCD

#### Set, get and list configuration keys
The [OVS commandline](ovs.md) allows to easily list and change the configuration parameters of the cluster:
* `ovs config edit some/key`: Edit that key in your `$EDITOR`. If it doesn't exist, the key is created.
* `ovs config list some`: List all keys with the given prefix.
* `ovs config get some/key`: Print the content of the given key.

#### The config keys
When needed the different components of Open vStorage create the necessary key in the distributed configuration management system. Please refer to the indiviual GitBook of each component for more info on the config keys:
* [Framework](https://openvstorage.gitbooks.io/framework/content/docs/configmgmt.html)
* [VolumeDriver](https://openvstorage.gitbooks.io/volumedriver/content/docs/config.html) 
* [ALBA](https://openvstorage.gitbooks.io/framework-alba-plugin/content/docs/configmgmt.html)

#### Arakoon
To retrieve a configuration key from Arakoon

```
arakoon -config /opt/OpenvStorage/config/arakoon_cacc.ini --get-raw ovs/framework/cluster_id
```

To set a configuration key in Arakoon

```
arakoon -config /opt/OpenvStorage/config/arakoon_cacc.ini --set key value
```

#### ETCD
To retrieve or list a specific config run

```
etcdctl <command>
```

To get an overview of all possible commands run
```
etcdctl
```

##### The available etcdctl commands
* ls: retrieve a directory (ovs configuration starts at /ovs)
    * `/ovs/alba`: List alba configurations
    * `/ovs/arakoon`: List arakoon configurations
    * `/ovs/framework`: List framework configurations
    * `/ovs/vpools/<GUID>/hosts/<VPOOL_NAME><GUID>/config`: List vpool configurations

* get: retrieve a specific configuration file
    * `etcdctl get /ovs/arakoon/ovsdb/config`: Shows configuration file for ovsdb arakoon
    * `etcdctl get /ovs/arakoon/ovsdb/metadata`: Shows metadata configuration file for ovsdb arakoon
    * `etcdctl get /ovs/vpools/<GUID>/hosts/<VPOOL_NAME><GUID>/config/content_addressed_cache`
