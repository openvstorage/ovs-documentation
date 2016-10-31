### vDisks

### vDisk object structure

-   guid: GUID of the vDisk.
-   volume_id: ID of the vDisk in the Open vStorage Volume Driver.
-   description: Description of the vDisk.
-   cinder_id: ID in OpenStack Cinder (only applicable in case of
    OpenStack).
-   name: Name of the vDisk.
-   parentsnapshot: Points to a parent voldrvsnapshotid. None if there
    is no parent Snapshot.
-   is_vtemplate: Boolean indicating whether this vDisk is a
    vTemplate.
-   order: Order with which vDisk is attached to a vMachine. None if not
    attached to a vMachine.
-   devicename: The name of the container file (e.g. the VMDK-file)
    describing the vDisk.
-   size: Size of the vDisk in Bytes.
-   storagedriver_id: ID of the StorageDriver currently hosting the
    vDisk.
-   snapshots: List of vDisk snapshots.
-   storagerouter_guid: GUID of the StorageRouter currently running the
    vDisk.
-   info: Information of the vDisks stored by the vStorage Driver.
    -   failover_port: Port on which to connect for the FailOver Cache.
    -   parent_namespace: (obsolete).
    -   failover_mode: Status of the FailOver Cache.
    -   stored: total Size in Bytes of the current data and the
        Snapshots without the overhead imposed by the Backend
        redundancy.
    -   vrouter_id: Id of the Virtual Storage Router application
        serving the vDisk.
    -   object_type: Type of the vDisk.
    -   namespace: Namespace of the vDisk on the Storage Backend.
    -   halted: (obsolete).
    -   volume_size: Provisioned size in Bytes.
    -   parent_snapshot_id: (internal).
    -   lba_size: size of the LBA unit.
    -   parent_volume_id: guid of the parent of the vDisk. Empty
        string if no parent exists.
    -   cluster_multiplier: cluster size of the vDisk in terms of the
        LBA unit. Default value is 8.
    -   volume_id: ID of the vDisk in the Open vStorage Volume Driver.
    -   footprint: amount of LBA addresses written in Bytes.
    -   lba_count: (obsolete).
    -   sco_multiplier: size of a SCO in cluster units. Default value
        is 1024.
    -   failover_ip: IP address of the GSR where the FailOver Cache is
        hosted.
-   statistics: Performance statistics for the storage of the vDisk.
    -   operations: Total amount of IO operations.
    -   cluster_cache_misses_ps: Amount of misses to the Content
        Based Cluster Cache (read cache) per second averaged over the
        last time it was requested. Zero in case no request was made in
        the last 10 seconds.
    -   data_read: Amount of data (in Bytes) read by the vMachine.
    -   sco_cache_misses: Amount of misses to the SCOcache (read/write
        cache).
    -   sco_cache_hits_ps: Amount of hits to the SCOcache (read/write
        cache) per second averaged over the last time it was requested.
        Zero in case no request was made in the last 10 seconds.
    -   sco_cache_hits: Amount of hits to the SCOcache (read/write
        cache).
    -   write_operations: Amount of write operations performed by the
        vMachine.
    -   cluster_cache_misses: Amount of misses to the Content Based
        Cluster Cache (read cache).
    -   read_operations_ps: Amount of read operations performed by the
        vMachine averaged over the last time it was requested. Zero in
        case no request was made in the last 10 seconds.
    -   sco_cache_misses_ps: Amount of misses to the SCOcache
        (read/write cache) averaged over the last time it was requested.
        Zero in case no request was made in the last 10 seconds.
    -   backend_write_operations: Amount of write operations to the
        Storage Backend.
    -   metadata_store_hits_ps: Amount of hits for metadata pages
        averaged over the last time it was requested. Zero in case no
        request was made in the last 10 seconds.
    -   metadata_store_misses: Amount of misses for metadata pages.
    -   backend_data_written: Amount of data (in Bytes) writen to the
        Storage Backend.
    -   data_read_ps: Amount of data (in Bytes) read by the vMachine
        averaged over the last time it was requested. Zero in case no
        request was made in the last 10 seconds.
    -   read_operations: Amount of read operations performed by the
        vMachine.
    -   cluster_cache_hits: Amount of hits to the Content Based
        Cluster Cache (read cache).
    -   data_written_ps: Amount of data (in Bytes) written by the
        vMachine averaged over the last time it was requested. Zero in
        case no request was made in the last 10 seconds.
    -   cluster_cache_hits_ps: Amount of hits to the Content Based
        Cluster Cache (read cache) averaged over the last time it was
        requested. Zero in case no request was made in the last 10
        seconds.
    -   cache_hits_ps: Amount of cache hits averaged over the last
        time it was requested. Zero in case no request was made in the
        last 10 seconds.
    -   timestamp: Time when the data was collected.
    -   metadata_store_misses_ps: Amount of misses for metadata pages
        averaged over the last time it was requested. Zero in case no
        request was made in the last 10 seconds.
    -   backend_data_written_ps: Amount of data (in Bytes) writen to
        the Storage Backend averaged over the last time it was
        requested. Zero in case no request was made in the last 10
        seconds.
    -   backend_read_operations: Amount of read operations from the
        Storage Backend.
    -   data_written: Amount of data (in Bytes) written by the
        vMachine.
    -   metadata_store_hits: Amount of hits for metadata pages.
    -   backend_data_read_ps: Amount of data (in Bytes) read from the
        Storage Backend averaged over the last time it was requested.
        Zero in case no request was made in the last 10 seconds.
    -   operations: Total amount of IO operations per second averaged
        over the last time it was requested. Zero in case no request was
        made in the last 10 seconds.
    -   backend_read_operations_ps: Amount of read operations from
        the Storage Backend per second averaged over the last time it
        was requested. Zero in case no request was made in the last 10
        seconds.
    -   data_transferred_ps: Amount of data transferred per second
        between Storage Router and the Storage Backend averaged over the
        last time it was requested. Zero in case no request was made in
        the last 10 seconds.
    -   write_operations_ps: Amount of write operations performed by
        the vMachine per second averaged over the last time it was
        requested. Zero in case no request was made in the last 10
        seconds.
    -   data_transferred: Amount of data transferred per second between
        Storage Router and the Storage Backend.
    -   backend_data_read: Amount of data (in Bytes) writen to the
        Storage Backend.
    -   cache_hits: Amount of cache hits.
    -   backend_write_operations_ps: Amount of write operations to
        the Storage Backend averaged over the last time it was
        requested. Zero in case no request was made in the last 10
        seconds.
    -   operations_ps: Amount of IO operations averaged over the last
        time it was requested. Zero in case no request was made in the
        last 10 seconds.
-   vpool_guid: GUID of the vPool on which the vDisk is stored.
-   vmachine_guid: GUID of the vMachine to which the vDisk is attached.
-   parent_vdisk_guid: GUID of the parent of the vDisk. Null if no
    parent exists.
-   child_vdisks_guids: List of child vDisk GUIDs.
-   domains_dtl: Domains used for DTL if configured.


### Example data

```
{
    "guid": "ba398bee-d9a0-4a80-9222-3220c313f63e",
    "volume_id": "43ac680c-40c9-4024-915f-23bfa0b7f3a2",
    "description": null,
    "cinder_id": null,
    "name": "Hard disk 1",
    "parentsnapshot": null,
    "order": 0,
    "devicename": "template/template.vmdk",
    "size": 42949672960,
    "storagedriver_id": "local0050569647a1",
    "snapshots": [
        {
            "timestamp": "1412150403",
            "label": "",
            "is_automatic": true,
            "stored": 0,
            "is_consistent": false,
            "guid": "53b23161-7e7b-42b7-af01-df5540afde56"
        }
    ],
    "storagerouter_guid": "a8743428-9bb8-478f-ac4e-366d273db96d",
    "info": {
        "failover_port": 0,
        "parent_namespace": "",
        "failover_mode": "OK_STANDALONE",
        "stored": 0,
        "vrouter_id": "local0050569647a1",
        "object_type": "TEMPLATE",
        "namespace": "43ac680c-40c9-4024-915f-23bfa0b7f3a2",
        "halted": false,
        "volume_size": 42949672960,
        "parent_snapshot_id": "",
        "lba_size": 512,
        "parent_volume_id": "",
        "cluster_multiplier": 8,
        "volume_id": "43ac680c-40c9-4024-915f-23bfa0b7f3a2",
        "footprint": 0,
        "lba_count": 83886080,
        "sco_multiplier": 1024,
        "failover_ip": ""
    },
    "statistics": {
        "operations": 0,
        "data_written_ps": 0,
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
        "cluster_cache_misses_ps": 0,
        "cluster_cache_hits_ps": 0,
        "cache_hits_ps": 0,
        "timestamp": 1412169400.884161,
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
    "vpool_guid": "e9146a46-2ca2-4f09-93f4-9101e0ffd439",
    "vmachine_guid": "67328b8e-d2a8-4d3b-979d-f39362df9471",
    "parent_vdisk_guid": null,
    "child_vdisks_guids": [
        "da1657cb-f42e-4678-85bf-d4d6eec4beae"
    ]
}
```

### vDisk actions

Main actions:

-   List: GET
-   Resource: GET

| Action |  Action/Link | Type | Input | Response |
|--------|--------------|------|-------|----------|
| Rollback a vDisk | rollback | POST | `{"timestamp": int, timestamp to rollback to}` | GUID of a Celery task |
| Update the parameters of a vDisk | set_config_params | POST | `{"set_config_params" : {"dtl_mode": str in [sync, a_sync, no_sync],"sco_size": int in [4, 8, 16, 32, 64, 128],"dtl_target": str, ip,"write_buffer": int, min 128, max 10240}}` | GUID of a Celery task |
| Get the parameters of a vDisk | get_config_params | GET |  | JSON containing the config parameters |
| Clone vDisk | clone | POST | `{"name": str, Name for the clone, "storagerouter_guid" : str , GUID of the Storage Router to clone to, "snapshot_id" : str, ID of the snapshot to clone from, optional  }` | GUID of a Celery task |
| Create snapshot | create_snapshot | POST | `{"name": str, Name for the snapshot, "timestamp" : int ,  timestamp of the snapshot, "consistent": boolean, label the snapshot as consistent, optional, "automatic" : boolean, label the snapshot as automatic, optional, "sticky": boolean, label the snapshot as sticky, optional,  "snapshot_id" : str, id of the snapshot, optional  }` | GUID of a Celery task |
| Remove snapshot | remove_snapshot | POST | `{"snapshot_id" : str, ID of the snapshot to remove }` | GUID of a Celery task |
| Set as template | set_as_template | POST |  | GUID of a Celery task |
| Create from template | create_from_template | POST | `{"devicename" : str, name of the new disk, "pachineguid": str,  GUID of pMachine to create new vDisk on, "machineguid": str,  GUID of the vMachine to assign disk to ,optional }` | GUID of a Celery task |
| Delete | delete | POST |  | GUID of a Celery task |
