### Remove a Storage Router

It is possible to remove a Storage Router from the Open vStorage cluster.

Removing a Storage Router is only possible through CLI. To remove a Storage Router execute:
```
ovs remove nodes <comma separated list of ips of the node to remove>
```

### Promoting and demoting Storage Routers
By default a cluster will have 3 Storage Routers running the master services. In case a master node is removed from the cluster, the next node that is added will automatically be configured as master.

It is possible to promote additional Storage Routers so they can also become a master. Execute following command on teh command line of the Storage Router:
```
ovs setup promote
```

It is possible remove the master roles by executing:
  ```
ovs setup demote
```