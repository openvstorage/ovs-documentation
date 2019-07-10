

## Django stack
As could be expected, our django stack is located in the `webapps.api` folder. We only use a subset of the whole Django functionality.
We do use their routing mechanism, and their views to implement these. Serialization, however, from api response values is overridden by us, as Django cannot cope too well with our DAL structure. We don't use their ORM for this reason.
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

### GUI entry point
`main.js`
 - configure some i8n and plugin settings
 - set application root to `shell.js`
`index.js` :
 - builds routes 
 - maps routes to modules (via durandal `childrouter.map`)
 - handles logging in
 - directs to landing page `viewmodels/index`
 
`shell.js`
 - loads in all routes, build navigation model and direct unknown routes
 - loads in layout via css
 - loads in translation
 - loads in backend module if needed
 - finally activate router
 
### Pluginloader
Loads pages by fetching hooks for all plugins. Supported plugins so far: alba and iSCSI.
The hooks that are found (`systems.acquire(<filename>`)) in the provided folders, are stored in the `routing.extraRoutes` and `routing.extraPatches`
from the `ovs.routing.js` object, as well as saved in a viewcache (`shell.js`)