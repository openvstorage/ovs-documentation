#### ALBA ASD Manager
Each ASD node has an ALBA ASD Manager config file located under `/opt/alba-asdmanager/config/config.json`.

```
{
    "main": {
        "username": "root",
        "password": "i4rRUSTyZbHmtowLMhWrAX0mzkMS9gWX",
        "version": 1,
        "node_id": "tsCmJKYNvxctkQhBqNDJBgsmZyw1rdc8"
    },
    "network": {
        "ips": [],
        "port": 8600
    }
}
```
The different fields are:
* Main:
    * username: The username to address the ASD node. Default is root.
    * password: The generated connection password of the ASD node.
    * version: API version.
    * node_id: The generated id of this node.
* Network:
    * ips: IPs on the ASDs will be listening. By default the ASD are listening on all IPs (empty list).
    * port: Start port of the ASDs. Default port is 8600. The first ASD will have port 8600, the second 8601 and so on.

It is possible to add additional fields under `extra_parameters`. This can be used to inject extra parameters into all newly created ASDs. An example usage is to disable the sync of the ASD.
 ```
 "__sync_dont_use": false
 ```

**Note: Modifying/changing the config files incorrectly might result in data loss.**