## Deploy an Open vStorage cluster with Ansible

### Introduction
To prevent errors and cut-down the deployment time, it is possible to automate the deployment of [Open vStorage](https://www.openvstorage.org/) through [Ansible](https://www.ansible.com/). Automating Open vStorage can of course also be achieved in a similar fashion with other tools like [Puppet](https://puppetlabs.com/) or [Chef](https://www.chef.io/chef/).

For starters we have 2 types of setups: **HyperConverged** and **HyperScale / GeoScale**

#### Prepare your control machine

* Install Ansible on a pc or server you can use as Control Machine. The Control Machine is used to send instructions to all hosts in the Open vStorage cluster. Note that the Control Machine should not be part of the cluster so it can later also be used for troubleshooting the Open vStorage cluster.

```
sudo apt-get install software-properties-common
sudo apt-add-repository ppa:ansible/ansible
sudo apt-get update
sudo apt-get install ansible
```

* Create `/usr/lib/ansible`, download the Open vStorage module to the Control Machine and put the module in `/usr/lib/ansible`.

```
sudo apt-get install git
cd ~; sudo git clone -b fargo-release3 https://github.com/openvstorage/dev_ops.git
sudo mkdir /usr/lib/ansible; sudo cp ~/dev_ops/Ansible/openvstorage_module_project/openvstorage.py /usr/lib/ansible
```

* Edit the Ansible config file `/etc/ansible/ansible.cfg` describing the library. Uncomment it and change it to `/usr/lib/ansible`.

```
vim /etc/ansible/ansible.cfg

##change
#inventory = /etc/ansible/hosts
#library = /usr/share/my_modules/

##to
inventory = /etc/ansible/hosts
library = /usr/lib/ansible
```

### HyperConverged

#### Short info

Converged compute and storage

#### Architecture
* The Ansible script will deploy a cluster with 3 types of nodes: controllers, compute and storage nodes.
    * **Controllers:** Dedicated nodes to run the master services and hold the distributed DBs. Can be configured with vPools and run the VMs
    * **Compute / Storage nodes:** These nodes run the extra services, are configured with vPools and run the VMs.

#### Deploy the cluster

* Install `Ubuntu 14.04` on all servers of the cluster. Username (default usage is `root`) and password should be the same on all servers.

* Edit the Ansible inventory file (`/etc/ansible/hosts`) and add the controller, compute and storage nodes to describe the cluster according to the below example:

```
#
# This is the default ansible 'hosts' file.
#

#cluster overview

[controllers]
ctl01 ansible_host=10.100.198.1 hypervisor_name=mas01 api_port=8500 excellerated_backend=false
ctl02 ansible_host=10.100.198.2 hypervisor_name=mas02 api_port=8500 excellerated_backend=false
ctl03 ansible_host=10.100.198.3 hypervisor_name=mas03 api_port=8500 excellerated_backend=false

[computenodes]
cmp01 ansible_host=10.100.198.4 hypervisor_name=hyp01 api_port=8500 excellerated_backend=false

#cluster details

[cluster:children]
controllers
computenodes

[cluster:vars]
cluster_password=rooter
cluster_type=KVM
install_master_ip=10.100.198.1
```

* Execute the Open vStorage HyperConverged playbook. *(It is advised to execute the playbook in debug mode* `-vvvv`*)*

```
cd ~/dev_ops/Ansible/hyperconverged_project/
ansible-playbook openvstorage_hyperconverged_setup.yml -u root -k -vvvv
```

The above playbook will install the necessary packages and run `ovs setup` on the controllers and compute/storage nodes.

Next steps are assigning roles to the SSDs and PCIe flash cards, create the backend and create the first vPool.

#### How fast is the playbook? (3 controller nodes & 1 compute node on 1Gbit network)

```
Friday 22 April 2016  17:35:26 +0200 (0:00:12.733)       0:11:35.012 ********** 
=============================================================================== 
TASK: install controllers packages ------------------------------------ 176.44s
TASK: install compute packages ---------------------------------------- 168.71s
TASK: installing the open vstorage controllers ------------------------ 120.92s
TASK: installing the open vstorage compute nodes ----------------------- 43.94s
TASK: install required packages ---------------------------------------- 19.26s
TASK: finalizing setup ------------------------------------------------- 12.73s
TASK: check hosts their availability ----------------------------------- 12.57s
TASK: add performance settings to cluster ------------------------------- 0.27s
TASK: add openvstorage apt-repo ----------------------------------------- 0.20s

real    11m37.835s
user    0m55.560s
sys     0m29.860s
```

### HyperScale / GeoScale 

#### Short info

* **HyperScale:** Seperated compute and storage (on 1 location)
* **GeoScale:** Seperated compute and storage, with alba excellerated alba (over multiple datacenters with layer 3 connection available)

#### Architecture
* The Ansible script will deploy a cluster with 3 types of nodes: controllers, compute and storage nodes.
    * **Controllers:** Dedicated nodes to run the master services and hold the distributed DBs.
    * **Compute nodes:** These nodes run the extra services, are configured with vPools and run the VMs.
    * **Storage nodes:** The storage servers for the backend storage.

#### Deploy the cluster

* Install `Ubuntu 14.04` on all servers of the cluster. Username (default usage is `root`) and password should be the same on all servers.

* Edit the Ansible inventory file (`/etc/ansible/hosts`) and add the controller, compute and storage nodes to describe the cluster according to the below example:

```
#
# This is the default ansible 'hosts' file.
#

#cluster overview

[controllers]
ctl01 ansible_host=10.100.198.1 hypervisor_name=mas01
ctl02 ansible_host=10.100.198.2 hypervisor_name=mas02
ctl03 ansible_host=10.100.198.3 hypervisor_name=mas03

[computenodes]
cmp01 ansible_host=10.100.198.4 hypervisor_name=hyp01

[storagenodes]
str01 ansible_host=10.100.198.11 api_port=8500 excellerated_backend=false
str02 ansible_host=10.100.198.12 api_port=8500 excellerated_backend=false
str03 ansible_host=10.100.198.13 api_port=8500 excellerated_backend=true

#cluster details

[cluster:children]
controllers
computenodes
storagenodes

[cluster:vars]
cluster_password=rooter
cluster_type=KVM
install_master_ip=10.100.198.1
```

* Execute the Open vStorage GeoScale / HyperScale playbook. *(It is advised to execute the playbook in debug mode* `-vvvv`*)*

```
cd ~/dev_ops/Ansible/hyperscale_geoscale_project/
ansible-playbook openvstorage_hyperscale_setup.yml -u root -k -vvvv
```

The above playbook will install the necessary packages and run `ovs setup` on the controllers and compute nodes. It will also add the storage nodes to the Open vStorage framework database (`ovsdb`) through the `asd-manager setup`.

Next steps are assigning roles to the SSDs and PCIe flash cards, create the backend and create the first vPool.

#### How fast is the playbook? (3 controller nodes, 1 compute node & 3 storage nodes on 1Gbit network)

```
Friday 22 April 2016  17:12:52 +0200 (0:00:00.530)       0:13:48.209 ********** 
=============================================================================== 
TASK: install controllers packages ------------------------------------ 170.95s
TASK: install compute packages ---------------------------------------- 169.11s
TASK: installing the open vstorage controllers ------------------------ 139.04s
TASK: install required packages ---------------------------------------- 76.79s
TASK: installing the open vstorage compute nodes ----------------------- 42.06s
TASK: install storage packages ----------------------------------------- 36.17s
TASK: fetch all etcd endpoints from controllers ------------------------ 14.07s
TASK: finalizing setup ------------------------------------------------- 12.81s
TASK: check hosts their availability ----------------------------------- 12.58s
TASK: create etcd proxy on storage nodes -------------------------------- 2.04s
TASK: installing the open vstorage storage nodes ------------------------ 0.53s
TASK: add openvstorage apt-repo ----------------------------------------- 0.40s
TASK: add performance settings to cluster ------------------------------- 0.39s
TASK: start etcd proxy on storage nodes --------------------------------- 0.26s
TASK: create required directories for alba on storage nodes ------------- 0.19s
TASK: parse etcd endpoints to a list ------------------------------------ 0.03s

real    13m51.644s
user    1m8.280s
sys     0m36.608s
```

### If something goes wrong...

* **Open vSwiper:**
  If something goes wrong you can run the dev_ops Open vStorage wiper to redo the installation. 
  Just go to: `dev_ops/Bash/` and run on the nodes: `bash OpenvSwiper.sh`

  **WARNING:** This script wipes all the disks / partitions that are not in use by the operating system. Also it wipes the content of `/root/.ssh/`.
  
  If you need a one liner to execute the wiper from your Ansible configuration master:

```
ansible cluster -m shell -a "wget https://raw.githubusercontent.com/openvstorage/dev_ops/fargo-release3/Bash/OpenvSwiper.sh -O /root/wiper.sh; bash /root/wiper.sh; bash /root/wiper.sh" -u root -k`
```

* **Ansible** `host_key_checking`: 
  If you get host key checking errors when you start the script, change this in `/etc/ansible/ansible.cfg`:

```
# uncomment this to disable SSH key host checking
host_key_checking = False
```
  
### Performance issue's
If the installation is on a large scale, you will probably experience a slow installation.
This is because Ansible only perform 5 parallel connections by default.
If you want to install packages faster, you can uncomment & edit the `forks` parameter in `/etc/ansible/ansible.cfg`:

* By default: 

```
#forks          = 5
```

* Change to: 

```
forks          = 200
```

If you want to know how long the installation last, you can enable the Ansible `profile_tasks` in `/etc/ansible/ansible.cfg`:

``` 
callback_whitelist = profile_tasks 
```

### For more information:

#### Full overview
For a full overview of the seperate repo to install Open vStorage through Ansible, click [here](https://github.com/openvstorage/dev_ops/tree/fargo-release3).

#### Older versions:
* For a full overview of the seperate releases, click [here](https://github.com/openvstorage/dev_ops/releases).
* To install a `eugene-updates` just follow the instrunctions on the `release02` branch, click [here](https://github.com/openvstorage/dev_ops/tree/release2.0).
