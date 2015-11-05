### Administration

An Open vStorge Cluster can be managed through a single web based
interface. The below section will explain how to access and manage an
Open vStorage Cluster through this GUI.

### Log In

The GUI to manage an Open vStorage can be found by navigating with an
HTML5 compatible browsers to the IP address of any of the Grid Storage
Routers (GSRs) in your cluster. In case you use HTTP instead of HTTPS,
the URL will be rewritten to use HTTPS. HTTPS is used to make sure all
the communication between your pc and the Open vStorage Cluster is
secure. When opening the GUI for the first time, you will be presented
with a message about the security certificate. Proceed anyway.

-   You will see the following login screen:

![](../Images/login.png)


-   Enter your credentials. The default administrator credentials are
    username:**admin**, password:**admin**.
-   Click the **Sign In** button.

When the login was successful, you will be transferred to the Open
vStorage Dashboard.


### The Open vStorage Dashboard

The Open vStorage Dashboard is the default starting point when working
with an Open vStorage Cluster. The Dashboard is a view on the health,
performance and status of the Cluster. unsupported

![](../Images/dashboard_small.png)


Following panels, from left to right and top to bottom, can be found on
the Dashboard:

-   Storage Routers: The Storage Routers panel list all the Storage Routers
    registered in the Open vStorage Cluster. For each Storage Router the status,
    name, IP address and amount of vDisks is displayed.
-   Open vStorage Backends (Optional): The Open vStorage Backend panel displays all registered Open vStorage Backends and their status. This panel is only displayed in case the hyper-converged version is installed.
-   Cache Hit Ratio: The Cache Hit Ratio panel contains the percentage
    of the cache hits to the total amount of cache lookups for all
    vDisks. Below the current amount of IOPS is displayed for the whole
    Cluster. The panel gets updated every 5 seconds.
-   vMachines: The vMachine panels lists the top vMachines according to
    IOPS and Stored Data. You can use the drop-down list to select the
    property to filter on. Click the number next to the vMachines title
    to go directly to the list of vMachines.
-   vDisk Statistics: The vDisk Statistics panels lists the average on
    IOPS, Read and Write Speed and Cache Hits over 5 seconds summarized
    for all vDisks in the Custer. The values are updated automatically
    every 5 seconds.
-   vPools: The vPools panels lists the top vPools according to Stored
    Data and Bandwidth. Use the drop-down list to select the property to
    filter on.

At the bottom of the page a panel is displayed with the amount of cache
hits, the total amount of data (including meta-data) written to the
Storage Backend, the data read from the Storage Backend and the bandwith
saved by Cache Hits. A read is served from the cache when it is found in
the read or write cache and no request is needed to fetch the data from
the Backend. The total amount of data written to the Storage Backend is
larger than the amount of data currently stored on the Backend as data
gets scrubbed when it falls outside the retention window. Data gets read
from the Backend when a cache miss occurs and the data needs to be
fetched from the Backend. The amount of data is the actual data plus the
overhead of the meta-data that needs to be retrieved. Due to its
aggressive caching mechanism, Open vStorage allows to minimize the load
on the Storage Backend. The total amount of bandwidth saved by the
caching is expressed by the Bandwidth Saved statistic.

### The Menu

The top navigation lists following sections:

-   [Storage Routers](storagerouters.md): the Storage Routers running the Open
    vStorage software.
-   [vPools](vpools.md): the available Virtual Storage Pools which can be
    used to deploy vMachines.
-   [vMachines](vmachines.md): the Virtual Machines with vDisks served by
    Open vStorage.
-   [vDisks](vdisks/md): the virtual disks served by Open vStorage.
-   [vTemplates](vtemplates.md): the blueprints for a new vMachines.
-   [Backends](backends.md): the Open vStorage Backends (only applicable
    when running hyperconverged).
-   [Administration](administration.md)

