## Deploy an Open vStorage cluster with Ansible

### Introduction
To prevent errors and cut-down the deployment time, it is possible to automate the deployment of [Open vStorage](https://www.openvstorage.org/) through [Ansible](https://www.ansible.com/). Automating Open vStorage can of course also be achieved in a similar fashion with other tools like [Puppet](https://puppetlabs.com/) or [Chef](https://www.chef.io/chef/).

There are 3 different deployment scenario's:
* [Hyper-converged](https://openvstorage.gitbooks.io/devops/content/Docs/hyperconverged.html): Compute and Storage are configured on the same server.
* [HyperScale](https://openvstorage.gitbooks.io/devops/content/Docs/hyperscale.html): The compute and performance functionality run on the same server. Capacity nodes are on separate servers.
* [GeoScale](https://openvstorage.gitbooks.io/devops/content/Docs/geoscale.html): The compute, performance and capacity functionality is configured on separate servers. The capacity nodes can be spread across multiple locations.
