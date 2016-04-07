## Install Open vStorage

### Introduction
Currently Open vStorage supports VMware and KVM as hypervisors. A complete and detailed installation guide can be found below for each supported hypervisor.
For OpenStack specif install instructions are added to correctly integrated Open vStorage through the [Open vStorage Cinder Plugin](https://github.com/openvstorage/framework-cinder-plugin).

Open vStorage is licensed under the [Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0).

### Installation Guides
* [Essentials](essentials.md): don't start an installation without going through this page.
* [Quick installation guide](quickinstall.md): short guide on how to setup Open vStorage on KVM or ESXi.
* [Deploy Open vStorage with Ansible](ansible.md)
* Manual installation:
    * Install Open vStorage HyperConverged (compute and storage on the same node).
        * [ESXi Installation](esxi.md)
        * [KVM Installation](kvm.md)
        * [OpenStack Installation](openstack.md)
    * [HyperScale](hyperscale.md): install Open vStorage as converged solution.
    * [GeoScale](geoscale.md): stretch an Open vStorage cluster across datacenters.

