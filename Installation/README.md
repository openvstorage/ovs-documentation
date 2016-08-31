## Install Open vStorage

### Introduction
Open vStorage is developed using a component based approach. By using this approach Open vStorage can be installed flexible and tailored to suit almost any use.
This of course means that setting up Open vStorage isn't as easy as pressing a button as setting up a cluster across multiple datacenters is complex.

Before starting the installation of your Open vStorage cluster, please go through our [Essentials](essentials.md) checklist.

For convenience reasons the Open vStorage team has created a couple of ansible scripts which [deploy Open vStorage with Ansible](https://openvstorage.gitbooks.io/devops/content/Docs/ansible.html). There are 2 fundamental ways to setup Open vStorage:
* [Hyper-converged](https://openvstorage.gitbooks.io/devops/content/Docs/hyperconverged.html): Compute and Storage are configured on the same server.
* [GeoScale](https://openvstorage.gitbooks.io/devops/content/Docs/geoscale.html): The compute, performance and capacity functionality is configured on separate servers.

For both of these setup types there is an ansible script. After running the ansible install script, you will still need to [configure the cluster](configurecluster.md) (setup roles, create backends and vPools, ...). For detailed documentation on how to use ansible scripts, please visit the [dev_ops GitBook](https://openvstorage.gitbooks.io/devops/).

**NOTE:** The ansible scripts are part of the commercial Open vStorage license. [Contact us](http://www.openvstorage.com/contact_us/) for more information.

#### Manual installation
For administrators that prefer a manual approach and want to setup the cluster manually there is a step by step manual to install [a GeoScale cluster on KVM/QEMU](geoscale.md).

#### Final Notes
* Open vStorage is licensed under the [AGPLv3 License](http://www.gnu.org/licenses/agpl.html).