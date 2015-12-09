#### Volume Driver Python API

##### How to install
```
pip install openvstorage_voldrv
```

##### how to use

```
import openvstorage_voldrv

cl=openvstorage_voldrv.connect()
volume=cl.volume_create(name,size, ...)
print volume.size

for vol in cl.volumes_list():
    print vol.id
    print vol.size
    print vol.caching...

volume=cl.volume_get(id)


```

##### @todo api for stats, creation of volumes, ...
