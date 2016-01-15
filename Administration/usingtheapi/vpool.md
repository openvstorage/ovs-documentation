### vPools

### vPool object structure

-   guid: GUID of the vPool.
-   name: Name of the vPool.
-   login: Login/Username for the Storage Backend, if not LOCAL or
    DISTRIBUTED.
-   connection: Connection (IP, URL, Domainname, Zone, ...) for the
    Storage Backend.
-   size: Size of the vPool expressed in Bytes. Set to zero if not
    applicable.
-   metadata: Metadata of the vPool.
-   description: Description of the vPool.
-   rdma_enabled: Does the vPool support RDMA.
-   backend_type: GUID of the type of Backend.
-   statistics: Aggregated statistics from all this vPools's vDisks. See
    vDisk.statistics.
-   status: Status of the vPool.
-   stored_data: Total size in Bytes of the current data and the
    Snapshots without the overhead imposed by the Backend redundancy.
-   vmachines_guids: List of vMachine GUIDs served by this vPool.
-   vdisks_guids: List of vDisk GUIDs served by this vPool.
-   storagedrivers_guids: List of StorageDriver GUIDs serving this
    vPool.

### Example data

```
{
    "guid": "e9146a46-2ca2-4f09-93f4-9101e0ffd439",
    "name": "local",
    "login": null,
    "connection": null,
    "size": 51653570560,
    "metadata": {
        "backend_type": "LOCAL",
        "local_connection_path": "/mnt/bfs/local"
    },
    "description": "LOCAL local",
    "type": "LOCAL",
    "statistics": {
        "operations": 0,
        "cluster_cache_misses_ps": 0,
        "data_read": 0,
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
        "metadata_store_hits_ps": 0,
        "metadata_store_misses": 0,
        "backend_data_written": 0,
        "data_read_ps": 0,
        "read_operations": 0,
        "cluster_cache_hits": 0,
        "data_written_ps": 0,
        "cluster_cache_hits_ps": 0,
        "cache_hits_ps": 0,
        "timestamp": 1412169916.481471,
        "metadata_store_misses_ps": 0,
        "backend_data_written_ps": 0,
        "backend_read_operations": 0,
        "data_written": 0,
        "metadata_store_hits": 0,
        "backend_data_read_ps": 0,
        "operations_ps": 0,
        "backend_read_operations_ps": 0,
        "data_transferred_ps": 0,
        "write_operations_ps": 0,
        "data_transferred": 0
    },
    "status": null,
    "stored_data": 0,
    "vmachines_guids": [
        "67328b8e-d2a8-4d3b-979d-f39362df9471",
        "86041df1-fa32-4377-97ed-e07cfc67d896"
    ],
    "vdisks_guids": [
        "ba398bee-d9a0-4a80-9222-3220c313f63e",
        "da1657cb-f42e-4678-85bf-d4d6eec4beae"
    ],
    "storagedrivers_guids": [
        "8ceb873a-7b60-4a8f-b91f-c373ce65ea07"
    ]
}
```

### vPool actions

Main actions:

-   List: GET
-   Resource: GET

| Action |  Action/Link | Type | Input | Response |
|--------|--------------|------|-------|----------|
| Get vPool configuration| get_configuration | GET |  | vPool object |
| Sync the vMachines on this vPool | sync_vmachines | POST | | GUID of a Celery task |
| List of StorageRouters serving this vPool | storagerouters | GET | | List of StorageRouters |
| Update StorageDrivers serving the vPool | update_storagedrivers | POST | {"storagerouter_guids": [str], list of StorageRouter GUIDs to append the vPool to, optional, "storagedriver_guid": str, GUID of a StorageDriver to use as a blueprint for the new StorageDrivers, "storagedriver_guids": [str], list of StorageDriver GUIDs to remove, optional } | GUID of a Celery task |

