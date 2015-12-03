### API examples

** Note: Make sure to escape your Client Secret if necessary (adding backslashes before e.g. semi colons, at signs, dashes, ..)**

#### OVS Client
In the below examples we will retrieve the vDisks in an Open vStorage Cluster and display the details of the first vDisk.
Start OVS
```
ovs
```
Load the client and get the vDisks
```
In [1]: from ovs.extensions.api.client import OVSClient
In [2]: client = OVSClient("172.19.5.31", 443, ["11761f88-11d0-40ad-a1c3-09e181869523", "PNdh;+:.MtMMowo[Zc8Iv!JMRHy*v<svfTrKsE2H0tw#!ruJneVq\"mhl6R@T,B0]"])
In [3]: client.get('/vdisks/')
Out[3]:
{u'_contents': None,
 u'_paging': {u'current_page': 1,
  u'end_number': 15,
  u'max_page': 1,
  u'start_number': 1,
  u'total_items': 15},
 u'_sorting': None,
 u'data': [u'0e0f3d45-d981-4dc3-b2f6-f8b02b5e141c',
  u'216f33c6-21e0-4c8a-87ba-503bfbdea042',
  u'22e3fcf1-80f2-4b0e-8e35-ceb0a92d6cde',
  u'fafe18dd-ff21-4d5c-8010-ef674efa2231']}
In [4]: client.get('/vdisks/{0}/'.format('0e0f3d45-d981-4dc3-b2f6-f8b02b5e141c'))
Out[4]:
{u'child_vdisks_guids': [],
 u'cinder_id': None,
 u'description': None,
 u'devicename': u'sa/disk-3-5.raw',
 u'guid': u'0e0f3d45-d981-4dc3-b2f6-f8b02b5e141c',
...
```

#### Command line
In the below example we will retrieve the Storage Routers in an Open vStorage Cluster.
```
IP=10.100.169.100
CLIENTSECRET=\@GXgYvjthI6P2VP1\{\|XV{\@\@qA\'\!ti\"Mivxrs\-IP+gVD+~K\?wKl+HwKN\/5eaasqq\;
CLIENTID=8f3ceef2-8fe2-4c89-b867-b52608828bb6
TOKEN=`curl -X POST -u $CLIENTID:$CLIENTSECRET -d "grant_type=client_credentials" -s -k https://$IP/api/oauth2/token/ | python -c 'import json,sys;d=json.load(sys.stdin);print d["access_token"]'`
curl -X GET -H "Accept: application/json; version=1" -H "Authorization: Bearer $TOKEN" -k https://$IP/api/storagerouters/
```

Result:

```
["122bae9d-4321-475c-a2bf-d39a3126ffbe"]
```

#### Python
In the below example we will retrieve the Storage Routers in an Open vStorage Cluster.

```python
#!/usr/bin/python

import sys
import urllib2
import urllib
import json
import base64

ip = '10.100.169.100'
client_id = '8f3ceef2-8fe2-4c89-b867-b52608828bb6'
client_secret = '@GXgYvjthI6P2VP1{|XV{@@qA\'!ti"Mivxrs-IP+gVD+~K?wKl+HwKN/5eaasqq;'

headers = {'Accept': 'application/json'}
auth_url = 'https://{0}/api/oauth2/token/'.format(ip)
base_url = 'https://{0}/api/'.format(ip)

headers['Authorization'] = 'basic {0}'.format(base64.encodestring('{0}:{1}'.format(client_id, client_secret)).strip())
request = urllib2.Request(auth_url, data=urllib.urlencode({'grant_type': 'client_credentials'}), headers=headers)
response = urllib2.urlopen(request).read()

token = json.loads(response)['access_token']

headers['Authorization'] = 'Bearer {0}'.format(token)
headers['Accept'] = 'application/json; version=*'

request = urllib2.Request(base_url + 'storagerouters/', None, headers=headers)
response = urllib2.urlopen(request).read()

data = json.loads(response)

print json.dumps(data, sort_keys=True, indent=4)
```

Result:

```
[
    "122bae9d-4321-475c-a2bf-d39a3126ffbe"
]
```