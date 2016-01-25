### Users

### User object structure

-   guid: GUID of the User.
-   username: Username of the User.
-   is_active: Boolean indicating if the User is active or disabled.
-   language: Language of the User.
-   group_guid: GUID of the Group to which the User belongs.
-   clients_guids: A list of Client GUIDs available on this User.

### Example data

```
{
    "guid": "26b78431-7eb1-4df2-95cb-8af3be883f54",
    "language": "en-US",
    "username": "admin",
    "is_active": true,
    "group_guid": "f5844c80-837b-49c4-a26c-6a261cd5ed3c",
    "clients_guids": [
        "89f162df-d8f9-4e35-b841-681a1cd42544",
        "332e1aa8-b796-43fa-8c7a-d61d3589d00b"
    ]
}
```

### User actions

Main actions:

-   List: GET, POST
-   Resource: GET, POST, PATCH, DELETE

| Action |  Action/Link | Type | Input | Response |
|--------|--------------|------|-------|----------|
| Set password | set_password | POST | {"current_password": str, current password, "new_password": str, new password} | Object data or 400 (Bad request) |

