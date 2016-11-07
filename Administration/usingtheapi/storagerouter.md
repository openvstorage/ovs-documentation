### StorageRouters

### StorageRouter object structure

-   guid: GUID of the StorageRouter.
-   machine_id: Internal machine identifier of the StorageRouter.
-   ip: Ip for inter-StorageRouter communication.
-   heartbeats: Heartbeat timestamps for different StorageRouter
    components.
-   name: Name of the StorageRouter.
-   description: Description of the StorageRouter.
-   failover_mode: Status of the StorageRouter failover mode.
-   vmachines_guids: List of vMachine GUIDs served by this
    StorageRouter.
-   vpools_guids: List of vPool GUIDs served by this StorageRouter.
-   vdisks_guids: List of vDisk GUIDs served by this StorageRouter.
-   status: Status of the StorageRouter.
-   statistics: Aggregated statistics from all this StorageRouter's
    vDisks. See vDisk.statistics.
-   stored_data: Amount of data stored by this StorageRouter's vPools.
-   pmachine_guid: The GUID of the pMachine hosting this StorageRouter.
-   storagedrivers_guids: A list of StorageDriver GUIDs served by this
    StorageRouter.

### Example data

```
{
    "guid": "a8743428-9bb8-478f-ac4e-366d273db96d",
    "machine_id": "0050569647a1",
    "ip": "10.100.169.100",
    "heartbeats": {
        "celery": 1412167861,
        "process": 1412167861
    },
    "name": "ovs100",
    "description": null,
    "failover_mode": "OK_STANDALONE",
    "vmachines_guids": [
        "67328b8e-d2a8-4d3b-979d-f39362df9471",
        "86041df1-fa32-4377-97ed-e07cfc67d896"
    ],
    "vpools_guids": [
        "e9146a46-2ca2-4f09-93f4-9101e0ffd439"
    ],
    "vdisks_guids": [
        "ba398bee-d9a0-4a80-9222-3220c313f63e",
        "da1657cb-f42e-4678-85bf-d4d6eec4beae"
    ],
    "status": "OK",
    "statistics": {
        ...
    },
    "stored_data": 0,
    "pmachine_guid": "64a60bb0-094a-4e6b-8e73-253a61fd63d3",
    "storagedrivers_guids": [
        "8ceb873a-7b60-4a8f-b91f-c373ce65ea07"
    ]
}
```

### StorageRouter actions

Main actions:

-   List: GET
-   Resource: GET

| Action |  Action/Link | Type | Input | Response |
|--------|--------------|------|-------|----------|
| Move all vDisks away from this StorageRouter, if possible | move_away | POST | | GUID of a Celery task |
| Get a list of available actions | get_available_actions | GET | | List of actions |
| Gets physical metadata of the StorageRouter | get_physical_metadata | POST | {"files": "<comma separated list of files to check for existence>"} | GUID of a Celery task |
| Gets package version info | get_version_info | GET | | GUID of a Celery task |
| Checks S3 connectivity | check_s3 | POST | {"host": str, s3 host, "port": str, s3 port, "accesskey": str, s3 access key, "secretkey": str, s3 secret key} | GUID of a Celery task |
| Checks whether a mountpoint path is empty | check_mtpt | POST | {"name": str, mountpoint path} | GUID of a Celery task |
| Adds a vPool | add_vpool | POST | {"call_parameters": {"vpool_name": str, vpool name, "type": str, vpool type, "connection_host": str, host, "connection_port": int, port, "connection_timeout": int, timeout, "connection_username": str, username, "connection_password": str, password, "mountpoint_temp": str, mountpoint, "mountpoint_bfs": str, mountpoint, "mountpoint_md": str, mountpoint, "mountpoint_readcache1": str, mountpoint, "mountpoint_readcache2": str, mountpoint, "mountpoint_writecache": str, mountpoint, "mountpoint_foc": str, mountpoint, "storage_ip": str, ip, "vrouter_port": int, port } } | GUID of a Celery task |

| Update the Volume Driver on all Storage Routers | update_volumedriver | POST |  | GUID of a Celery task |
| Configure a disk | configure_disk | POST | {"disk_guid":str, GUID of the disk, "offset": int, offset, "size": int, size in , "roles": str in [WRITE, DB, SCRUB], role, "partition_guid": str, GUID of the partition, optional } | GUID of a Celery task |
| Rescan disks | rescan_disks | POST |  | GUID of a Celery task |
| Refresh all hardware parameters | refresh_hardware | POST |  | GUID of a Celery task |
