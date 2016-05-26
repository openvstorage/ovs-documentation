#### Administration

The Administration section acts as the central location to manage and
configure your Open vStorage Cluster.

#### Administrative Actions

Following actions are available for logged in users of the GUI:

#### <a name="hmc"></a>Hypervisor Management

The Hypervisor Management allows to register Hypervirsor Management
Centers (HMC) such as VMware vCenter or an OpenStack Controller Node. To
add a HMC, click the **Add new Center** button and provide a friendly
name, type, administrator username, password and IP address.

Once a HMC is added following details will be displayed:

-   Name: friendly name used in the GUI.
-   IP: IP address the HMC is listening on. For vCenter this is the IP of the vCenter server. For OpenStack this is the Virtual IP of the Controller nodes.
-   Port: Port the HMC is listening on.
-   Type: Type of the Hypervisor Management Center. Currently only OpenStack and vCenter are supported.
-   Username: Administrator username of the HMC.
-   OVS Hosts/Total: The amount of Hosts known by the Open vStorage
    framework versus the total amount of Hosts registered in the HMC.
-   Actions: Delete the HMC.

Below the Hosts known by the Open vStorage framework are listed. For
each Host following info is displayed:

-   Name: Name of the Host.
-   IP: IP address of the Host.
-   Type: Hypervisor of the Host.
-   Mgtm. Center: The Hypervisor Management Center managing the Host.

In case a Hosts is highlighted in red, it means the Host is known by the
Open vStorage framework but not linked to the HMC managing this Host.
Click the Mgmt. Center value to edit and select the correct HMC from the
list. **Hosts highlighted in red are not covered for automated
fail-over.**

#### <a name="domains"></a>Domains
The Domains functionality allows to group Storage Routers together and allows to configure the DTL and MDS (MetaDataServer) based upon this grouping. This allows to make sure that when there is a site disaster, the data in the DTL is safeguarded to prevent dataloss.

To add a new domain or assign a Backend to a domain, see the [Backend overview page](backends.md).
To add a new domain or assign a Storage Router to a domain, see the [Storage Router overview page](storagerouters.md).

#### User Management

Under User Management you can add new users or delete existing users.
There are 2 groups of users: Viewers and Administrators. Viewers can
only see the dashboard and the detail pages but can not launch any
actions. Administrators can view all pages and launch actions. To edit a
password of a user, select the key-icon next to the username.

Under the OAuth 2 Clients section, you can see an overview of all
configured OAuth clients for the selected user. By default no OAuth2
client is generated. To add an OAuth2 client for a user, simply add a
name and select all appropriate roles.

All user actions and all actions executed by the Open vStorage core are
logged for 30 days. To list all logged actions open an interactive
ipython session from the command line of a Storage Router by typing:

```
ovs
```

In the ipython session execute

```
from ovs.dal.lists.loglist import LogList
for log in LogList.get_logs():
    print log
```

The result for a single entry will look like:

```
{
    "storagedriver_guid": "dcc17681-a157-454e-be7b-a229afddafae",
    "method_args": [],
    "method_kwargs": {
        "storagedriver_id": "local0050569647a1",
        "name": "/test/test.vmx"
    },
    "module": "ovs.lib.vmachine",
    "source": "VOLUMEDRIVER_TASK",
    "time": 1411726920.744713,
    "user_guid": null,
    "guid": "725699e3-e2af-448f-b165-8eecc1e5a491",
    "method": "delete_from_voldrv",
    "metadata": null
}
```

#### User Mgmt.

Under User Mgmt. you get can overview of all the registered users and
OAuth 2 Clients that have access to the Open vStorage environment.

Under Users you get an overview of all the users. To add a new user, add
a name in the textbox, select the group and press the check mark to
confirm. There are 2 groups: Administrators and Viewers. Viewers can not
make any changes and have no access to the Administration section.
Administrators can make changes to the Open vStorage environment (f.e.
create new users and vPools) and have access to the Administration
section.

Under OAuth 2 Clients you get an overview of all the registered OAuth 2
Clients. To add a new client, add a name in the textbox, select the role
and press the check mark. The Client ID and secret token will
automatically be generated.

#### Statistics

Under Statistics you get an overview of the load on the framework. Under
Open vStorage DAL, you get an overview of the performance of the Data
Abstraction Layer: the amount of objects being loaded from the database
and values retrieved from teh cache. The Memcache section displays the
current Memcache, the caching component of the API, activity, its size
in memory and uptime.

#### Support

Under Support you can manage the support options of your Open vStorage
environment and download the log files. When asked by the Open vStorage
Support Team , click the **download logfiles** link and mail the logfiles
of the Open vStorage nodes to the support team. Under the Call Home
section you can enable or disable the heartbeats and whether or not
Remote Access is enabled. Enabling Remote Access will set up a VPN
tunnel between the central datacenter and the individual Open vStorage
environment. This will allow the Open vStorage Support team to remotely
log in and troubleshoot the issue. The content of the heartbeats can be
found under Heartbeat data.

Add the bottom you can find an overview of the installed Open vStorage packages installed
on every Node and the important Open vStorage
related websites such as the [Project
Website](http://www.openvstorage.com) and the [Source
Code](https://github.com/openvstorage).

#### Updates
Under updates you can see if there are updates available for the different components of Open vStorage. Click the button to update the different components.

#### API

Links to the API of Open vStorage. You can find more info
[here](Administration/usingtheapi/README.md) on using the Open vStorage API.

#### Log out

Safely ends the session and logs the current user out of the Open
vStorage GUI.



