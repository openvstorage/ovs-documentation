### Tasks

Tasks behave differently as they don't represent model objects but key
components of the Celery workflow.

### Tasks overview

The overview of all active, scheduled, reserved and revoked Tasks can be
found at the url [https://:ip/api/tasks/](https://:ip/api/tasks/). The
result of this call is an object containing a list of Celery task GUIDs
ordered per type, per worker. Example:

```
{
    "active": {
        "celery@ovs131": []
    },
    "scheduled": {
        "celery@ovs131": []
    },
    "reserved": {
        "celery@ovs131": []
    },
    "revoked": {
        "celery@ovs131": [
            "18143e2f-4fca-4702-b367-1286c892ff83",
            "c747b0c5-1209-429c-9b57-dfcf7eb7e2cf"
        ]
    }
}
```

To see the details of a Task, paste the guid to the end of the Tasks
overview url:
[https://:ip/api/tasks/:guid](https://:ip/api/tasks/:guid). The result
will be an object with the data of the Task.

```
{
    "status": "PENDING",
    "successful": false,
    "failed": false,
    "result": null,
    "ready": false,
    "id": "bd6a5c69-6817-4d6c-b4de-a6e7b47f4f21"
}
```

For each Task following info is provided:

-   status: Status of the Task.
-   successful: Boolean indicating if the Task was executed successful.
-   failed: Boolean indicating if the Task failed.
-   result: Result of the Task.
-   ready: Boolean indicating if the Task is finished.
-   id: Celery id of the Task.

