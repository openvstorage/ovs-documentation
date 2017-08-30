### Remove a Storage Router

It is possible to remove a Storage Router from the Open vStorage cluster. This action can be executed on online nodes but also in case the node is offline. In the later case it is advised to make sure the node remains offline or is reformatted before it rejoins the cluster.


Removing a Storage Router is only possible through CLI. To remove a Storage Router execute:
```
ovs remove node <ip of the node to remove>
```

During the remove process you will be prompted to confirm the removal. You can also add the `--force-yes` option in scripts to bypass the confirmation.

### Promoting and demoting Storage Routers
By default a cluster will have 3 Storage Routers running the master services. In case a master node is removed from the cluster, the next node that is added will automatically be configured as master.

It is possible to promote additional Storage Routers so they can also become a master. Execute following command on the command line of the Storage Router:
```
ovs setup promote
```

It is possible remove the master roles by executing:
  ```
ovs setup demote
```

### Remove an ALBA node
To remove an ALBA node execute `asd-manager remove` on the node with the ALBA SDM manager package.