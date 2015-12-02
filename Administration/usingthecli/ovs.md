### ovs
The [Open vStorage Framework](../../Internals/Framework/README.md) can be managed through a command line interface from any of the master nodes. To execute a command run

```
ovs <command>
```

To get an overview of all possible commands run
```
ovs help
```

#### The available ovs commands
* setup: Launch Open vStorage setup and autodetect required node role (master/extra)
    * setup master: Launch Open vStorage setup and install node as master node
    * setup promote: Promote this node (extra -> master)
    * setup demote: Demote this node (master -> extra)
* update
    * update framework: Update the OPen vStorage Framework on all nodes
    * update volumedriver: Update the Volume Driver on all nodes
* monitor services: Watch Open vStorage services
* monitor heartbeat: Send an internal heartbeat
* stop services: Try to stop all Open vStorage services
* start services: Try to start all Open vStorage servces
* collect logs: Collect all Open vStorage logs to a tarball for support purposes under /tmp.
* ovs unittest filepath: Run the unittests in filepath
