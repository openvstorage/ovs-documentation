#### ASD Manager API
The ASD Manager exposes an API on port 8500. The API can be used to manage the ASDs inside the node. The API can only be used on ASD nodes which are already added to an Open vStorage environment. At the bottom of the page you can find some [basic examples](#examples).

##### Security

The API should only be used over HTTPS. Never relay the API trough an insecure channel.

##### Authentication

The API uses Basic authentication ("username" and "password"). The username and password for authentication can be found in the config file of the ASD node `/opt/alba-asdmanager/config/config.json`:
```
root@cmp02:~# cat /opt/alba-asdmanager/config/config.json
{
    "main": {
        "username": "root",
        "password": "i4rRUSTyZbHmtowLMhWrAX0mzkMS9gWX",
        "version": 1,
        "node_id": "tsCmJKYNvxctkQhBqNDJBgsmZyw1rdc8"
    },
    "network": {
        "ips": [],
        "port": 8600
    }
}
```

##### Versioning

The ASD Manager API supports versioning. All requests to resources
need a version to be specified. This can be a specific version, or \*
for the latest version. The version needs to be specified via the Accept
header.

### API information/metadata

By issuing a GET to the root of the API

Example request
header)

```
GET / HTTP/1.1
Host: <ip address>
Authorization:  Basic <base 64 encode (username:password)>
Accept: application/json; version=1
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded
```

Available fields in the response:

-   node_id: unique ID of the ASD node.

Example response:

```
{
  "_error": "",
  "_version": 1,
  "_success": true,
  "node_id": "tsCmJKYNvxctkQhBqNDJBgsmZyw1rdc8",
  "_links": [
    "/disks",
    "/net",
    "/update"
  ],
  "_actions": []
}
```


##### API Resources

The current API provides access to following resources:

-   net: network information.
-   disks: ASD and disk information.
-   update: used to update ALBA ASD Manager

###### General usage
.
###### GET /:resource/

Fetch a list of resource GUIDs or resource objects. This is available on
all resources.

-   Request headers:
    -   Accept
        -   application/json
    -    Basic Authentication
-   Query parameters:
    -   None
-   Response headers:
    -   Content-Type
        -   application/json
-   Response object:
    -   A list of GUIDs or a list of objects (depending on the content
        parameter)
    -   Fields containing passwords will never be sent
    -   (see resource specific information for object structure)
-   Status codes:
    -   200 (OK)
    -   401 (Unauthorized)
    -   (see resource specific status codes)

###### POST /:resource/

Create a new object. Not available for all resources. See for possible
resource actions below.

-   Request headers:
    -   Accept
        -   application/json
     -    Basic Authentication
-   Query parameters:
    -   None
-   Response headers:
    -   Content-Type
        -   application/json
-   Response object:
    -   One of two possibilities:
        -   The object that was just created
        -   A description of the errors occurred during object creation
-   Status codes:
    -   201 (Created)
    -   400 (Bad request)
    -   401 (Unauthorized)

###### GET /:resource/:guid/

Fetch a resource. By default only static properties will be returned.

-   Request headers:
    -   Accept
        -   application/json
    -    Basic Authentication
-   Query parameters:
    -   None
-   Response headers:
    -   Content-Type
        -   application/json
-   Response object:
    -   Fields containing passwords will never be sent
    -   (see resource specific information for object structure)
-   Status codes:
    -   200 (OK)
    -   401 (Unauthorized)
    -   404 (Not found)
    -   (see resource specific status codes)


###### POST /:resource/:guid/:action

Executes an action on a given resource. Not available for all resources.
See for possible resource actions below.

-   Request headers:
    -   Accept
        -   application/json
    -    Basic Authentication
-   Query parameters:
    -   None
-   Response headers:
    -   Content-Type
        -   application/json
        -   text/html
-   Response object:
    -   (see resource specific information for object structure)
-   Status codes:
    -   200 (OK)
    -   401 (Unauthorized)
    -   404 (Not found)

###### Net

####### Net object structure

-   ips: IPs of the ASD node.

####### Example data

```
{
  "_error": "",
  "_version": 1,
  "_success": true,
  "ips": [
    "172.19.5.31",
    "192.168.122.1"
  ],
  "_links": [],
  "_actions": [
    "/net"
  ]
}
```

### Net actions

Main actions:

-   Resource: GET, POST

| Action | Action/Link | Type | Input | Response |
|--------|-------------|------|-------|----------|
| NA | NA | NA | NA | NA |

###### Disks
####### Disk object structure

-   available:
-   asd_id: Unique ID of the ASD.
-   log_level: Log level configured for the ASD.
-   name: Name of the ASD as known by the disk controller.
-   devicename: Device path as known by the OS
-   state: State of the ASD
-   node_id: Unique ID of the ASD node the ASD is in.
-   home: Home directory of the ASD on the ASD node.
-   usage: usage statictics of the ASD as reported by `df -h`
-   mountpoint: Mountpoint of the ASD on the ASD Node.
-   port: Port on which the ASD is exposed.

####### Example data

```
{
  "available": false,
  "asd_id": "xV7ipU80ZhAB0cmxaPUFvGSJMkeKAQn3",
  "log_level": "info",
  "name": "ata-HGST_HUS724020ALA640_PN2134P5G6J9UX",
  "_version": 1,
  "_link": "/disks/ata-HGST_HUS724020ALA640_PN2134P5G6J9UX",
  "_success": true,
  "device": "/dev/disk/by-id/ata-HGST_HUS724020ALA640_PN2134P5G6J9UX",
  "state": {
    "state": "ok"
  },
  "node_id": "tsCmJKYNvxctkQhBqNDJBgsmZyw1rdc8",
  "home": "/mnt/alba-asd/xV7ipU80ZhAB0cmxaPUFvGSJMkeKAQn3/data",
  "usage": {
    "available": 1970250104832,
    "used": 29169889280,
    "size": 1999419994112
  },
  "mountpoint": "/mnt/alba-asd/xV7ipU80ZhAB0cmxaPUFvGSJMkeKAQn3",
  "_actions": [
    "/disks/ata-HGST_HUS724020ALA640_PN2134P5G6J9UX/delete"
  ],
  "port": 8602,
  "_error": ""
}
```

####### Disk actions

Main actions:

-   List: GET
-   Resource: GET

| Action | Action/Link | Type | Input | Response |
|--------|-------------|------|-------|----------|
| Add a disk as ASD by name| add | POST | NA | Disk Object |
| Delete a Disks | delete | POST | NA | NA |
| Restart an ASD | restart | POST | NA | NA |

###### Updates

####### Update actions

Main actions:

| Action | Action/Link | Type | Input | Response |
|--------|-------------|------|-------|----------|
| Get the update information | update/information | GET | NA | {"version": Latest available SDM version , "installed": Installed SDM version } |
| Execute an update | update/execute/started|running|done  | POST | NA | {"status": Status of the update } |
| Restart the ASD services | update/restart_services | POST | NA | {"result": Services which were restarted} |

##### <a name="examples"></a>Some basic examples
Start the OVS client
```
ovs
```

Get the first node from the ASD nodes in the Open vStorage environment:

```
In [1]: from ovs.dal.lists.albanodelist import AlbaNodeList

In [2]: n = AlbaNodeList.get_albanodes()[0]
```

Restart one of the ASDs of the node:
```
In [3]: from ovs.extensions.plugins.asdmanager import ASDManagerClient

In [4]: c = ASDManagerClient(n)

In [5]: c.get_disks()
Out[5]:
[{u'asd_id': u'Mgh9EnhFlqhkcHQd8sIELTo4wSUN6V5y',
  u'available': False,
  u'device': u'/dev/disk/by-id/ata-HGST_HUS724020ALA640_PN2134P5G6HUKX',
  u'home': u'/mnt/alba-asd/Mgh9EnhFlqhkcHQd8sIELTo4wSUN6V5y/data',
  u'log_level': u'info',
  u'mountpoint': u'/mnt/alba-asd/Mgh9EnhFlqhkcHQd8sIELTo4wSUN6V5y',
  u'name': u'ata-HGST_HUS724020ALA640_PN2134P5G6HUKX',
  u'node_id': u'Q5D8tBfqkzRjCmEF4I2uZFcSSNSTrzLN',
  u'port': 8602,
  u'state': {u'state': u'ok'},
  u'usage': {u'available': 1971560161280,
   u'size': 1999419994112,
   u'used': 27859832832}},
 {u'asd_id': u'CsYZlADVYbsI3Pq6AcY32zJeiGcHzPNO',
  u'available': False,
  u'device': u'/dev/disk/by-id/ata-HGST_HUS724020ALA640_PN2134P5G6DJKX',
  u'home': u'/mnt/alba-asd/CsYZlADVYbsI3Pq6AcY32zJeiGcHzPNO/data',
  u'log_level': u'info',
  u'mountpoint': u'/mnt/alba-asd/CsYZlADVYbsI3Pq6AcY32zJeiGcHzPNO',
  u'name': u'ata-HGST_HUS724020ALA640_PN2134P5G6DJKX',
  u'node_id': u'Q5D8tBfqkzRjCmEF4I2uZFcSSNSTrzLN',
  u'port': 8601,
  u'state': {u'state': u'ok'},
  u'usage': {u'available': 1972376358912,
   u'size': 1999419994112,
   u'used': 27043635200}},
 {u'asd_id': u'MsxBPzOd2PrGKZ10Mx7JUMQmYXkqnNlu',
  u'available': False,
  u'device': u'/dev/disk/by-id/ata-HGST_HUS724020ALA640_PN2134P5G6DHNX',
  u'home': u'/mnt/alba-asd/MsxBPzOd2PrGKZ10Mx7JUMQmYXkqnNlu/data',
  u'log_level': u'info',
  u'mountpoint': u'/mnt/alba-asd/MsxBPzOd2PrGKZ10Mx7JUMQmYXkqnNlu',
  u'name': u'ata-HGST_HUS724020ALA640_PN2134P5G6DHNX',
  u'node_id': u'Q5D8tBfqkzRjCmEF4I2uZFcSSNSTrzLN',
  u'port': 8600,
  u'state': {u'state': u'ok'},
  u'usage': {u'available': 1971477966848,
   u'size': 1999419994112,
   u'used': 27942027264}},
 {u'asd_id': u'VvlCGqmqDr187XE7U6V4KwuhR4MP0Z3o',
  u'available': False,
  u'device': u'/dev/disk/by-id/ata-HGST_HUS724020ALA640_PN2134P5G6J87X',
  u'home': u'/mnt/alba-asd/VvlCGqmqDr187XE7U6V4KwuhR4MP0Z3o/data',
  u'log_level': u'info',
  u'mountpoint': u'/mnt/alba-asd/VvlCGqmqDr187XE7U6V4KwuhR4MP0Z3o',
  u'name': u'ata-HGST_HUS724020ALA640_PN2134P5G6J87X',
  u'node_id': u'Q5D8tBfqkzRjCmEF4I2uZFcSSNSTrzLN',
  u'port': 8603,
  u'state': {u'state': u'ok'},
  u'usage': {u'available': 1972897153024,
   u'size': 1999419994112,
   u'used': 26522841088}}]

In [6]: c.restart_disk('ata-HGST_HUS724020ALA640_PN2134P5G6DHNX')
Out[6]:
{u'_error': u'',
 u'_link': u'/disks/ata-HGST_HUS724020ALA640_PN2134P5G6DHNX',
 u'_success': True,
 u'_version': 1}
```
