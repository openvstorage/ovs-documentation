#### ASD Manager
The ASD Manager is the software that turns standard SATA drives into ASDs you can use with ALBA.

##### Turn a JBOD into an ASD node
On the compute nodes, make sure the Open vStorage Backend packages are installed (`apt-get install openvstorage-hc`).

###### On the storage server:
* Make sure that the storage server has an OS disk and at least 3 additional empty SATA disks.
* Install the storage server with Ubuntu LTS 14.04.2. The storage server should be added in the same network as the compute hosts.
* Execute following to setup the program that will manage the disks of the storage server:
```
echo "deb http://apt.openvstorage.org eugene main" > /etc/apt/sources.list.d/ovsaptrepo.list

apt-get update
apt-get install openvstorage-sdm
```
* Retrieve the automatically generated password from the config
```
cat /opt/alba-asdmanager/config/config.json | grep password
        "password": "u9Q4pQ76e0VJVgxm6hasdfasdfdd",
```

###### On the compute host:

* Login and go to Backends.
* Click the add Backend button, specify a name and select Open vStorage Backend as type. Click Finish and wait until the status becomes green.
* In the Backend details page of the freshly created Backend, click the Discover button. Wait until the storage server pops up and click on the + icon to add it. When asked for credentials use root as login and the password retrieved above.
* Next follow the standard procedure to claim the disks of the storage server and add them to the Backend.


#### Basic ASD Manager commands


##### Start the ASD manager
To start an ASD manager execute
```
start alba-asdmanager
```

##### Retart the ASD manager
To restart an ASD manager execute
```
restart alba-asdmanager
```

##### Stop the ASD manager
To Stop an ASD manager execute
```
stop alba-asdmanager
```

##### Display the ASD manager status
To display the status of an ASD manager execute
```
status alba-asdmanager
alba-asdmanager start/running, process 18652
```



port 8500


root@cmp02:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 0c:c4:7a:59:3a:c0 brd ff:ff:ff:ff:ff:ff
    inet 172.19.5.31/24 brd 172.19.5.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::ec4:7aff:fe59:3ac0/64 scope link
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 0c:c4:7a:59:3a:c1 brd ff:ff:ff:ff:ff:ff
4: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether f6:8c:3b:6e:db:38 brd ff:ff:ff:ff:ff:ff
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
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
}root@cmp02:~# status alba-asdmanager
alba-asdmanager start/running, process 18652
root@cmp02:~# ovs
Python 2.7.6 (default, Jun 22 2015, 17:58:13)
Type "copyright", "credits" or "license" for more information.

IPython 1.2.1 -- An enhanced Interactive Python.
?         -> Introduction and overview of IPython's features.
%quickref -> Quick reference.
help      -> Python's own help system.
object?   -> Details about 'object', use 'object??' for extra details.

In [1]: from ovs.extensions.plugins.
ovs.extensions.plugins.albacli     ovs.extensions.plugins.asdmanager

In [1]: from ovs.extensions.plugins.asdmanager import ASDManagerClient

In [2]: ASDManagerClient.
ASDManagerClient.add_disk                ASDManagerClient.get_disks               ASDManagerClient.get_update_information  ASDManagerClient.restart_disk
ASDManagerClient.execute_update          ASDManagerClient.get_ips                 ASDManagerClient.mro                     ASDManagerClient.restart_services
ASDManagerClient.get_disk                ASDManagerClient.get_metadata            ASDManagerClient.remove_disk             ASDManagerClient.set_ips

In [2]: ASDManagerClient.
ASDManagerClient.add_disk                ASDManagerClient.get_disks               ASDManagerClient.get_update_information  ASDManagerClient.restart_disk
ASDManagerClient.execute_update          ASDManagerClient.get_ips                 ASDManagerClient.mro                     ASDManagerClient.restart_services
ASDManagerClient.get_disk                ASDManagerClient.get_metadata            ASDManagerClient.remove_disk             ASDManagerClient.set_ips

In [2]: ASDManagerClient?
Type:       type
String Form:<class 'ovs.extensions.plugins.asdmanager.ASDManagerClient'>
File:       /opt/OpenvStorage/ovs/extensions/plugins/asdmanager.py
Docstring:  <no docstring>
Constructor information:
 Definition:ASDManagerClient(self, node)

In [3]: ASDManagerClient??

In [4]: from ovs.dal.lists
ovs.dal.lists

In [4]: from ovs.dal.lists.
ovs.dal.lists.albabackendlist    ovs.dal.lists.bearertokenlist    ovs.dal.lists.diskpartitionlist  ovs.dal.lists.mdsservicelist     ovs.dal.lists.servicelist        ovs.dal.lists.userlist
ovs.dal.lists.albanodelist       ovs.dal.lists.brandinglist       ovs.dal.lists.failuredomainlist  ovs.dal.lists.mgmtcenterlist     ovs.dal.lists.servicetypelist    ovs.dal.lists.vdisklist
ovs.dal.lists.backendlist        ovs.dal.lists.clientlist         ovs.dal.lists.grouplist          ovs.dal.lists.pmachinelist       ovs.dal.lists.storagedriverlist  ovs.dal.lists.vmachinelist
ovs.dal.lists.backendtypelist    ovs.dal.lists.disklist           ovs.dal.lists.licenselist        ovs.dal.lists.rolelist           ovs.dal.lists.storagerouterlist  ovs.dal.lists.vpoollist

In [4]: from ovs.dal.lists.alb
ovs.dal.lists.albabackendlist  ovs.dal.lists.albanodelist

In [4]: from ovs.dal.lists.albanodelist import A
AlbaNode      AlbaNodeList

In [4]: from ovs.dal.lists.albanodelist import AlbaNode
AlbaNode      AlbaNodeList

In [4]: from ovs.dal.lists.albanodelist import AlbaNodeList

In [5]: n = AlbaNodeList.
AlbaNodeList.get_albanode_by_ip       AlbaNodeList.get_albanode_by_node_id  AlbaNodeList.get_albanodes            AlbaNodeList.mro

In [5]: n = AlbaNodeList.get
AlbaNodeList.get_albanode_by_ip       AlbaNodeList.get_albanode_by_node_id  AlbaNodeList.get_albanodes

In [5]: n = AlbaNodeList.get_albanodes()

In [6]: n = AlbaNodeList.get_albanodes()[0]

In [7]: c = ASDManagerClient()
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-7-b64f6c4a8020> in <module>()
----> 1 c = ASDManagerClient()

TypeError: __init__() takes exactly 2 arguments (1 given)

In [8]: c = ASDManagerClient(n)

In [9]: c
%%capture       %colors         %cpaste         callable        celerybeat.pid  class           clear           coerce          complex         continue        cp
%cd             %config         c               cat             chr             classmethod     cmp             compile         config/         copyright       credits

In [9]: c
%%capture       %colors         %cpaste         callable        celerybeat.pid  class           clear           coerce          complex         continue        cp
%cd             %config         c               cat             chr             classmethod     cmp             compile         config/         copyright       credits

In [9]: c.
c.add_disk                c.get_disk                c.get_ips                 c.get_update_information  c.remove_disk             c.restart_services
c.execute_update          c.get_disks               c.get_metadata            c.node                    c.restart_disk            c.set_ips

In [9]: c.get
c.get_disk                c.get_disks               c.get_ips                 c.get_metadata            c.get_update_information

In [9]: c.get_disks[]
  File "<ipython-input-9-4dc735de26a1>", line 1
    c.get_disks[]
                ^
SyntaxError: invalid syntax


In [10]: c.get_disks()
Out[10]:
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

In [14]: c.restart_disk('ata-HGST_HUS724020ALA640_PN2134P5G6DHNX')
Out[14]:
{u'_error': u'',
 u'_link': u'/disks/ata-HGST_HUS724020ALA640_PN2134P5G6DHNX',
 u'_success': True,
 u'_version': 1}
