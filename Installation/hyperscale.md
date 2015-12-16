## HyperScale

### Introduction
In large setups you typically don't want to run Open vStorage hyperconverged (compute + storage inside the same server). Open vStorage is flexible enough to be installed as a converged setup. This mode is called HyperScale as it allows to scale compute and storage independent.

### Compute nodes
* Install Open vStorage (apt-get install openvstorage-hc) as explained in the [KVM](kvm.md),[OpenStack](openstack.md) or [ESXi](esxi.md) section.

### storage server:
* The storage server must have an OS disk and at least 3 empty disks for the storage backend.
* Setup the storage server with Ubuntu LTS 14.04.2. Make sure the storage server is in the same network as the compute host.
*Execute following to setup the program that will manage the disks of the storage server:
```
echo "deb http://apt.openvstorage.org <version as installed on the compute hosts> main" > /etc/apt/sources.list.d/ovsaptrepo.list
apt-get update
apt-get install openvstorage-sdm
```
Retrieve the automatically generated password from the config:
```
cat /opt/alba-asdmanager/config/config.json
...
"password": "u9Q4pQ76e0VJVgxm6hasdfasdfdd",
...
```

### Add the ASD nodes to a backend
* Login into the Open vStorage GUI and go to Backends.
* Click the `add Backend` button, specify a name and select Open vStorage Backend as type. Click `Finish` and wait until the status becomes green.
* In the Backend details page of the freshly created Backend, click the `Discover` button. Wait until the storage server pops up and click on the + icon to add it. When asked for credentials use root as login (root) and the password retrieved above.
* Next follow the standard procedure to claim the disks of the storage server and add them to the Backend.