

## Django stack
Our django stack is located in the `webapps.api` folder. We only use a subset of the whole Django functionality.
 - Authentication
 - Django rest framework + views
 - serialization: customized, as Django cannot cope too well with our DAL structure. We don't use their ORM for this reason.
 Django relies heavily on their own ORM, which we exclude completely. Due to this, their default serialization cannot be used and we have to implement our own serialization (`FullSerializer`)
 
 This is hammering Django just hard enough to make it fit our stack
 A Better idea would have been to use flask from the start, as this provides way more liberty in implementation and doesn't rely on an ORM.
 Work has been done to migrate from DFR (Django rest-framework) towards flask, since our current DFR version is harder to control and customize, especially when also upgrading to python 3.x.
 This transition has been laborous and is unfinished.
### Default Django files and contents
- wsgi.py
- urls.py: implementation of the Django simplerouter. An own implementation is needed to backport features introduced in newer django versions.
implements static and dynamic list and detail routes, and functions to access these.
The default django `urlpatterns` variable contains routes for the built views:


|routes  | views  |
|---|---|
| ovs api calls  |  `def build_router_urls` |
| authentication, identification, plugin metadata |  `metadata.py`|
| openAPI according to swagger| `openapi.py`|
| relay | `view.py`, `def relay` |
| oath2 token creation and redirection | `oath2` module |
The `PluginController.get_webapps` fetches all viewclasses in `api/backend/views`.

All ovs implemented routes in this module are registered through the base django rest framework router. 
   
- settings.py
- manage.py

## Frontend stack

Our frontend relies on the [Durandal framework](http://durandaljs.com), which in turn uses Knockout for databinding. The GUI is implemented as an MVC, and this is projected in the structure of the webapps.
Javascript version used is ES4, with certain ES5 backports.

### GUI entry point
`main.js`
 - configure some i8n and plugin settings
 - set application root to `shell.js`

`shell.js`
 - loads in all routes, build navigation model and direct unknown routes
 - loads in layout via css
 - loads in translation
 - loads in backend module if needed
 - activate router
 
`index.js` :
 - adds guarding to routes if necessary 
 - maps routes to modules (via durandal `childrouter.map`)
 - handles logging in
 - directs to landing page `viewmodels/index`
 

 
### Pluginloader
Loads pages by fetching hooks for all plugins. Supported plugins so far: alba and iSCSI.
The hooks that are found (`systems.acquire(<filename>`)) in the provided folders, are stored in the `routing.extraRoutes` and `routing.extraPatches`
from the `ovs.routing.js` object, as well as saved in a viewcache (`shell.js`)