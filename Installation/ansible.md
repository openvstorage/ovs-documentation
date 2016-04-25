## Deploy an Open vStorage cluster with Ansible

### Introduction
To prevent errors and cut-down the deployment time, it is possible to automate the deployment of Open vStorage through [Ansible](https://www.ansible.com/). Automating Open vStorage can of course also be achieved in a similar fashion with other tools like [Puppet](https://puppetlabs.com/) or [Chef](https://www.chef.io/chef/).

For starters we have 2 types of setups: HyperConverged and HyperScale / GeoScale 

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
cd dev_ops/Ansible/hyperconverged_project/
ansible-playbook openvstorage_hyperconverged_setup.yml -u root -k -vvvv
```

The above playbook will install the necessary packages and run `ovs setup` on the controllers and compute/storage nodes.

Next steps are assigning roles to the SSDs and PCIe flash cards, create the backend and create the first vPool.

### HyperScale / GeoScale 

#### Short info

* **HyperScale:** Seperated compute and storage (on 1 location)
* **GeoScale:** Seperated compute and storage, with alba excellerated alba (over multiple datacenters)

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
cd ~/dev_ops/Ansible/hyperscale_project/
ansible-playbook openvstorage_hyperscale_setup.yml -u root -k -vvvv
```

The above playbook will install the necessary packages and run `ovs setup` on the controllers and compute nodes. It will also add the storage nodes to the Open vStorage framework database (`ovsdb`) through the `asd-manager setup`.

Next steps are assigning roles to the SSDs and PCIe flash cards, create the backend and create the first vPool.
