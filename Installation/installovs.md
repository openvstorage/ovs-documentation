
### <a name="installovs"></a>Install the Open vStorage software on all Storage Routers

There are 2 options to install Open vStorage. The first option is to
install Open vStorage and the Open vStorage Backend packages. When you
install the Open vStorage Backend packages you can use the disks inside
the host as Tier 2 storage (hyperconverged). You will need a
license to enable these packages. There is free license with
restrictions available which can be requested from the License section
under [Administration](Administration/administration.md). The second
option is to only install the Open vStorage core packages. Without the
Open vStorage Backend packages you will need to set up a separate Tier 2
Storage Backend (Swift, Ceph, GlusterFS, ...). There is no license
required when installing only the Open vStorage core packages.
**All Open vStorage nodes should have the same packages (openvstorage-hc or openvstorage) installed.**

Execute the next steps in the shell of all KVM Nodes:

#### Ubuntu
-   Add the repo to your sources
```
echo "deb http://apt.openvstorage.org chicago-community main" > /etc/apt/sources.list.d/ovsaptrepo.list
```
-  Install the Open vStorage HyperConverged or not:
  *   Install Open vStorage HyperConverged
```
apt-get update; apt-get install openvstorage-hc
```
  *   Install the Open vStorage packages *without* the Open vStorage Backend packages
```
apt-get update; apt-get install  openvstorage
```



#### CentOS

