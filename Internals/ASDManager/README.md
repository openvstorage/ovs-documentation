#### ASD Manager
The ASD Manager is the software that turns standard SATA drives into ASDs you can use with ALBA.

##### Turn a JBOD into an ASD node
On the compute nodes, make sure the Open vStorage Backend packages are installed (`apt-get install openvstorage-hc`).

###### On the storage server:
* Make sure that the storage server has an OS disk and at least 3 additional empty SATA disks.
* Install the storage server with Ubuntu LTS 14.04.2. The storage server should be added in the same network as the compute hosts.
* Execute following to setup the program that will manage the disks of the storage server:
```
echo "deb http://apt.openvstorage.org eugene main" > /etc/apt/sources.list.d/ovsaptrepo.list

apt-get update
apt-get install openvstorage-sdm
```
* Retrieve the automatically generated password from the config
```
cat /opt/alba-asdmanager/config/config.json | grep password
        "password": "u9Q4pQ76e0VJVgxm6hasdfasdfdd",
```

###### On the compute host:

* Login and go to Backends.
* Click the add Backend button, specify a name and select Open vStorage Backend as type. Click Finish and wait until the status becomes green.
* In the Backend details page of the freshly created Backend, click the Discover button. Wait until the storage server pops up and click on the + icon to add it. When asked for credentials use root as login and the password retrieved above.
* Next follow the standard procedure to claim the disks of the storage server and add them to the Backend.


#### Basic ASD Manager commands


##### Start the ASD manager
To start an ASD manager execute
```
start alba-asdmanager
```

##### Retart the ASD manager
To restart an ASD manager execute
```
restart alba-asdmanager
```

##### Stop the ASD manager
To Stop an ASD manager execute
```
stop alba-asdmanager
```

##### Display the ASD manager status
To display the status of an ASD manager execute
```
status alba-asdmanager
alba-asdmanager start/running, process 18652
```
