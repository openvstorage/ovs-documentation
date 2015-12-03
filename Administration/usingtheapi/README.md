## Using the API
### Introduction

Open vStorage comes with a RESTful API. This section is intended for
anyone who wants to integrate their application with Open vStorage. The
API is not only for external developers but is also used by the Open
vStorage GUI. Don't forget to let us know how you integrate with Open
vStorage and if you have issues you can always ask the [Open vStorage
community](https://groups.google.com/forum/#!forum/open-vstorage) for
help.

### Security

The API should only be used over HTTPS. Never relay the API trough an
insecure channel.

### Authentication

The API uses OAuth 2 for authentication. Two grant types are
implemented:

-   Resource Owner Password Credentials Grant
    -   Reserved for the GUI. Do **not** use this grant type for third party applications.
-   Client Credentials
    -   Clients can be created trough the Open vStorage GUI
        (Administration > User Management)
    -   All third party applications should ask the user to create such
        a Client and let the user enter its "Client ID" and "Client
        secret" in the third party application.
    -   All third party applications must store the provided Client
        Credentials in a secure manner. Only the application should be
        able to view/use the Client Credentials.

### Requesting Access Tokens

Once provided with the "Client ID" and "Client secret", an Access Token
can be requested. This Access Token can be used for further
communication with the API.

-   Post variables:
    -   grant_type: "client_credentials"
    -   scope: (optional) a space delimited list of scopes that should
        be linked to the Access token. Omit if all scopes available to
        the Client must be available.
-   Basic authentication
    -   Using "Client ID" and "Client secret"

Example request:

```
POST /api/oauth2/token/ HTTP/1.1
Host: <ip address>
Authorization: Basic MzMyZTFhYTgtYjc5Ni00M2ZhLThjN2EtZDYxZDM1ODlkMDBiOlRaQHl6SUpnIVVCTCw+cFZ9Ik9BUyxyNUVoUmdxSkREXSoycjBLI0NfSjptTiIqQUJfLTA5dFM9OyJDMmtbUjg=
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
```

Returns an "application/json" object with:

-   access_token: The generated Access Token
-   token_type: "bearer"
-   expires_in: 3600 (one hour)

Example response:

```
{
    "access_token": ":kx@m/@r>}3A~O6g27LE8xubMvvbx\"|t\"ThUKR22TL49Ty,>R?-a]|'mj?Wgil*|",
    "token_type": "bearer",
    "expires_in": 3600
}
```

This response is json, so make sure the json is parsed (e.g. on the
above access_token, the double quotes inside the token are escaped)

### Using Access Tokens

The obtained Access Token should be provided on each request to the API
using an Authorization type "Bearer" header.

Example request:

```
GET /api/storagerouters/ HTTP/1.1
Host: <ip address>
Authorization: Bearer :kx@m/@r>}3A~O6g27LE8xubMvvbx"|t"ThUKR22TL49Ty,>R?-a]|'mj?Wgil*|
Accept: application/json; version=1
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded
```

Once the token is expired, a 401 Unauthorized error will be returned,
stating "Token expired". Other possible errors are listed in the "API
information/metadata" chapter in this documentation.

### Versioning

The Open vStorage API supports versioning. All requests to resources
need a version to be specified. This can be a specific version, or \*
for the latest version. The version needs to be specified via the Accept
header.

Example request:

```
GET /api/storagerouters/ HTTP/1.1
Host: <ip address>
Authorization: Bearer :kx@m/@r>}3A~O6g27LE8xubMvvbx"|t"ThUKR22TL49Ty,>R?-a]|'mj?Wgil*|
Accept: application/json; version=1
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded
```

Information regarding the available versions can be fetched from
executing a GET on the root of the API.

### API information/metadata

By issuing a GET to the root of the API, information of the API can be
retrieved, such as the current authentication state, the available
versions, the ip addresses of all Storage Routers and - if authenticated
- information about the current logged in user.

Example request (not authenticating since there's no Authorization
header)

```
GET /api HTTP/1.1
Host: <ip address>
Cache-Control: no-cache
```

Available fields in the response:

-   authenticated: States whether the request was authenticated.
-   authentication_state: Information about the authentication.
    -   "unauthenticated": No Authorization header was given.
    -   "invalid authorization type": When not using a Bearer token
        (e.g. when just using Basic authentication).
    -   "invalid token": The given token was invalid or not found in the
        database.
    -   "token expired": The given token is expired (and is now
        scheduled for removal, so subsequent tries might return "invalid
        token").
    -   "inactive user": The token matched a valid user, but the user's
        status is inactive and should not be used.
    -   "unexpected exception": Most likely, some invalid/corrupt data
        was send to the API.
-   username: The username of the authenticated user, if any.
-   userguid: The GUID (identifier) of the user, if any.
-   storagerouter_ips: The ip addresses of all Storage Routers
    available.
-   versions: All available API versions.
-   roles: All roles/scopes that are available when using this token.

Example response (when not authenticated):

```
{
    "username": null,
    "authenticated": false,
    "roles": [],
    "versions": [
        1
    ],
    "authentication_state": "invalid token",
    "storagerouter_ips": [
        <ip address>,
        <ip address>
    ],
    "userguid": null
}
```

Example response (when authenticated):

```
{
    "username": "admin",
    "authenticated": true,
    "roles": [
        "write",
        "manage",
        "read"
    ],
    "versions": [
        1
    ],
    "authentication_state": null,
    "storagerouter_ips": [
        <ip address>,
        <ip address>
    ],
    "userguid": "26b78431-7eb1-4df2-95cb-8af3be883f54"
}
```

### API Resources

The current API provides access to following resources:

-   branding: Brandings of the Open vStorage node.
-   clients: OAuth 2 clients.
-   groups: The groups a user can belong to.
-   mgmtcenters: Hypervisor management centers (e.g. a VMware VCenter
    server, OpenStack Controller Node).
-   pmachines: The Physical Machines (hypervisors) known to the Open
    vStorage cluster.
-   roles: All different roles/scopes available to groups, clients and
    tokens.
-   storagedrivers: All Storage Driver instances.
-   storagerouters: All Storage Routers of the Cluster, either running
    virtually (e.g. on VMWare) or physical (e.g. on KVM).
-   tasks: The celery tasks which are active, scheduled, reserved or
    revoked. These tasks are the results of actions performed on the
    Open vStorage Cluster.
-   users: The users which are registered in the Open vStorage Cluster.
-   vdisks: Virtual disk served by Open vStorage. vDisks can be part of
    a vMachine or stand-alone.
-   vmachines: The Virtual Machines in the Cluster.
-   vpools: The vPools which can be used to deploy vMachines.

It also provides two "special" resources:

-   messages: Access to the management system for sending messages to
    the GUI by using a long-polling mechanism.
-   statistics: Statistical information about the DAL (Data Abstraction
    Layer).

### General usage

### OPTIONS

When using CORS, each call can be preflighted by sending the OPTIONS
HTTP verb, providing a correct Access-Control-Allow-Origin header.

### GET /api/:resource/

Fetch a list of resource GUIDs or resource objects. This is available on
all resources.

-   Request headers:
    -   Accept
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS only)
-   Query parameters:
    -   sort (optional - no sorting if omitted)
        -   A comma separated list of fields
        -   Prefix fields with a dash (-) for reverse sorting
        -   Dictionary fields can have a key specified (e.g.
            my_propertymy_key)
        -   Example:
            sort=field_one,-field_two,dict_fieldmy_key,other_field
    -   page (optional - all resources if omitted)
        -   An integer indicating which page to return (10 entries per
            page)
        -   Invalid pages will return the first (page < 0) or last
            (page > number of pages) page
        -   Example: page=3
    -   contents (optional - list of GUIDs if omitted)
        -   If this parameter is passed, a list of object will be
            returned instead of a list of GUIDs
        -   If this parameter is passed, all static properties will be
            returned by default
        -   A comma separated list of fields that should be returned
            (next to the static properties)
        -   Special fields can be requested
            -   _dynamics: Include all dynamic properties
            -   _relations: Include foreign keys and lists of primary
                keys of linked objects
        -   Prefix fields with a dash (-) to explicitly exclude them
            (e.g. to exclude one dynamic property when _dynamics was
            passed)
        -   Examples:
            -   contents= (only show static properties)
            -   contents=dynamic_property_1,dynamic_property_2
                (static properties plus 2 dynamic properties)
            -   contents=_dynamic,-dynamic_property_2,_relations
                (static properties, all dynamic properties except for
                dynamic_property_2 plus all relations)
    -   (see resource specific information for more parameters)
-   Response headers:
    -   Content-Type
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS preflight only)
    -   Access-Control-Allow-Credentials (CORS preflight only)
    -   Access-Control-Allow-Headers (CORS preflight only)
    -   Access-Control-Allow-Methods (CORS preflight only)
-   Response object:
    -   A list of GUIDs or a list of objects (depending on the content
        parameter)
    -   Fields containing passwords will never be sent
    -   (see resource specific information for object structure)
-   Status codes:
    -   200 (OK)
    -   401 (Unauthorized)
    -   (see resource specific status codes)

### POST /api/:resource/

Create a new object. Not available for all resources. See for possible
resource actions below.

-   Request headers:
    -   Accept
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS only)
-   Query parameters:
    -   None
-   Response headers:
    -   Content-Type
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS preflight only)
    -   Access-Control-Allow-Credentials (CORS preflight only)
    -   Access-Control-Allow-Headers (CORS preflight only)
    -   Access-Control-Allow-Methods (CORS preflight only)
-   Response object:
    -   One of two possibilities:
        -   The object that was just created
        -   A description of the errors occurred during object creation
-   Status codes:
    -   201 (Created)
    -   400 (Bad request)
    -   401 (Unauthorized)

### GET /api/:resource/:guid/

Fetch a resource. By default only static properties will be returned.

-   Request headers:
    -   Accept
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS only)
-   Query parameters:
    -   contents (optional - list of GUIDs if omitted)
        -   If this parameter is passed, a list of object will be
            returned instead of a list of GUIDs
        -   If this parameter is passed, all static properties will be
            returned by default
        -   A comma separated list of fields that should be returned
            (next to the static properties)
        -   Special fields can be requested
            -   _dynamics: Include all dynamic properties
            -   _relations: Include foreign keys and lists of primary
                keys of linked objects
        -   Prefix fields with a dash (-) to explicitly exclude them
            (e.g. to exclude one dynamic property when _dynamics was
            passed)
        -   Examples: See GET /api/:resource/
    -   (see resource specific information for more parameters)
-   Response headers:
    -   Content-Type
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS preflight only)
    -   Access-Control-Allow-Credentials (CORS preflight only)
    -   Access-Control-Allow-Headers (CORS preflight only)
    -   Access-Control-Allow-Methods (CORS preflight only)
-   Response object:
    -   Fields containing passwords will never be sent
    -   (see resource specific information for object structure)
-   Status codes:
    -   200 (OK)
    -   401 (Unauthorized)
    -   404 (Not found)
    -   (see resource specific status codes)

### DELETE /api/:resource/:guid/

Deletes a given resource

-   Request headers:
    -   Accept
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS only)
-   Query parameters:
    -   None
-   Response headers:
    -   Content-Type
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS preflight only)
    -   Access-Control-Allow-Credentials (CORS preflight only)
    -   Access-Control-Allow-Headers (CORS preflight only)
    -   Access-Control-Allow-Methods (CORS preflight only)
-   Response object:
    -   None
-   Status codes:
    -   204 (No content)
    -   401 (Unauthorized)
    -   404 (Not found)

### PATCH /api/:resource/:guid/

Updates a set of properties of a given resource.

-   Request headers:
    -   Accept
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS only)
-   Query parameters:
    -   None
-   Response headers:
    -   Content-Type
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS preflight only)
    -   Access-Control-Allow-Credentials (CORS preflight only)
    -   Access-Control-Allow-Headers (CORS preflight only)
    -   Access-Control-Allow-Methods (CORS preflight only)
-   Response object:
    -   One of two possibilities:
        -   The object that was just updated
        -   A description of the errors occurred during object update
-   Status codes:
    -   202 (Accepted)
    -   400 (Bad request)
    -   401 (Unauthorized)
    -   404 (Not found)

### POST /api/:resource/:guid/:action

Executes an action on a given resource. Not available for all resources.
See for possible resource actions below.

-   Request headers:
    -   Accept
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS only)
-   Query parameters:
    -   None
-   Response headers:
    -   Content-Type
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS preflight only)
    -   Access-Control-Allow-Credentials (CORS preflight only)
    -   Access-Control-Allow-Headers (CORS preflight only)
    -   Access-Control-Allow-Methods (CORS preflight only)
-   Response object:
    -   (see resource specific information for object structure)
-   Status codes:
    -   200 (OK)
    -   401 (Unauthorized)
    -   404 (Not found)

### GET /api/:resource/:guid/:link

Fetch specialized information related to the given object. Not available
for all resources. See for possible resource actions below.

-   Request headers:
    -   Accept
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS only)
-   Query parameters:
    -   None
-   Response headers:
    -   Content-Type
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS preflight only)
    -   Access-Control-Allow-Credentials (CORS preflight only)
    -   Access-Control-Allow-Headers (CORS preflight only)
    -   Access-Control-Allow-Methods (CORS preflight only)
-   Response object:
    -   (see resource specific information for object structure)
-   Status codes:
    -   200 (OK)
    -   401 (Unauthorized)
    -   404 (Not found)

### Brandings

### Branding object structure

-   guid: GUID of the Branding.
-   productname: Commercial product name in this Branding.
-   is_default: Boolean indicating whether this is the default brand.
-   css: The GUI main css filename.
-   name: The brand name.
-   description: A description of the brand.

### Example data

```
{
    "guid": "208b423f-ce8c-4cb5-8e64-736f179ab5b9",
    "productname": "Open vStorage",
    "is_default": true,
    "css": "bootstrap-default.min.css",
    "name": "Default",
    "description": "Default bootstrap theme"
}
```

### Branding actions

Main actions:

-   List: GET
-   Resource: GET

| Action |  Action/Link | Type | Input | Response |
|----------|---------------|-------|----------|---------------|
| NA | NA | NA | NA | NA |


### Clients

### Client object structure

-   guid: GUID of the Client.
-   name: Name of the Client.
-   ovs_type: Type of the Client within OVS.
-   client_secret: The Client's secret key.
-   grant_type: The Client's grant type.
-   client_id: The Client's ID.
-   user_guid: GUID of the User this Client belongs to.
-   tokens_guids: A list of GUIDS of the bearer tokens that exist for
    this Client.
-   roles_guids: A list of GUIDS of all roles that are active/available
    for this Client.

### Example data

```
{
    "guid": "332e1aa8-b796-43fa-8c7a-d61d3589d00b",
    "name": "test",
    "ovs_type": "USER",
    "client_secret": "TZ@yzIJg!UBL,>pV}\"OAS,r5EhRgqJDD]*2r0K#C_J:mN\"*AB_-09tS=;\"C2k[R8",
    "grant_type": "CLIENT_CREDENTIALS",
    "client_id": "332e1aa8-b796-43fa-8c7a-d61d3589d00b",
    "user_guid": "26b78431-7eb1-4df2-95cb-8af3be883f54",
    "tokens_guids": [],
    "roles_guids": [
        "d3874784-dd97-4784-8d0f-e60d7ce4b04e",
        "d124c2ad-ffda-4cea-a93d-66ef0f796e77",
        "c6dd0fc8-3170-43de-a466-a00bb3eb2d84"
    ]
}
```

### Client actions

Main actions:

-   List: GET
-   Resource: GET, POST, DELETE

| Action | Action/Link | Type | Input | Response |
|--------|-------------|------|-------|----------|
| NA | NA | NA | NA | NA |

### Groups

### Group object structure

-   guid: GUID of the Group.
-   name: Name of the Group.
-   description: Description of the Group
-   users_guids: A list of GUIDS of the Users that have this Group
    configured.
-   roles_guids: A list of GUIDS of the Roles that are active/enabled
    for this Group.

### Example data

```
{
    "guid": "d804177f-985a-441c-8053-b59094a5f1aa",
    "name": "viewers",
    "description": "Viewers",
    "users_guids": [],
    "roles_guids": [
        "65c6807d-4395-4f63-82c2-2e3fb89065b9"
    ]
}
```

### Group actions

Main actions:

-   List: GET
-   Resource: GET

| Action |  Action/Link | Type | Input | Response |
|--------|--------------|------|-------|----------|
| NA | NA | NA | NA | NA |

### Management Centers

### Management center object structure

-   guid: GUID of the Management Center.
-   name: Name of the Management Center.
-   username: Username for the Management Center.
-   ip: IP address of the Management Center.
-   port: Port of the Management Center's API.
-   type: Type of the Management Center.
-   description: Description of the Management Center.
-   hosts: A dynamic list of all hosts that are managed by this
    Management Center, which may or may not be represented by pMachines.
-   pmachine_guids: A list of pMachine GUIDS managed by this Management
    Center.

### Example data

```
{
    "guid": "ad406114-5066-4238-a64f-8ff9ce7aba38",
    "name": "vcenter",
    "username": "root",
    "ip": "10.100.169.250",
    "port": 443,
    "type": "VCENTER",
    "description": null,
    "hosts": {
        "host-140": {
            "ips": [
                "10.100.169.253",
                "172.22.1.1"
            ],
            "name": "10.100.169.253"
        },
        "host-12": {
            "ips": [
                "10.100.169.254",
                "172.22.1.1"
            ],
            "name": "10.100.169.254"
        },
        "host-30": {
            "ips": [
                "10.100.169.242",
                "172.22.1.5",
                "172.23.1.5"
            ],
            "name": "10.100.169.242"
        }
    },
    "pmachines_guids": []
}
```

### Management Center actions

Main actions:

-   List: GET
-   Resource: GET, POST, DELETE

| Action |  Action/Link | Type | Input | Response |
|--------|--------------|------|-------|----------|
| NA | NA | NA | NA | NA |

### pMachines

### pMachine object structure

-   guid: GUID of the pMachine.
-   hypervisor_id: The internal ID of the hypervisor, if available.
-   username: The username to connect to the pMachine.
-   name: The name of the pMachine.
-   ip: The ip address of the pMachine.
-   description: The description of the pMachine.
-   hvtype: The hypervisor type of the pMachine.
-   host_status: The host's status.
-   mgmtcenter_guid: The GUID of the linked Management Center.
-   vmachines_guids: A list of vMachine GUID running on this pMachine.
-   storagerouters_guids: A list of StorageRouter GUIDS that are
    available to this pMachine.

### Example data

```
{
    "guid": "db68cada-f7f3-4830-987b-6491ce02f6de",
    "hypervisor_id": null,
    "username": "root",
    "name": "esxi154",
    "ip": "10.100.169.254",
    "description": null,
    "hvtype": "VMWARE",
    "host_status": "UNKNOWN",
    "mgmtcenter_guid": null,
    "vmachines_guids": [],
    "storagerouters_guids": [
        "d019e8df-b398-4095-9915-516b90780df7"
    ]
}
```

### pMachine actions

Main actions:

-   List: GET
-   Resource: GET, PATCH

| Action |  Action/Link | Type | Input | Response |
|--------|--------------|------|-------|----------|
| NA | NA | NA | NA | NA |

### Roles

### Role object structure

-   guid: GUID of the Role.
-   name: Name of the Role.
-   code: Code of the Role.
-   description: Description of the Role.
-   tokens_guids: A list of Token GUIDs that are currently using this
    Role.
-   clients_guids: A list of Client GUIDs that are currently using this
    Role.
-   groups_guids: A list of Group GUIDs that are currently using this
    Role.

### Example data

```
{
    "guid": "beeff03e-6c7e-491c-bf8f-425aaf6948fe",
    "name": "Manage",
    "code": "manage",
    "description": "Can manage the system",
    "tokens_guids": [
        "a417c519-1d6d-4732-a207-20e475a5653a",
        "bf8a7378-1b3a-434f-bad6-649701ce0e5c",
        "bd6d3c2c-4649-4021-a64e-701cd164b251",
        "705c8ed3-717a-48b9-925e-20ca1063b1ef",
        "e29345eb-514a-4ad5-af78-2a0f2cf4bd3d"
    ],
    "clients_guids": [
        "0bbd0ebd-02c0-441b-a6ee-0c8b40055fc9",
        "d3874784-dd97-4784-8d0f-e60d7ce4b04e"
    ],
    "groups_guids": [
        "14b83622-f11f-4580-a485-ab8ae8326d42"
    ]
}
```

### Role actions

Main actions:

-   List: GET
-   Resource: GET

| Action |  Action/Link | Type | Input | Response |
|--------|--------------|------|-------|----------|
| NA | NA | NA | NA | NA |

### StorageDrivers

### StorageDriver object structure

-   guid: GUID of the StorageDriver.
-   storagedriver_id: ID of the StorageDriver as known by the
    StorageDriver processes.
-   mountpoint_foc: Failover Cache mountpoint.
-   mountpoint: Mountpoint on which the served vPool is available.
-   mountpoint_md: Metadata mountpoint.
-   mountpoint_temp: Temporary data mountpoint (used for e.g.
    scrubbing).
-   mountpoint_writecache: Mountpoint of the StorageDriver's write
    cache.
-   mountpoint_readcache1: First mountpoint for the StorageDriver's
    read cache.
-   mountpoint_readcache2: Second mountpoint for the StorageDriver's
    read cache.
-   mountpoint_bfs: Mountpoint where the actual data is stored, in case
    of a LOCAL or DISTRIBUTED type backend.
-   name: Name of the StorageDriver.
-   description: Description of the StorageDriver.
-   port: Base port of the StorageDriver process.
-   cluster_ip: Ip for inter-StorageDriver communications.
-   storage_ip: Ip on which to expose the mountpoint, in case of
    VMware.
-   statistics: Aggregated statistics from all this StorageDriver's
    vDisks. See vDisk.statistics.
-   status: StorageDriver status.
-   stored_data: Amount of data stored on the StorageDriver.
-   storagerouter_guid: GUID of the StorageRouter running this
    StorageDriver.
-   vpool_guid: GUID of the vPool provided by this StorageDriver.

### Example data

```
{
    "guid": "8ceb873a-7b60-4a8f-b91f-c373ce65ea07",
    "storagedriver_id": "local0050569647a1",
    "mountpoint_foc": "/mnt/cache1",
    "mountpoint": "/mnt/local",
    "mountpoint_md": "/mnt/md",
    "mountpoint_temp": "/var/tmp",
    "mountpoint_writecache": "/mnt/cache1",
    "name": "local0050569647a1",
    "mountpoint_bfs": "/mnt/bfs/local",
    "description": "local0050569647a1",
    "port": 12323,
    "mountpoint_readcache2": "",
    "cluster_ip": "10.100.169.100",
    "storage_ip": "172.22.1.100",
    "mountpoint_readcache1": "/mnt/cache1",
    "statistics": {
        ...
    },
    "status": null,
    "stored_data": 0,
    "storagerouter_guid": "a8743428-9bb8-478f-ac4e-366d273db96d",
    "vpool_guid": "e9146a46-2ca2-4f09-93f4-9101e0ffd439"
}
```

### StorageDriver actions

Main actions:

-   List: GET
-   Resource: GET

| Action |  Action/Link | Type | Input | Response |
|--------|--------------|------|-------|----------|
| Check whether a StorageDriver can be deleted | can_be_deleted | GET | | Boolean indicating whether the StorageDriver can be removed |

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
| Checks S3 connectivity | check_s3 | POST | {"host": "<s3 host>", "port", <s3 port>, "accesskey": "<s3 access key>", "secretkey": "<s3 secret key>"} | GUID of a Celery task |
| Checks whether a mountpoint path is empty | check_mtpt | POST | {"name": "<mountpoint path>"} | GUID of a Celery task |
| Adds a vPool | add_vpool | POST | {"call_parameters": {"vpool_name": "<vpool name>", "type": "<vpool type>", "connection_host": "<host>", "connection_port": <port>, "connection_timeout": <timeout>, "connection_username": "<username>", "connection_password": "<password>", "mountpoint_temp": "<mountpoint>", "mountpoint_bfs": "<mountpoint>", "mountpoint_md": "<mountpoint>", "mountpoint_readcache1": "<mountpoint>", "mountpoint_readcache2": "<mountpoint>", "mountpoint_writecache": "<mountpoint>", "mountpoint_foc": "<mountpoint>", "storage_ip": "<ip>", "vrouter_port": <port> } } | GUID of a Celery task |

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
| Set password | set_password | POST | {"current_password": <current password>, "new_password": <new password>} | Object data or 400 (Bad request) |

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
| Rollback a vDisk | rollback | POST | {"timestamp": \<timestamp\>} | GUID of a Celery task |
| Update the parameters of a vDisk | set_config_params | POST | {"set_config_params" : {"dtl_mode": str in [sync, a_sync, no_sync],"sco_size": int in [4, 8, 16, 32, 64, 128],"dtl_target": str, ip,"dedupe_mode": str in [dedupe, non_dedupe],"write_buffer": int, min 128, max 10240,"cache_strategy": str in [none, on_read, on_write],"readcache_limit": int, min 1, max 10240, optional}} | GUID of a Celery task |


### vMachines

### vMachine object structure

-   guid: GUID of the vMachine.
-   hypervisorid: Id of the vMachines on the Hypervisor.
-   description: Description of the vMachine.
-   name: Name of the vMachine.
-   status: Internal status of the vMachine. Possible values are OK,
    NOK, CREATED, SYNC, SYNC\_NOK.
-   is_vtemplate: Boolean indicating whether this vMachine is a
    vTemplate.
-   devicename: The name of the container file (e.g. the VMX-file)
    describing the vMachine.
-   failover_mode: Status of the Fail-Over Cache.
-   storagerouters_guids: A list of StorageRouters serving this
    vMachine's vDisks.
-   vpools_guids: A list of vPools used by this vMachine's vDisks.
-   snapshots: List of vMachine snapshots.
-   hypervisor_status: Status of the vMachine on the Hypervisor.
-   statistics: Aggregated statistics from all this vMachine's vDisks.
    See vDisk.statistics.
-   stored_data: Total size in Bytes of the current data and the
    Snapshots without the overhead imposed by the Backend redundancy.
-   pmachine_guid: GUID of the Host running the vMachine.
-   vpool_guid: GUID of the vPool where the device is stored.
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
-   Resource: GET, DELETE

| Action |  Action/Link | Type | Input | Response |
|--------|--------------|------|-------|----------|
| Rollback a vMachine | rollback | POST | {"timestamp": <timestamp>} | GUID of a Celery task |
| Snapshot a vMachine | snapshot | POST | {"name": "<name>", "consistent" : <boolean>} | GUID of a Celery task |
| Get the children from a vTemplate | get_children | GET | | List of GUIDs or a list of vMachine objects, depending on the contents |
| Set as vTemplate | set_as_template | POST | | GUID of a Celery task |
| Create from vTemplate | create_from_template | POST | {"pmachineguid": "<GUID of target pMachine>", "name": "<name of the new vMachine>", "description": "<description of the new vMachine>"} | GUID of a celery task |
| Create multiple vMachines from vTemplate | create_multiple_from_template | POST | {"pmachineguids": \["<GUID 1>", "<GUID 2>"\], "amount": <integer>, "start": <integer>, "name": "<name of vMachines>", "description": "<description of vMachines>"} | GUID of a celery task |
| Get a list of target pMachines to which a vTemplate can be cloned | get_target_pmachines | GET | | List of pMachine GUIDs |

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
    -   backend_type: Type of the Storage Backend.
    -   local_connection_path: Local path for the local data, if
        applicable.
-   description: Description of the vPool.
-   type: Type of the Backend.
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
| Sync the vMachines on this vPool | sync_vmachines | POST | | GUID of a Celery task |
| List of StorageRouters serving this vPool | storagerouters | GET | List of StorageRouters |
| Update StorageDrivers serving the vPool | update_storagedrivers | POST | {"storagerouter_guids": <list of StorageRouter GUIDs to append the vPool to (optional)>, "storagedriver_guid": "<GUID of a StorageDriver to use as a blueprint for the new StorageDrivers>", "storagedriver_guids": <optional list of StorageDriver GUIDs to remove>} | GUID of a Celery task |

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

