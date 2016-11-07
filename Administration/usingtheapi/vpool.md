### vPools

### vPool object structure

-   configuration: The vPool configuration known by the StorageDriver.
-   connection: Connection (IP, URL, Domainname, Zone, ...) for the Storage Backend.
-   description: Description of the vPool.
-   guid: GUID of the vPool.
-   login: Login/Username for the Storage Backend, if not LOCAL or DISTRIBUTED.
-   mds_services: The MDS services used by this vPool.
-   mds_services_guids: The GUIDs of the MDS services.
-   metadata: Metadata of the vPool.
-   name: Name of the vPool.
-   password: Password for the Storage Backend.
-   rdma_enabled: Does the vPool support RDMA.
-   size: Size of the vPool expressed in Bytes. Set to zero if not applicable.
-   statistics: Aggregated statistics from all this vPools's vDisks. See vDisk.statistics.
-   status: Status of the vPool.
-   storagedriver_client: Client used for communication between Storage Driver and framework.
-   storagedrivers: List of Storage Drivers serving this vPool.
-   storagedrivers_guids: The GUIDs of the Storage Drivers.
-   stored_data: Total size in Bytes of the current data and the Snapshots without the overhead imposed by the Backend redundancy.
-   vdisks: The vDisks served by this vPool.
-   vdisks_guids: List of vDisk GUIDs served by this vPool.

### Example data

```
{
    "status": "RUNNING",
    "guid": "f4f0bce7-e14f-4272-bd27-9bde3d3e405e",
    "name": "veepoel",
    "identifier": "f4f0bce7-e14f-4272-bd27-9bde3d3e405e_9b353f51-466d-4912-b305-6c4428d53242",
    "size": 35800154112,
    "metadata": {
        "backend": {
            "arakoon_config": {
                "global": {
                    "cluster": "a20NIhgcyZ29TItH",
                    "cluster_id": "bend-abm",
                    "tlog_max_entries": "5000",
                    "plugins": "albamgr_plugin"
                },
                "a20NIhgcyZ29TItH": {
                    "log_level": "info",
                    "name": "a20NIhgcyZ29TItH",
                    "fsync": "true",
                    "crash_log_sinks": "console:",
                    "ip": "10.100.193.154",
                    "log_sinks": "console:",
                    "tlog_compression": "snappy",
                    "client_port": 26402,
                    "messaging_port": 26403,
                    "home": "/mnt/hdd1/arakoon/bend-abm/db",
                    "tlog_dir": "/mnt/hdd1/arakoon/bend-abm/tlogs"
                }
            },
            "name": "bend",
            "backend_info": {
                "fragment_cache_on_write": true,
                "sco_size": 4194304.0,
                "total_size": 9997099970560.0,
                "frag_size": 1048576.0,
                "policies": [
                    [
                        5,
                        4
                    ],
                    [
                        2,
                        2
                    ]
                ],
                "fragment_cache_on_read": true
            },
            "connection": {
                "client_secret": "-J{!Pth<_6b@1UHuhzW1j#gW9l6wF,lfYhj[uN,N--uO_aB,vf7.ASPDu.FMt}YrYFie1xWTF'*hjQ_N*<D=TXQ>rg1*x9S5C}lmEpZ*#bx/uOsSGxS}P#\"~ma-sy*dy",
                "host": "10.100.193.154",
                "local": true,
                "port": 443,
                "client_id": "4a0de1d8-df8e-4e5a-b6d9-5bd85eb4433d"
            },
            "preset": "default",
            "backend_guid": "b5b71076-d48d-4a51-ab14-2bbc6b2afdc8"
        }
    },
    "rdma_enabled": false,
    "connection": null,
    "stored_data": 0,
    "configuration": {
        "write_buffer": 128.0,
        "sco_size": 4,
        "dtl_transport": "tcp",
        "cluster_size": 4,
        "tlog_multiplier": 16,
        "dtl_mode": "a_sync",
        "dtl_enabled": true
    },
    "statistics": {
        "operations": 0,
        "backend_data_written_ps": 0,
        "cluster_cache_misses_ps": 0,
        "data_read": 0,
        "4k_read_operations_ps": 0,
        "stored": 0,
        "sco_cache_misses": 0,
        "sco_cache_hits_ps": 0,
        "sco_cache_hits": 0,
        "write_operations": 0,
        "cluster_cache_misses": 0,
        "read_operations_ps": 0,
        "sco_cache_misses_ps": 0,
        "backend_write_operations": 0,
        "backend_data_read": 0,
        "cache_hits": 0,
        "backend_write_operations_ps": 0,
        "data_transferred_ps": 0,
        "metadata_store_hits_ps": 0,
        "metadata_store_misses": 0,
        "backend_data_written": 0,
        "4k_operations_ps": 0,
        "data_read_ps": 0,
        "4k_read_operations": 0,
        "4k_operations": 0,
        "read_operations": 0,
        "cluster_cache_hits": 0,
        "data_written_ps": 0,
        "cluster_cache_hits_ps": 0,
        "cache_hits_ps": 0,
        "timestamp": 1466672442.258548,
        "stored_ps": 0,
        "metadata_store_misses_ps": 0,
        "cache_misses": 0,
        "4k_write_operations": 0,
        "data_written": 0,
        "metadata_store_hits": 0,
        "backend_data_read_ps": 0,
        "operations_ps": 0,
        "backend_read_operations_ps": 0,
        "backend_read_operations": 0,
        "4k_write_operations_ps": 0,
        "cache_misses_ps": 0,
        "write_operations_ps": 0,
        "data_transferred": 0
    },
    "login": "",
    "password": "",
    "description": "alba veepoel"
}
```

### vPool actions

Main actions:

-   List: GET
-   Resource: GET

| Action |  Action/Link | Type | Input | Response |
|--------|--------------|------|-------|----------|
| Sync the vMachines on this vPool | sync_vmachines | POST | | GUID of a Celery task |
| List of StorageRouters serving this vPool | storagerouters | GET | | List of StorageRouters |
| Shrink a vPool | shrink_vpool | POST | {"storagerouter_guid": [str], GUID of the Storage Router to remove from the vPool | Celery Task GUID | 

