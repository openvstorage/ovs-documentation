### Configuration Management
The [Open vStorage Framework](https://openvstorage.gitbooks.io/framework/) uses a distributed configuration management system.
This configuration management system is implemented using Arakoon, our own distributed, always consistent DB.

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

#### Interact directly with Arakoon for config management
> ==== Warning::The OVS CLI is advised
> 
> The easiest, safest and advised way to update a key in the configuration management system is by using the OVS CLI (see above). Incorrect usage of the below comments can lead to a corrupt cluster.


To retrieve a configuration key from Arakoon

```
arakoon -config /opt/OpenvStorage/config/arakoon_cacc.ini --get-raw ovs/framework/cluster_id
```

To set a configuration key in Arakoon

```
arakoon -config /opt/OpenvStorage/config/arakoon_cacc.ini --set key value
```
