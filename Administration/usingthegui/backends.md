### Backends

#### Introduction

The Open vStorage Backends page allows to manage physical devices (SATA, SSD, NVMe) as a storage backend for Open vStorage. 


#### Backends Overview

The Backend Overview page displays the different Open vStorage Backends
to which the logged in user has access.

For each Backend following details are available:

-   Name: friendly name of the Backend.
-   Type: Type of the Backend. Currently the only supported type as Backend is Open
    vStorage Backend.
-   Domain: The domain to which the backend belongs (f.e. datacenter in case of local backend).
-   Status: Status of the Backend. This is indicated by a color.
    -   Green: Everything is ok.
    -   Orange: Issues have been reported which require your attention.
    -   Red: Possible data loss.

#### Backend Actions

##### Add a Backend

-   To add a new Backend to the Open vStorage Cluster, click the **+ Add
    Backend** button.
-   Give the Backend a name. Only the letters a-z, numbers and dashes
    (not as first or last character) are allowed. A minimum of 3 and a
    maximum of 50 characters has to be maintained.
-   Select whether it a Local or Global backend. A local backend needs to be selected when the disks belong to the same datacenter. A Global backend allows to combine multiple local backends into a single backend which is spread across multiple datacenters.
-   Click *Finish* to create the Backend.

![](../../Images/addnewbackend.png)

After creating the Backend, assign some disks or local backends (in case of a global backend) to the Backend to store
the actual vDisk data.

> #### Note::Default preset
>
> Don't forget to [add a custom preset](#Presets) to the backend. The default preset might not be suited for your cluster and might cause dataloss.

#### Backend Details

The Backend Details page displays the details of a single backend and
allows to add or remove SATA disks in case of a local backend or local backends in case of a global backend.

Following details are displayed for a Backend:

-   Type: Type of the Backend. Currently the only supported type is Open
    vStorage Backend.
-   Scaling: Local or Global. A local backend groups the drives within a datacenter. A Global backend allows to combine multiple local backends into a single backend which is spread across multiple datacenters.
-   Status: Status of the Backend. This is indicated by a color.
    -   Green: Everything is ok.
    -   Orange: Issues have been reported which require your attention.
    -   Red: Possible dataloss.
-   Health: The amount of disks/backends for each state assigned to the Backend.
    -   Green: The disk/backend is in use by the Backend.
    -   Orange: The disk/backend is in warning state.
    -   Red: The disk/backend is in error.
-   Domain: The domain to which the backend belongs (f.e. datacenter in case of local backend).
-   Puts/s: Average amount of puts/s to the Backend over the last 5
    seconds.
-   Gets/s: Average amount of gets/s from the Backend over the last 5
    seconds.

On the right side you can find a pie-chart which displays used and free space of the backend (if available).

#### Backend Actions

##### Edit Backend
Edit the domain to which the backend belongs.

##### Delete Backend
Delete a backend in case it is no longer in use. Deleting a backend when there are still ASDs attached is not allowed. Remove all ASDs by selecting each ASD and clicking the **Remove** button on the ASD detail panel.

#### ASD nodes

Under ASD nodes a list of nodes with disks (ASDs)that can be added to
the Backend are shown. By default all nodes with a Storage Router are
shown. Additional nodes can be added by clicking the **Discover** button.

Each disk which can be used as disk for a Backend will be shown. The OS
disk and SSDs will not be shown on the Backend Details page as these can
not be selected to be included in the Backend. Each disks will be
highlighted with a specific color explaining its status:

-   Unavailable (Grey): The disk is already claimed by another Backend.
-   Uninitialized (White): The disk can be claimed to be used in the
    Backend but must first be initialized.
-   Initialized (Light blue): The disk is initialized and will broadcast
    it is ready to be used.
-   Available (Dark blue): The disk is detected by the the Open vStorage
    software as initialized and can now be claimed as Backend disk.
-   Good (Green): The disk is in use by the Backend.
-   Warning (Orange): Disk is in warning state. An action might be
    required.
-   Faulted (Red): Disk is broken and should be replaced. The disks
    needs to be replaced. See the [Replace a broken disk
    section](#replace-a-broken-disk)

##### Node Actions

There are 2 actions possible available on a node level

-   Initialize all disks: This action will initialize all disks of the
    node which can be used as Backend disks.
-   Claim all disks: This action will claim all disks which are
    available and will assign them to the Backend.

##### ASD Details and Actions

Each disk can hold many ASDs (ALBA Storage Daemons) which can be managed individually. Select the dropdown box to
see the ASD details and possible actions. 

![](../../Images/asd_details.png)

##### Replace a broken disk

When a disk is having issues it will be highlighted in orange. You can
try to fix the issue by pressing the **Restart** button in the ASD details
section of that disk. In case the disk is broken and labeled as Faulted,
you can remove the disk from the Backend by clicking the **Remove** button
in the ASD details section. 

In case a disk broken, the maintenance will kick in after 15 minutes and start regenerating the data on the broken disk on the remaining disks. This 15 minutes time-out can be configured in the [ALBA config](https://openvstorage.gitbooks.io/alba).
Once the broken disk is removed you can safely add a new disk. The new disk will be labeled as
Unitialized (White). The next step is to initialize the disk and once it becomes available claim the disk.

#### Presets
A backend has a list of presets assigned to it. A preset consists out of a set of policies and a compression method (optional). A policy defines how the VM data is stored across the ASDs. The easiest way is replication. In that case you only need to set the replication factor. A more advanced policy allows to define the amount of fragments an object is split in, the amount of parity fragments, the minimal amounts of fragments before a write is executed and the maximal amount of fragments per ASD node. Active policies, meaning that objects are using this policy will have a green font color. Available policies which can be used but currently have no object being stored with this policy will be shown in black. In case a policy can not be met, e.g. because there are not enough ASDs or nodes, the policy will be disabled (grey). In case no policy can be met, the backend will be read-only.

##### Add a new Preset
To add a new preset for a backend, click the **+ Add Preset** button. Give the new preset a name. By default you can select the replication factor of fragments stored on the backend. Selecting 1 means that a single broken disk will lead to data loss.

In case you want to use erasure coding instead of replication, select the advanced settings. Select the checkbox to indicate you understand the risk of adding new policies, select whether you want to encrypt the backend and select the compression algorithm to use. You will need to add at least one policy. You can also sort the policies according to which policy is more preferred. In case the policy can be met, the top policy will be selected. In case the policy can not be met, the second one from the list gets selected and so forth.

> #### Note::Presetname
>
> Supported characters for the presetname are a-zA-Z0-9, - and _. Special characters - and _ can't be the first character of the presetname.

![](../../Images/addpreset.png)

##### Edit a Preset
Once a preset is created, it is possible to update the policies of the preset. It is not possible to update the name and encryption and compression settings.


##### k,m,c,x explained
A policy consist out of 4 numbers labaled as k,m,c,x:
* k: Each Storage Container Object (SCO) gets  split into k fragments, the data fragments. 
* m: Next to the data framents (k), m additional paraity fragments will be created. Upto m fragments can be lost before any dataloss occurs.
* c: The amount of fragments that must be acknowledged by the backends before the SCO is marked as safely stored on the backend. For datasafety reasons k <= c <= (k + m). 
* x: The maximal amount of fragments per ASD node. This setting avoids hotspots where all fragments end up on the same ASD node.

An example might explain everything a bit more. E.g. in case the SCO size is 16MB and (k,m,c,x ) is set to (16,4,18,4). This means 16 1MB fragments will be created as data fragments.
Additionally 4 parity fragments will be created. If 18 out of the 20 (16+4) fragments are stored on the backend, the SCO is marked as safely stored on the backend.
In order to use this policy, the cluster will need at least 5 nodes  (20/4) as the maximal amount of fragments on a node is 4. In case a nodes fails you lose in a worst case scenario 4 fragments. 
As the amount of parity fragments (m) is set to 4 this means there will be no dataloss. Another disk failure on top of the node failure might cause dataloss in this case.  


#### Access Rights
It is possible to limit the access to a Backend per user or OAuth2 client. By default users which are part of the Administrator have access to all backend but access can be explicitly denied. All other users need to be explicitly granted access to the Backend by clicking the **Edit** button.
