#### Self Healing
ALBA has built-in self healing functionality. In case an ASD is broken, fragments on the ASD will be automatically reconstructed on remaining disks in the Backend.

The self healing has some default parameters but these can be overridden through the ALBA CLI.

##### Retrieve the maintenance config
Run the **alba get-maintenance-config** command with as option the config of the config file of the ABM Arakoon. (See [Arakoon config files](../../Administration/Configs/arakoon.md) for more information).

```
root@cmp02:~# alba get-maintenance-config --config=/opt/OpenvStorage/config/arakoon/<backendname>-abm/<backendname>-abm.cfg  --to-json
```
The result is a json with configuration
```
{"success":true,"result":{"enable_auto_repair":true,"auto_repair_timeout_seconds":900.0,"auto_repair_disabled_nodes":[],"enable_rebalance":true}}
root@cmp02:~#

The different configuration fields:
* enable_auto_repair: true or false. Indicates if a broken ASD gets automatically rebuilt on the remaining ASDs. Default is true.
* auto_repair_timeout_seconds: time it takes before the maintenance process will start to rebuild the broken ASD. Default is 900 seconds
* auto_repair_disabled_nodes: list of nodes which should not automatically be repaired
* enable_rebalance: true or false. Indicates if existing fragments should be automatically moved to new ASDs when the new ASDs are added. Default is true.

##### Update the ALBA maintenance config
To change the configuration of the ALBA maintenance, run the **alba update-maintenance-config** command with as option the config of the config file of the ABM Arakoon and the parameters you cant to change.

For example to update the repair timeout
```
root@cmp02:~# alba get-maintenance-config --config=/opt/OpenvStorage/config/arakoon/<backendname>-abm/<backendname>-abm.cfg  --auto-repair-timeout-seconds=100

Maintenance config now is { Maintenance_config.enable_auto_repair = true;
  auto_repair_timeout_seconds = 100.; auto_repair_disabled_nodes = [];
  enable_rebalance = true }
```

The different option are:
* --auto-repair-add-disabled-node=\<node_id of the ASD node\> : add a node to the list of nodes which should not be automatically rebuild.
* --auto-repair-remove-disabled-node=\<node_id of the ASD node\> : remove a node to the list of nodes which should not be automatically rebuild.
* --auto-repair-timeout-seconds=\<int\> : interval before the automated repair starts rebuilding data.
* --disable-auto-repair :  diskable the self healing functionality.
* --disable-rebalance : disable the rebalancer.
* --enable-auto-repair : enable the self healing functionality.
* --enable-rebalance : enable the rebalancer.

** Note: the ASD node ID can be retrieved from the Backend Detail page in the GUI or by executing in the shell of the ASD node following command:
```
cat /opt/alba-asdmanager/config/config.json | grep node_id
```


