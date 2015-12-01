#### Open vStorage Config file
The general configuration of Open vStorage gets done in a json file per node. To change the config file, edit the json located at */opt/openvstorage/config/ovs.json*

> **Note:**
> Modifying the config files incorrectly might result in data loss.

| component | key | default value | dynamically reconfigurable | remarks |
| --- | --- | -- | --- | --- |
| core | setupcompleted | true | NA | result post install |
| core | cfgdir | "/opt/OpenvStorage/config" | NA | default location of the config files |
| core | nodetype | "master" or "extra" | NA | type of node: master or slave |
| core | audittrails / keep | "30" | NA | amount of time in days to keep audit logs |
| core | ovsdb | "/opt/OpenvStorage/db" | NA | location of the Framework DB |
| core | basedir | "/opt/OpenvStorage" | NA | basedir for Open vStorage |
| core | storage / volatile | "memcache" | NA | cache DB for model |
| core | storage / volatile | "arakoon" | NA | persistent DB for model |



```
{
    "core": {
        "setupcompleted": true,
        "cfgdir": "/opt/OpenvStorage/config",
        "nodetype": "MASTER",
        "audittrails": {
            "keep": 30
        },
        "ovsdb": "/opt/OpenvStorage/db",
        "basedir": "/opt/OpenvStorage",
        "storage": {
            "volatile": "memcache",
            "persistent": "arakoon"
        },
        "broker": {
            "queues": {
                "storagedriver": "volumerouter"
            },
            "password": "0penv5tor4ge",
            "protocol": "amqp",
            "login": "ovs",
            "port": 5672
        },
        "install_time": 1448976816.808232
    },
    "logging": {
        "path": "/var/log/ovs",
        "default_name": "logger",
        "default_file": "generic",
        "level": "DEBUG"
    },
    "webapps": {
        "main": {
            "secret": "YaN|zz3L$FTO7MA8b`vD`(ku2iB_w$=kC}\"O=.+}\"Ix(`iux.U",
            "appname": "api",
            "uiname": "api",
            "dbname": "api.sqlite3"
        },
        "html_endpoint": "/",
        "dir": "webapps",
        "oauth2": {
            "mode": "local"
        }
    },
    "support": {
        "enablesupport": false,
        "enabled": true,
        "cid": "YSLYgOC4CeCP5bqp",
        "nid": "4z1zuG8bsQCY8avP",
        "interval": 60
    },
    "grid": {
        "ip": "10.100.174.254"
    },
    "plugins": {
        "generic": [],
        "backends": [
            "alba"
        ]
    },
    "path": {
        "celery": "/usr/bin/celery"
    },
    "storagedriver": {
        "mds": {
            "tlogs": 100,
            "safety": 2,
            "maxload": 75
        },
        "rsp": "/var/rsp",
        "vmware_mode": "ganesha"
    },
    "ports": {
        "mds": [
            [
                26300,
                26399
            ]
        ],
        "storagedriver": [
            [
                26200,
                26299
            ]
        ],
        "arakoon": [
            26400
        ]
    }
}
```