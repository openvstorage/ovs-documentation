### Configure the Storage Router disks
-   Open the [Open vStorage GUI](Administration/usingthegui/) on the public IP of
    the Storage Router and enter with the default login and password:
    admin/admin.
-   Select from the menu **Storage Routers** and select the newly installed Storage Router from the list.
-   Select the Physical Disk Management tab. On this tab you can assign roles to the different detected physical disks. To assign a role to a disk click the gear icon and select the role from the dropdown.
-   Assign a DB role to one of the SSDs. This will reserve 10% of the SSD for the distributed database. Each Storage Router should have one disk with a DB role. Note that this role can't be removed once set.
-   Assign a scrub role to one of the disks. The scrubber is the application which does the garbage collection of snapshot data which is out of the retention. This will reserve 300 GB of space. All storage Routers must have at least one disk with the scrubbing role. Note that this role can't be removed once set.
-   Assign the read or write role to the SSDs or PCIe flash cards you want to use as caching devices for the vPools. A Storage Router must have at least one disk with a read role assigned and one with the write role assigned. A disk can have both the read and write role assigned at the same time. The read and write role can only be removed in case no vPool is using them.
