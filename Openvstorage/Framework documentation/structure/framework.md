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
Both can be accessed the same way however: 
`DALObject.property` 
- relations: These relations reflect junctions between 2 DAL objects that are not a one-to-one or one-to-many relationship:
these junctions are the many-to-many relationships that are needed in every abstractionlayer of a DB.

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
