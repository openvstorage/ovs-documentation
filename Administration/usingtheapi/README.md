## Using the API
### Introduction

Open vStorage comes with a RESTful API. This section is intended for
anyone who wants to integrate their application with Open vStorage. The
API is not only for external developers but is also used by the Open
vStorage GUI. Don't forget to let us know how you integrate with Open
vStorage and if you have issues you can always ask the [Open vStorage
community](https://groups.google.com/forum/#!forum/open-vstorage) for
help.

### Browsable API
Open vStorage follows the Open API specification (OAI). This specification is supported by some of the big names in the IT industry such as Google, Microsoft, IBM and PayPal. 
This means some great open-source tools can be leveraged such as Swagger UI.  Swagger UI is a tool that dynamically generates beautiful documentation and a sandbox to play with straight from the browser. 

 
To explore the Open vStorage API, download the [Swagger UI](https://github.com/swagger-api/swagger-ui/releases) , unzip the archive and serve the dist folder from either your file system or a web server.


Next, enter in the textbox `https://[ip of the GUI]/api/swagger.json` and press enter.

![](../../Images/Open vStorage API.png)

You can now browse through the API. As an example you can verify which parameters are required to move a vDisk between to Storage Routers.

![](../../Images/Open vStorage API - move vdisk.png)


### Security

The API should only be used over HTTPS. Never relay the API trough an
insecure channel.

### Authentication

The API uses OAuth 2 for authentication. Two grant types are
implemented:

-   Resource Owner Password Credentials Grant
    -   Reserved for the GUI. Do **not** use this grant type for third party applications.
-   Client Credentials
    -   Clients can be created trough the Open vStorage GUI
        (Administration > User Management)
    -   All third party applications should ask the user to create such
        a Client and let the user enter its "Client ID" and "Client
        secret" in the third party application.
    -   All third party applications must store the provided Client
        Credentials in a secure manner. Only the application should be
        able to view/use the Client Credentials.

### Requesting Access Tokens

Once provided with the "Client ID" and "Client secret", an Access Token
can be requested. This Access Token can be used for further
communication with the API.

-   Post variables:
    -   grant_type: "client_credentials"
    -   scope: (optional) a space delimited list of scopes that should
        be linked to the Access token. Omit if all scopes available to
        the Client must be available.
-   Basic authentication
    -   Using "Client ID" and "Client secret"

Example request:

```
POST /api/oauth2/token/ HTTP/1.1
Host: <ip address>
Authorization: Basic MzMyZTFhYTgtYjc5Ni00M2ZhLThjN2EtZDYxZDM1ODlkMDBiOlRaQHl6SUpnIVVCTCw+cFZ9Ik9BUyxyNUVoUmdxSkREXSoycjBLI0NfSjptTiIqQUJfLTA5dFM9OyJDMmtbUjg=
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
```

Returns an "application/json" object with:

-   access_token: The generated Access Token
-   token_type: "bearer"
-   expires_in: 3600 (one hour)

Example response:

```
{
    "access_token": ":kx@m/@r>}3A~O6g27LE8xubMvvbx\"|t\"ThUKR22TL49Ty,>R?-a]|'mj?Wgil*|",
    "token_type": "bearer",
    "expires_in": 3600
}
```

This response is json, so make sure the json is parsed (e.g. on the
above access_token, the double quotes inside the token are escaped)

### Using Access Tokens

The obtained Access Token should be provided on each request to the API
using an Authorization type "Bearer" header.

Example request:

```
GET /api/storagerouters/ HTTP/1.1
Host: <ip address>
Authorization: Bearer :kx@m/@r>}3A~O6g27LE8xubMvvbx"|t"ThUKR22TL49Ty,>R?-a]|'mj?Wgil*|
Accept: application/json; version=1
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded
```

Once the token is expired, a 401 Unauthorized error will be returned,
stating "Token expired". Other possible errors are listed in the "API
information/metadata" chapter in this documentation.

### Versioning

The Open vStorage API supports versioning. All requests to resources
need a version to be specified. This can be a specific version, or \*
for the latest version. The version needs to be specified via the Accept
header.

Example request:

```
GET /api/storagerouters/ HTTP/1.1
Host: <ip address>
Authorization: Bearer :kx@m/@r>}3A~O6g27LE8xubMvvbx"|t"ThUKR22TL49Ty,>R?-a]|'mj?Wgil*|
Accept: application/json; version=1
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded
```

Information regarding the available versions can be fetched from
executing a GET on the root of the API.

### API information/metadata

By issuing a GET to the root of the API, information of the API can be
retrieved, such as the current authentication state, the available
versions, the ip addresses of all Storage Routers and - if authenticated
- information about the current logged in user.

Example request (not authenticating since there's no Authorization
header)

```
GET /api HTTP/1.1
Host: <ip address>
Cache-Control: no-cache
```

Available fields in the response:

-   authenticated: States whether the request was authenticated.
-   authentication_state: Information about the authentication.
    -   "unauthenticated": No Authorization header was given.
    -   "invalid authorization type": When not using a Bearer token
        (e.g. when just using Basic authentication).
    -   "invalid token": The given token was invalid or not found in the
        database.
    -   "token expired": The given token is expired (and is now
        scheduled for removal, so subsequent tries might return "invalid
        token").
    -   "inactive user": The token matched a valid user, but the user's
        status is inactive and should not be used.
    -   "unexpected exception": Most likely, some invalid/corrupt data
        was send to the API.
-   username: The username of the authenticated user, if any.
-   userguid: The GUID (identifier) of the user, if any.
-   storagerouter_ips: The ip addresses of all Storage Routers
    available.
-   versions: All available API versions.
-   roles: All roles/scopes that are available when using this token.

Example response (when not authenticated):

```
{
    "username": null,
    "authenticated": false,
    "roles": [],
    "versions": [
        1
    ],
    "authentication_state": "invalid token",
    "storagerouter_ips": [
        <ip address>,
        <ip address>
    ],
    "userguid": null
}
```

Example response (when authenticated):

```
{
    "username": "admin",
    "authenticated": true,
    "roles": [
        "write",
        "manage",
        "read"
    ],
    "versions": [
        1
    ],
    "authentication_state": null,
    "storagerouter_ips": [
        <ip address>,
        <ip address>
    ],
    "userguid": "26b78431-7eb1-4df2-95cb-8af3be883f54"
}
```

### API Resources

The current API provides access to following resources:

-   branding: Brandings of the Open vStorage node.
-   clients: OAuth 2 clients.
-   groups: The groups a user can belong to.
-   mgmtcenters: Hypervisor management centers (e.g. a VMware VCenter
    server, OpenStack Controller Node).
-   pmachines: The Physical Machines (hypervisors) known to the Open
    vStorage cluster.
-   roles: All different roles/scopes available to groups, clients and
    tokens.
-   storagedrivers: All Storage Driver instances.
-   storagerouters: All Storage Routers of the Cluster.
-   tasks: The celery tasks which are active, scheduled, reserved or
    revoked. These tasks are the results of actions performed on the
    Open vStorage Cluster.
-   users: The users which are registered in the Open vStorage Cluster.
-   vdisks: Virtual disk served by Open vStorage. vDisks can be part of
    a vMachine or stand-alone.
-   vpools: The vPools which can be used to deploy vMachines.

It also provides two "special" resources:

-   messages: Access to the management system for sending messages to
    the GUI by using a long-polling mechanism.
-   statistics: Statistical information about the DAL (Data Abstraction
    Layer).

### General usage

### OPTIONS

When using CORS, each call can be preflighted by sending the OPTIONS
HTTP verb, providing a correct Access-Control-Allow-Origin header.

### GET /api/:resource/

Fetch a list of resource GUIDs or resource objects. This is available on
all resources.

-   Request headers:
    -   Accept
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS only)
-   Query parameters:
    -   query (optional - no querying if omitted)
        -   The 'query' JSON object with 'type' and 'items' ({'type' : TYPE, 'items': ITEMS})
        -   The 'type' indicates how the items supplied in 'items' should be chained
            -   Possible types are 'AND' and 'OR'   
        -   The 'filter' is a list defining a single expression:
            - (FIELD, OPERATOR, VALUE [, IGNORE CASE])
            - The field is any property you would also find on the given object. In case of properties, you can dot as far as you like.
            - The operator indicates what condition should apply
                -   Possible operators are 
                    -   'EQUALS' (=)
                    -   'NOT_EQUALS' (!=)
                    -   'LT' (<)
                    -   'GT' (>)
                    -   'IN' (check if a value is in a list of values)
                    -   'CONTAINS' (check if a string value is a substring of the given value) 
            - The value is what value the FIELD should match to (for the given operator)
            - The ignore case option is optional, this indicates if the query should operate case-sensitive for the given filter item (defaults to true)
        -   The 'items' are a list of one or more QUERY or FILTER items. This means the query structure is recursive and complex queries are possible
        -   Example simple query:
            ```
            {
                type: 'AND',
                items: [
                    ['is_vtemplate', 'EQUALS', false]
                ]
            }
            ```
            -   Query explained:
                -   Check if the 'is_vtemplate' property is false
        -   Example recursive query:
            ```
            {
                type: 'AND',
                items: [
                    ['is_vtemplate', 'EQUALS', false],
                    {
                        type: 'OR',
                        items: [
                            ['size', 'LT', 100],
                            ['size', 'EQUALS, 100],
                            {
                                type: 'AND'
                                items: [
                                    ['name', 'CONTAINS', 'openvstorage']
                                ]
                        ]
                    }
                ]
            }
            ```
            -   Query explained:
                -   Check if the 'is_vtemplate' property is false
                -   Also check if either
                    -   'size' is lesser than 100,
                    -   'size' equals 100
                    -   'name' is a substring of 'openvstorage'
    -   sort (optional - no sorting if omitted)
        -   A comma separated list of fields
        -   Prefix fields with a dash (-) for reverse sorting
        -   Dictionary fields can have a key specified (e.g.
            my_propertymy_key)
        -   Example:
            sort=field_one,-field_two,dict_fieldmy_key,other_field
    -   page (optional - all resources if omitted)
        -   An integer indicating which page to return (10 entries per
            page)
        -   Invalid pages will return the first (page < 0) or last
            (page > number of pages) page
        -   Example: page=3
    -   contents (optional - list of GUIDs if omitted)
        -   If this parameter is passed, a list of object will be
            returned instead of a list of GUIDs
        -   If this parameter is passed, all static properties will be
            returned by default
        -   A comma separated list of fields that should be returned
            (next to the static properties)
        -   Special fields can be requested
            -   _dynamics: Include all dynamic properties
            -   _relations: Include foreign keys and lists of primary
                keys of linked objects
        -   Prefix fields with a dash (-) to explicitly exclude them
            (e.g. to exclude one dynamic property when _dynamics was
            passed)
        -   Examples:
            -   contents= (only show static properties)
            -   contents=dynamic_property_1,dynamic_property_2
                (static properties plus 2 dynamic properties)
            -   contents=_dynamic,-dynamic_property_2,_relations
                (static properties, all dynamic properties except for
                dynamic_property_2 plus all relations)
    -   (see resource specific information for more parameters)
-   Response headers:
    -   Content-Type
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS preflight only)
    -   Access-Control-Allow-Credentials (CORS preflight only)
    -   Access-Control-Allow-Headers (CORS preflight only)
    -   Access-Control-Allow-Methods (CORS preflight only)
-   Response object:
    -   A list of GUIDs or a list of objects (depending on the content
        parameter)
    -   Fields containing passwords will never be sent
    -   (see resource specific information for object structure)
-   Status codes:
    -   200 (OK)
    -   401 (Unauthorized)
    -   (see resource specific status codes)

### POST /api/:resource/

Create a new object. Not available for all resources. See for possible
resource actions below.

-   Request headers:
    -   Accept
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS only)
-   Query parameters:
    -   None
-   Response headers:
    -   Content-Type
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS preflight only)
    -   Access-Control-Allow-Credentials (CORS preflight only)
    -   Access-Control-Allow-Headers (CORS preflight only)
    -   Access-Control-Allow-Methods (CORS preflight only)
-   Response object:
    -   One of two possibilities:
        -   The object that was just created
        -   A description of the errors occurred during object creation
-   Status codes:
    -   201 (Created)
    -   400 (Bad request)
    -   401 (Unauthorized)

### GET /api/:resource/:guid/

Fetch a resource. By default only static properties will be returned.

-   Request headers:
    -   Accept
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS only)
-   Query parameters:
    -   contents (optional - list of GUIDs if omitted)
        -   If this parameter is passed, a list of object will be
            returned instead of a list of GUIDs
        -   If this parameter is passed, all static properties will be
            returned by default
        -   A comma separated list of fields that should be returned
            (next to the static properties)
        -   Special fields can be requested
            -   _dynamics: Include all dynamic properties
            -   _relations: Include foreign keys and lists of primary
                keys of linked objects
        -   Prefix fields with a dash (-) to explicitly exclude them
            (e.g. to exclude one dynamic property when _dynamics was
            passed)
        -   Examples: See GET /api/:resource/
    -   (see resource specific information for more parameters)
-   Response headers:
    -   Content-Type
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS preflight only)
    -   Access-Control-Allow-Credentials (CORS preflight only)
    -   Access-Control-Allow-Headers (CORS preflight only)
    -   Access-Control-Allow-Methods (CORS preflight only)
-   Response object:
    -   Fields containing passwords will never be sent
    -   (see resource specific information for object structure)
-   Status codes:
    -   200 (OK)
    -   401 (Unauthorized)
    -   404 (Not found)
    -   (see resource specific status codes)

### DELETE /api/:resource/:guid/

Deletes a given resource

-   Request headers:
    -   Accept
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS only)
-   Query parameters:
    -   None
-   Response headers:
    -   Content-Type
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS preflight only)
    -   Access-Control-Allow-Credentials (CORS preflight only)
    -   Access-Control-Allow-Headers (CORS preflight only)
    -   Access-Control-Allow-Methods (CORS preflight only)
-   Response object:
    -   None
-   Status codes:
    -   204 (No content)
    -   401 (Unauthorized)
    -   404 (Not found)

### PATCH /api/:resource/:guid/

Updates a set of properties of a given resource.

-   Request headers:
    -   Accept
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS only)
-   Query parameters:
    -   None
-   Response headers:
    -   Content-Type
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS preflight only)
    -   Access-Control-Allow-Credentials (CORS preflight only)
    -   Access-Control-Allow-Headers (CORS preflight only)
    -   Access-Control-Allow-Methods (CORS preflight only)
-   Response object:
    -   One of two possibilities:
        -   The object that was just updated
        -   A description of the errors occurred during object update
-   Status codes:
    -   202 (Accepted)
    -   400 (Bad request)
    -   401 (Unauthorized)
    -   404 (Not found)

### POST /api/:resource/:guid/:action

Executes an action on a given resource. Not available for all resources.
See for possible resource actions below.

-   Request headers:
    -   Accept
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS only)
-   Query parameters:
    -   None
-   Response headers:
    -   Content-Type
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS preflight only)
    -   Access-Control-Allow-Credentials (CORS preflight only)
    -   Access-Control-Allow-Headers (CORS preflight only)
    -   Access-Control-Allow-Methods (CORS preflight only)
-   Response object:
    -   (see resource specific information for object structure)
-   Status codes:
    -   200 (OK)
    -   401 (Unauthorized)
    -   404 (Not found)

### GET /api/:resource/:guid/:link

Fetch specialized information related to the given object. Not available
for all resources. See for possible resource actions below.

-   Request headers:
    -   Accept
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS only)
-   Query parameters:
    -   None
-   Response headers:
    -   Content-Type
        -   application/json
        -   text/html
    -   Access-Control-Allow-Origin (CORS preflight only)
    -   Access-Control-Allow-Credentials (CORS preflight only)
    -   Access-Control-Allow-Headers (CORS preflight only)
    -   Access-Control-Allow-Methods (CORS preflight only)
-   Response object:
    -   (see resource specific information for object structure)
-   Status codes:
    -   200 (OK)
    -   401 (Unauthorized)
    -   404 (Not found)

