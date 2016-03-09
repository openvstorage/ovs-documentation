## Ansible

### Introduction
To prevent errors and cut-down the deployment time, it is possible to automate the deployment of Open vStorage through [Ansible](https://www.ansible.com/). Automating Open vStorage can of course also be achieved in a similar fashion with other tools like [Puppet](https://puppetlabs.com/) or [Chef](https://www.chef.io/chef/).

### Architecture
* The Ansible script will deploy a cluster with 3 types of nodes: controllers, compute and storage nodes.
    * Controllers:  dedicated nodes to run the master services and hold the distributed DBs.
    * Compute nodes: These nodes run the extra services, are configured with vPools and run the VMs.
    * Storage nodes: The storage servers for the Backend storage.

### Deploy the cluster
* Install Ubuntu 14.04 on all servers of the cluster. Username and password should be the same on all servers.
* Install Ansible on a pc or server you can use as Control Machine. The Control Machine is used to send instructions to all hosts in the Open vStorage cluster. Note that the Control Machine should not be part of the cluster so it can later also be used for troubleshooting the Open vStorage cluster.
```
sudo apt-get install software-properties-common
sudo apt-add-repository ppa:ansible/ansible
sudo apt-get update
sudo apt-get install ansible
```
* Create `/usr/lib/ansible`, download the Open vStorage module to the Control Machine and put the module in `/usr/lib/ansible`.
```
mkdir /opt/openvstorage/
cd /opt/openvstorage/
git clone -b release1.0 https://github.com/openvstorage/dev_ops.git
mkdir /usr/lib/ansible
cp dev_ops/Ansible/openvstorage_module_project/openvstorage.py /usr/lib/ansible
```
* Edit the Ansible config file (`/etc/ansible/ansible.cfg`) describing the library. Uncomment it and change it to `/usr/lib/ansible`.
```
vim /etc/ansible/ansible.cfg

##change
#inventory = /etc/ansible/hosts
#library = /usr/share/my_modules/

##to
inventory = /etc/ansible/hosts
library = /usr/lib/ansible
```
* Edit the Ansible inventory file (`/etc/ansible/hosts`) and add the controller, compute and storage nodes to describe the cluster according to the below example:
```
#
# This is the default ansible 'hosts' file.
#

#cluster overview

[controllers]
ctl01 ansible_host=10.100.69.171 hypervisor_name=mas01
ctl02 ansible_host=10.100.69.172 hypervisor_name=mas02
ctl03 ansible_host=10.100.69.173 hypervisor_name=mas03

[computenodes]
cmp01 ansible_host=10.100.69.181 hypervisor_name=hyp01

[storagenodes]
str01 ansible_host=10.100.69.191

#cluster details
[cluster:children]
controllers
computenodes
storagenodes

[cluster:vars]
cluster_name=cluster100
cluster_user=root
cluster_password=rooter
cluster_type=KVM
install_master_ip=10.100.69.171
``
Execute the Open vStorage HyperScale playbook. (It is advised to execute the playbook in debug mode -vvvv)
```
cd /opt/openvstorage/dev_ops/Ansible/hyperscale_project/
ansible-playbook openvstorage_hyperscale_setup.yml -k -vvvv
```
The above playbook will install the necessary packages and run `ovs setup` on the controllers, compute and storage nodes. Next steps are assigning roles to the SSDs and PCIe flash cards, create the backend and create the first vPool.



