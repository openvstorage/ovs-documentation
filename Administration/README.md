## Administration

This section of the documentation describes how to use and manage an Open vStorage cluster. 

First of all this section describes how to create [a vDisk on a vPool](createvdisk.md) using the different interfaces supported by Open vStorage.   

An Open vStorage Cluster can be managed through different interfaces:
- [Using the GUI](usingthegui/README.md)
- [Using the API](usingtheapi/README.md)
- [Using the CLI](usingthecli/README.md)

To keep your cluster healthy, there is a [healthcheck](https://github.com/openvstorage/openvstorage-health-check) provided. Based upon this healthcheck it might be required to perform certain maintenance actions:
* [Shutdown and Restart a cluster](shutdownrestart.md)
* [Upgrade a cluster](upgrade.md)
* [Remove a node](removenode.md)

In case of (hardware) failure scenarios:
* [Replace an Open vStorage node](replacenode.md)
* [Replace a compute node](replacecpu.md)
* [Replace an SSD with the write role](replacewrite.md)

Under [services](Services/README.md) you get an overview of the different services which are running in an Open vStorage cluster.

