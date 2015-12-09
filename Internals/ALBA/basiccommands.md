#### Basic ALBA Commands
The OVS python client allow to manage ALBA, the namespaces and the OSDs. Below you can find some basic examples.

##### Display all OSDs in the ALBA backend
```
from ovs.extensions.plugins.albacli import AlbaCLI
from ovs.dal.lists.albabackendlist import AlbaBackendList

for backend in AlbaBackendList.get_albabackends():
   config_file = '/opt/OpenvStorage/config/arakoon/{0}-abm/{0}-abm.cfg'.format(backend.name)
   print backend.name
   osds = AlbaCLI.run('list-osds', config=config_file, as_json=True)
   print osds
```

##### Get the statistics of an ASD
The below code will fetch and display the statistics of the first ASD of a backend.

```
In [1]: from ovs.extensions.plugins.albacli import AlbaCLI

In [2]: from ovs.dal.lists.albabackendlist import AlbaBackendList

In [3]: for backend in AlbaBackendList.get_albabackends():
   ...:        config_file = '/opt/OpenvStorage/config/arakoon/{0}-abm/{0}-abm.cfg'.format(backend.name)
   ...:        print AlbaCLI.run('asd-statistics', long_id=backend.asds[0].asd_id, config=config_file, extra_params='--clear', as_json=True)
   ...:
{u'GetDiskUsage': {u'min': 1.4066696166992188e-05, u'max': 4.982948303222656e-05, u'n': 48, u'm2': 2.5715571894124874e-09, u'var': 5.471398275345718e-11, u'alpha': 0.9, u'avg': 2.3320317268371582e-05, u'exp_avg': 2.380806531316661e-05}, u'Statistics': {u'min': 2.6941299438476562e-05, u'max': 2.6941299438476562e-05, u'n': 1, u'm2': 0.0, u'var': 0.0, u'alpha': 0.9, u'avg': 2.6941299438476562e-05, u'exp_avg': 2.6941299438476556e-06}, u'creation': 1449665830.472146, u'period': 411.1669268608093, u'Range': {u'min': 0.00018310546875, u'max': 0.002727985382080078, u'n': 16, u'm2': 1.0533168360638003e-05, u'var': 7.022112240425335e-07, u'alpha': 0.9, u'avg': 0.0008333474397659303, u'exp_avg': 0.0007093288926755573}, u'Apply': {u'min': 6.985664367675781e-05, u'max': 0.0017130374908447266, u'n': 521, u'm2': 0.00013651837295824298, u'var': 2.6253533261200574e-07, u'alpha': 0.9, u'avg': 0.0005049110602966424, u'exp_avg': 0.0007329513103859234}, u'MultiGet2': {u'min': 7.891654968261719e-05, u'max': 0.0652000904083252, u'n': 484, u'm2': 0.016533840517534103, u'var': 3.4231553866530234e-05, u'alpha': 0.9, u'avg': 0.003124799117569096, u'exp_avg': 0.0019112751364455376}}

```

More commands can be found in the [ALBA CLI documentation](../../Administration/usingthecli/alba.md).

