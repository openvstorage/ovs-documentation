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
* setup: Launch Open vStorage setup and autodetect required node role (master/extra).
    * `ovs setup master`: Launch Open vStorage setup and install node as master node.
	* `ovs setup extra`: Launch Open vStorage setup and install node as extra node.
    * `ovs setup promote`: Promote this node (extra -> master).
    * `ovs setup demote`: Demote this node (master -> extra).
* remove node	
	* `ovs remove node <IP>`: Removes the node from cluster.
* update
    * `ovs update framework`: Update the OPen vStorage Framework on all nodes.
    * `ovs update volumedriver`: Update the VolumeDriver on all nodes.
* monitor
	* `ovs monitor services`: Watch Open vStorage services.
	* `ovs monitor heartbeat`: Send an internal heartbeat.
* service options
	* `ovs stop framework [ip|all]`: Stop Open vStorage Framework services on this node, on all nodes, or on the given ip.
	* `ovs start framework [ip|all]`: Start Open vStorage Framework services on this node, on all nodes, or on the given ip.
* miscellaneous options	
	* `ovs collect logs`: Collect all Open vStorage logs to a tarball for support purposes under `/tmp`.
	* `ovs unittest filepath`: Run the unittests in filepath.
* configuration management
	* `ovs config edit some/key`: Edit that key in your `$EDITOR`. If it doesn't exist, the key is created.
	* `ovs config list some`: List all keys with the given prefix.
	* `ovs config get some/key`: Print the content of the given key.
