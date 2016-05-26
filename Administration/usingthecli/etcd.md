### etcd
The [Open vStorage Framework](../../Internals/Framework/README.md) configuration can be retrieved from any node.  To retrieve or list a specific config run

```
etcdctl <command>
```

To get an overview of all possible commands run
```
etcdctl
```

#### The available etcdctl commands
* ls: retrieve a directory (ovs configuration starts at /ovs)
    * /ovs/alba: List alba configurations
    * /ovs/arakoon: List arakoon configurations
    * /ovs/framework: List framework configurations
    * /ovs/vpools/<GUID>/hosts/<VPOOL_NAME><GUID>/config: List vpool configurations

* get: retrieve a specific configuration file
    * etcdctl get /ovs/arakoon/ovsdb/config: Shows configuration file for ovsdb arakoon
    * etcdctl get /ovs/arakoon/ovsdb/metadata: Shows metadata configuration file for ovsdb arakoon
    * etcdctl get /ovs/vpools/<GUID>/hosts/<VPOOL_NAME><GUID>/config/content_addressed_cache
