#### ALBA Proxy Config files
 There is an ALBA Proxy configuration file per vPool. The config file is located at `/opt/OpenvStorage/config/storagedriver/storagedriver/<vpool_name>_alba.json`.

```
{
    "ips" : ["::1"], // optional
    "log_level": "debug",
    "port":  26203,
    "albamgr_cfg_file": "/opt/OpenvStorage/config/storagedriver/storagedriver/poc01-vpool-ovs_alba.cfg",
    "manifest_cache_size" : 100000, // optional, default 100_000
    "fragment_cache_dir" : "/mnt/ssd1/poc01-vpool-ovs_write_fcache_1",
    "fragment_cache_size" : 100000000, // optional, default 100_000_000
    "albamgr_connection_pool_size" : 10, // optional, default 10
    "nsm_host_connection_pool_size" : 10, // optional, default 10
    "osd_connection_pool_size" : 10, // optional, default 10
    "osd_timeout" : 30.0 //optional, default 30.0
    "max_client_connections" : 128 // optional, default 128
    // tls client config:
    , "tls_client" : {
        "ca_cert" : "/tmp/arakoon/cacert.pem",
        "cert"    : "/tmp/arakoon/my_client/my_client.pem",
        "key"     : "/tmp/arakoon/my_client/my_client.key",
    }
}
```

The different fields are:
* ips: IPs o which the proxy is exposed.
* log_level: Configured log level. Options are debug, info, notice, warning, error and fatal.
* port: Port used by the ALBA Proxy.
* albamgr_cfg_file: vPool configuration file. By default located at `"albamgr_cfg_file": "/opt/OpenvStorage/config/storagedriver/storagedriver/<vpool-name>_alba.cfg"`.
* manifest_cache_size : Size of the cache for the manifests on the node in bytes.
* fragment_cache_dir : Directory for the fragment cache.
* fragment_cache_size : Size of the fragment cache on the node in bytes.
* albamgr_connection_pool_size : Amount of connections to the ABM. Default is 10.
* nsm_host_connection_pool_size : Amount of connections to an NSM. Default is 10.
* osd_connection_pool_size : Amount of connections to an OSD. Default is 10.
* osd_timeout : Time-out for operations on the OSD (f.e. fragment upload). Default is 30 seconds.
* tls_client : Configuration of the TLS client.
    * ca_cert : Certificate of the signing authority.
    * cert: public certificate of the peer, signed using this very ca_cert.
    * key : private key file of that peer.


**Note: Modifying/changing the config files incorrectly might result in data loss.**

