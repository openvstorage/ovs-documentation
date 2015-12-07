#### ASD Config files
Each ASD has its own config file stored on the ASD itself under `/mnt/alba-asd/xV7ipU80ZhAB0cmxaPUFvGSJMkeKAQn3/asd.json`

```

root@cmp02:~# cat /mnt/alba-asd/xV7ipU80ZhAB0cmxaPUFvGSJMkeKAQn3/asd.json
{
    "home": "/mnt/alba-asd/xV7ipU80ZhAB0cmxaPUFvGSJMkeKAQn3/data",
    "asd_id": "xV7ipU80ZhAB0cmxaPUFvGSJMkeKAQn3",
    "node_id": "tsCmJKYNvxctkQhBqNDJBgsmZyw1rdc8",
    "log_level": "info",
    "port": 8602
}
```

The different fields are:
* home: Mountpount of the disk on the ASD node.
* asd_id: Unique ID of the ASD.
* node_id: Unique ID of the ASD node.
* log_level: Configured log level.
* port: Port on which the ASD is exposed.

In case you update the json, kill the process and let upstart take care of the restart.


**Note: Modifying the config files incorrectly might result in data loss.**

