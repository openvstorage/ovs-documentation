## Deploy an Open vStorage cluster with Ansible

### Introduction
To prevent errors and cut-down the deployment time, it is possible to automate the deployment of [Open vStorage](https://www.openvstorage.org/) through [Ansible](https://www.ansible.com/). Automating Open vStorage can of course also be achieved in a similar fashion with other tools like [Puppet](https://puppetlabs.com/) or [Chef](https://www.chef.io/chef/).

**Note that the Ansible scripts are part of the Open vStorage Enterprise Edition.** 

There are 2 fundamentally different deployment scenario's:
* [Hyper-converged](https://openvstorage.gitbooks.io/devops/content/Docs/hyperconverged.html): Compute and Storage are configured on the same server.
* [GeoScale](https://openvstorage.gitbooks.io/devops/content/Docs/geoscale.html): The compute, performance and capacity functionality is configured on separate servers. The capacity nodes can be spread across multiple locations.

After running the ansible install script, you will still need to [configure the cluster](configurecluster.md) (setup roles, create backends and vPools, ...). For detailed documentation on how to use ansible scripts, please visit the [dev_ops GitBook](https://openvstorage.gitbooks.io/devops/).