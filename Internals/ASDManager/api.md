##### ASD Manager API
The ASD Manager exposes an API on port 8500. The API can be used to manage the ASDs inside the node. The API can only be used on ASD nodes which are already added to an Open vStorage environment.


###### Retrieving the username and password
The username and password for authentication can be found in the config file of the ASD node `/opt/alba-asdmanager/config/config.json`:
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

###### Some basic examples
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