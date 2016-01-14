#### ALBA Config files
The ALBA configuration file is located at `/opt/OpenvStorage/config/alba.json`.

```
{
    "nsm": {
        "safety": 3,
        "maxload": 75
    }
}
```

The different fields are:
* nsm:
    * safety: Amount of arakoon nodes in the NSM cluster. The value should be equal or less than the amount of master nodes. Default is 3.
    * maxload: Percentage when to create an additional NSM cluster during the NSM checkup. Default is 75% of 50 volumes.

**Note: Modifying/changing the config files incorrectly might result in data loss.**

##### Maintenance and rebalance config files
For the maintenance and the rebalancer you have the option to  [set parameters across all nodes](../../Internals/ALBA/selfhealing.md) but some parameters can be set per node in a config file.

For the maintenance process the node specific config file is located at `/opt/OpenvStorage/config/arakoon/<backend_name>-abm/<backend_name>.json`.
For the rebalancer the node specific config file is located at `/opt/OpenvStorage/config/arakoon/<backend_name>-abm/<backend_name>-rebalancer.json`.


```
{
    "log_level": "info",
    "albamgr_cfg_file": "/opt/OpenvStorage/config/arakoon/ovs-bknd-poc01-abm/ovs-bknd-poc01-abm.cfg",,
    "albamgr_connection_pool_size" : 10, // optional, default 10
    "nsm_host_connection_pool_size" : 10, // optional, default 10
    "osd_connection_pool_size" : 10, // optional, default 10
    "osd_timeout" : 30.0 //optional, default 30.0

    // tls client config:
    , "tls_client" : {
        "ca_cert" : "/tmp/arakoon/cacert.pem",
        "cert"    : "/tmp/arakoon/my_client/my_client.pem",
        "key"     : "/tmp/arakoon/my_client/my_client.key",
    }
}
```

The different fields are:
* log_level: Configured log level. Options are debug, info, notice, warning, error and fatal.
* albamgr_cfg_file: ABM configuration file. By default located at `/opt/OpenvStorage/config/arakoon/<backend_name>-abm/<backend_name>-abm.cfg`.
* albamgr_connection_pool_size : Amount of connections to the ABM. Default is 10.
* nsm_host_connection_pool_size : Amount of connections to an NSM. Default is 10.
* osd_connection_pool_size : Amount of connections to an OSD. Default is 10.
* osd_timeout : Time-out for operations on the OSD (f.e. fragment upload). Default is 30 seconds.
* tls_client : Configuration of the TLS client.
    * ca_cert : Certificate of the signing authority.
    * cert: public certificate of the peer, signed using this very ca_cert.
    * key : private key file of that peer.
