## Configuration management
Configuration management is where all long-living key-value pairs, such as configuration settings, essential parameters or even short-living keys, for locking of processes.
### Usage
#### Get

Getting a specific key:
```
from ovs.extensions.generic.configuration import Configuration
Configuration.get('key')
```
Inside these values, nested values may be present. For example, if 'key' contains the value
```
{'a_key': 'a_value',
 'b_key': 'b_value'}
 
```
then the `a_key` can be directly accessed via
``` 
Configuration.get('a|a_key')
```

#### List
Not sure what's in the config mgmt?
```python
from ovs.extensions.generic.configuration import Configuration
list(Configuration.list('ovs')) 
```
Will return
```
['alba', 'arakoon', 'framework', 'machines', 'vpools']
```
From where you can further look into specific keys.
Note that this is a generator for memory consumption reasons, and needs to be consumed in a list (iteratable) for any usage but yielding values.
Digging deeper is done as follows:
```python
from ovs.extensions.generic.configuration import Configuration
list(Configuration.list('ovs/alba')) 
```
etc.

#### Set
Works identically to get, just needs a value as extra parameter.
```
Configuration.set('test|a_key', 'test_value')
Configuration.get('test')
> {u'a_key': u'test_value'}
```

### Under the hood
#### Arakoon
As of now, only [arakoon](https://github.com/openvstorage/arakoon-ee) is implemented as backend for persistent storage.

#### Structure
As much code as possible is offloaded to `ovs_extensions`, such as `set`, `get`, `delete`.
Other stuff _needs_ to be implemented in the corresponding implementing manager class, cf Java interfaces. Eg. `get_store_info`

This is to facilitate eventual 
- transition from arakoon to other backends for persistent storage (not to be confused with alba backends) 
- implementation of new managers that need their own config management


#### Passthrough
The `_passthrough` method is the core of the current implementation. This piece of code has been inherited from legacy code, and has been improved and prettyfied ever since. While still pretty ugly, it works, and changes should be made with caution.
The reason why we keep this, is for easy implementation of new persistent clients.

