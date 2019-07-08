## Framework structure

### Packaging
Previously redhat (deprecated), now debian is used as default ubuntu.
This module contains all installfiles that are needed for packaging and pre-, post- and regular installing ovs

### Scripts
Contains everything that is a `.sh` file or as script that needs to be run once.

`/system/ovs.sh` contains the entrypoint to the ovs CLI in the Andes releases. This has been reworked though in develop and master, and this is now offloaded to the python click library.

### Venv

### OVS
#### CLI 
_MASTER ONLY_

This folder contains all logic needed for the `click` library to build our refactored CLI.
Every `ovs` subcommand has its subfolder, except for calls used once (-> `misc`)
The calls are built dynamically starting from this folders' `__init__.py`. Check the `__init.py__`, `commands.py` and `entry.py` files for seeing its magic in action.
Adding commands should be done accordingly: 
- Add the module to the main `__init__.py` if necessary
- implement call in `__init__.py` of module
- add call to group

#### Constants
_MASTER ONLY_
This module contains a multitude of strings of names, paths and other strings that are used throughout the codebase. Equivalent to regular i8n, its purpose is to prevent much refactoring in case some namestrings should change. And it is cleaner, in our opinion.

Usage is pretty straightforward.


#### DAL

Ah, the DAL. A wonderful piece of black magic, obfuscating it design flaws through its obscurity.
The DAL, in its essence, is a comfort (abstraction)layer on top of a db. 
##### Hybrids
Hybrids are name after their, well, _hybrid_ nature.
They contain 
- properties: static, fixed attributes that shouldn't be reloaded, or at least not often.
- dynamics: dynamic, changing attributes that should reflect state or other frequently changing parameters of an object. 

Now, get ready for some obscure magic.
- relations: These relations reflect relations between 2 DAL objects that are a one-to-one or one-to-many relationship.

But wait, there's more!

- These relations only reflect one-to-one or one-to-many relationships. For many-to-many relationships, `junctions` were introduced. These files can be seen in the list of 
the DAL hybrids as files named according to this format: `^j_.*`. 

All these `properties`, `dynamics`, `relations` and `junctions` are manipulated into the DAL object upon creation with some metaprogramming in the `__init__` of the `DataObject` superclass.

All these attributes can be accessed the same way however: 
`DALObject.property` 


#### Lists
This is where the the comfortlayer on top of our sql DB becomes really handy. These lists contain logic to  gracefully implement SQL queries without having to worry about to much sql code.
example:
```
backends =  DataList(Backend, {'type': DataList.where_operator.AND,
                               'items': []})
backends = # add some extra logic here: filtering on names, types, w/e
return backends
```

#### Migration
This section contains code that will be executed when upgrading the ovs framework. When changing from version `x` to `y`, some changes might need to be made on existing (DAL) objects themselves. These objects need to be 'migrated': they need to be manipulated so that they fit the new model. 
Our migration code does this, and will be executed depending on which was the original version of the fwk, and what version one upgrades towards.
This code is summoned from `ovs.lib.update` -> `ovs.lib.migration`

#### Extensions
OVS framework code that belongs nowhere specifically, but is closely related to the ovs core package
### Webapps
Contains all GUI and api related code.


#### Backend
`serializers/serializers.py` contains our serializer, needed for the django rest framework to cope with our DAL datastructure. 

#### Views
Contains all api routes for all fwk DAL objects. Ideally, the views contain as little logic as possible. Logic for the views should be mostly in the DAL lists.
decorators:
 - `log()`: make sure that the api call is logged in `webapp-api.log`
 - `required_roles()`: verify that the apiclient has the correct role for executing this call.
          A list as parameter such as ` required_roles([role_a, role_b])` means that there is an `OR` relationship. Either of these listitem roles can execute this call.
 - `return_list()` or `return_object()` will wrap the return value of the api call in all imaginable metadata needed for pagination, sorting etc.
 - `load()` will make sure the return value will eventually be of given type. 
          eg. `@load('VDisk')` will return an object of type VDisk
          
#### Oath2 
Contains all logic, decorators and others regarding credentials, token generation and verification etc.

#### Misc


### Frontend
All GUI logic
#### App
contains durandals and other JS models (`viewmodels`), HTML `views` and `widgets` such as dropdownboxes, lazylists used throughout the GUI
#### Locales
Internationalization strings obviously
#### CSS
Shouldn't be touched, as our design colors etc were fixed. Errorhandling css can obviously be implemented
