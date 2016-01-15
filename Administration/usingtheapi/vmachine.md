### vMachines

### vMachine object structure

-   guid: GUID of the vMachine.
-   hypervisorid: Id of the vMachines on the Hypervisor.
-   description: Description of the vMachine.
-   name: Name of the vMachine.
-   status: Internal status of the vMachine. Possible values are OK,
    NOK, CREATED, SYNC, SYNC_NOK.
-   is_vtemplate: Boolean indicating whether this vMachine is a
    vTemplate.
-   devicename: The name of the container file (e.g. the VMX-file)
    describing the vMachine.
-   snapshots: List of vMachine snapshots.
-   hypervisor_status: Status of the vMachine on the Hypervisor.
-   statistics: Aggregated statistics from all this vMachine's vDisks.
    See vDisk.statistics.
-   stored_data: Total size in Bytes of the current data and the
    Snapshots without the overhead imposed by the Backend redundancy.
-   dtl_mode: Status of the Distributed Transaction Log.
-   storagerouters_guids: A list of StorageRouters serving this
    vMachine's vDisks.
-   vpools_guids: A list of vPools used by this vMachine's vDisks.
-   pmachine_guid: GUID of the pmachine running the vMachine.
-   vpool_guid: GUID of the pmachine hosting the vMachine config file.
-   vdisks_guids: List of the vMachine's vDisks.

### Example data

```
{
    "guid": "86041df1-fa32-4377-97ed-e07cfc67d896",
    "hypervisor_id": "1195",
    "description": "",
    "name": "test",
    "status": "SYNC",
    "is_vtemplate": false,
    "devicename": "test/test.vmx",
    "failover_mode": "OK_STANDALONE",
    "storagerouters_guids": [
        "a8743428-9bb8-478f-ac4e-366d273db96d"
    ],
    "vpools_guids": [
        "e9146a46-2ca2-4f09-93f4-9101e0ffd439"
    ],
    "snapshots": [
        {
            "timestamp": "1412154003",
            "snapshots": {
                "da1657cb-f42e-4678-85bf-d4d6eec4beae": "6c6b2a49-7b4e-4fd1-954d-be8273a9711f"
            },
            "label": "",
            "is_automatic": true,
            "stored": 0,
            "is_consistent": false
        }
    ],
    "hypervisor_status": "HALTED",
    "statistics": {
        ...
    },
    "stored_data": 0,
    "pmachine_guid": "64a60bb0-094a-4e6b-8e73-253a61fd63d3",
    "vpool_guid": "e9146a46-2ca2-4f09-93f4-9101e0ffd439",
    "vdisks_guids": [
        "da1657cb-f42e-4678-85bf-d4d6eec4beae"
    ]
}
```

### vMachine actions

Main actions:

-   List: GET
-   Resource: GET

| Action |  Action/Link | Type | Input | Response |
|--------|--------------|------|-------|----------|
| Rollback a vMachine | rollback | POST | {"timestamp": int, timestamp to rollback too} | GUID of a Celery task |
| Snapshot a vMachine | snapshot | POST | {"name": "str, name of the snapshot, "consistent" : boolean, label the snapshot as consistent, "sticky": boolean, label the snapshot as sticky } | GUID of a Celery task |
| Clone a vMachine from snapshot | clone_from_snapshot | POST | {"snapshot_timestamp": int, timestamp of the snapshot, "new_machine_name": "str, name of the vmachine} | GUID of a Celery task |
| Delete snapshot | delete_snapshot | POST | {"snapshot_timestamp": int, timestamp of the snapshot to delete} | GUID of a Celery task |
| Get the children from a vTemplate | get_children | GET | | List of GUIDs or a list of vMachine objects, depending on the contents |
| Set as vTemplate | set_as_template | POST | | GUID of a Celery task |
| Create from vTemplate | create_from_template | POST | {"pmachineguid": str, GUID of target pMachine, "name": str, name of the new vMachine, "description": str, description of the new vMachine} | GUID of a celery task |
| Create multiple vMachines from vTemplate | create_multiple_from_template | POST | {"pmachineguids": [str], GUIDs of the pMachines, "amount": int, amount of vMachines, "start": int, start number, "name": str, name of vMachines, "description": str, description of vMachines} | GUID of a celery task |
| Remove a vTemplate | destroy | POST | | GUID of a Celery task |
| Get a list of target pMachines to which a vTemplate can be cloned | get_target_pmachines | GET | | List of pMachine GUIDs |


