### ALBA
The [ALBA component](../../Architecture/ALBA/README.md) can be managed through a command line interface from any of the nodes. To execute a command run

```
alba <command>
```

To get an overview of all possible commands run
```
alba --help
```

#### The available ALBA commands
* add-nsm-host: add a nsm host
* add-osd: add osd to manager so it could be claimed later. The long id is fetched from the device. Note: this is for development purposes only.
* add-osds-to-preset: add some osds to the specified preset
* alba-bench: simple alba benchmark
* asd-delete: perform a delete on a remote ASD
* asd-discovery: listen for ASD servers
* asd-get-version: get remote version
* asd-multi-get: perform a multi get on a remote ASD
* asd-range: range query on a remote ASD
* asd-set: perform a set on a remote ASD
* asd-set-full: make this asd behave as if (not) full
* asd-start: start ASD server
* asd-statistics: get statistics from the asd
* bench-fsync: write files and sync with fsync
* bench-syncfs: write files and sync with syncfs
* claim-osd: claim the osd by this alba instance
* clear-job-progress: clear progress of a certain job
* create-namespace: create a namespace with the given name
* create-preset: create a new preset. the preset is read from stdin as json, pls have a look at cfg/preset.json for more details.
* decommission-osd: tell this alba instance we are no longer going to use the osd
* delete-namespace: delete the namespace with the given name
* delete-object: delete an object from alba
* delete-preset: delete the specified preset
* deliver-messages: deliver all outstanding messages (note that this happens automatically by the maintenance process too, so you usually shouldn't need this.)
* dev-list-device-objects: objects from the namespace that have fragments on this device
* download-object: download an object from alba
* get-alba-id: Get the unique alba instance id
* get-disk-safety: get the disk safety
* get-maintenance-config: get the maintenance config from the albamgr
* list-all-osds: list registered osds
* list-available-osds: list known osds still available for claiming by this alba instance
* list-decommissioning-osds: list osds that are not yet fully decommissioned
* list-namespaces: list all namespaces
* list-namespaces-by-id: show id to name mapping
* list-ns-osds: list OSDs coupled to the specified namespace
* list-nsm-host-messages: list messages from mgr to nsm hosts
* list-nsm-hosts: list all nsm hosts
* list-objects: list all objects in the specified namespace
* list-osd-messages: list messages from mgr to osds
* list-osds: list registered osds
* list-participants: list participants
* list-presets: list the presets available in the albamgr
* list-work: list outstanding work items
* maintenance: run the maintenance process (garbage collection, obsolete fragment deletion, repair, ...)
* mgr-get-version: the alba mgr's version info
* mgr-statistics: the alba mgr's statistics
* namespace-recovery-agent: recover the contents of a namespace from the osds
* nsm-get-version: nsm host's version info
* nsm-host-statistics: namespace hosts' statistics
* osd-benchmark: perform a benchmark on an osd
* osd-multi-get: multi get on an OSD
* osd-range: range query on an OSD
* preset-set-default: make the specified preset the default preset
* proxy-bench: simple proxy benchmark
* proxy-create-namespace: create a namespace
* proxy-delete-namespace: delete a namespace
* proxy-download-object: download an object from alba
* proxy-get-version: the proxy's version info
* proxy-invalidate-cache: invalidate the cache on the proxy for $(NAMESPACE)
* proxy-list-namespaces: list namespaces
* proxy-list-objects: list the objects
* proxy-osd-view: this proxy's view on osds
* proxy-start: start a proxy server
* proxy-statistics: retrieve statistics for this proxy
* proxy-upload-object: upload an object to alba
* recover-namespace: recover an existing namespace from which the metadata got lost to another nsm host
* rewrite-namespace: rewrite all objects in the specified namespace
* rewrite-object: rewrite an object
* show-job-progress: show progress of a certain job
* show-namespace: show information about a namespace
* show-object: print some info about the object
* unit-tests: run unit tests
* update-maintenance-config: update the maintenance config
* update-nsm-host: update a nsm host
* update-preset: update an existing preset. the preset is read from stdin as json, please have a look at cfg/update_preset.json for more details.
* upload-object: upload an object to alba
* verify-namespace: verify all objects in the specified namespace
* version: show version information



