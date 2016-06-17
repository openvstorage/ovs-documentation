### <a name="installovs"></a>Install the Open vStorage software on all Storage Routers

There are 2 options to install Open vStorage. The first option is to
install Open vStorage and the Open vStorage Backend packages. When you
install the Open vStorage Backend packages you can use the disks inside
the host as Tier 2 storage (hyperconverged). The second
option is to only install the Open vStorage core packages. Without the
Open vStorage Backend packages you will need to set up a separate Tier 2
Storage Backend (Swift, Ceph, GlusterFS, ...).


**All Open vStorage nodes should have the same packages (openvstorage-hc or openvstorage) installed.**

Execute the next steps in the shell of all Compute nodes:

#### Ubuntu
-   Add the repo to your sources. For older versions please check [here](../olderreleases.md).
    - Latest  Fargo version
```
echo "deb http://apt.openvstorage.org fargo main" > /etc/apt/sources.list.d/ovsaptrepo.list
apt-get update
apt-get install openvstorage-hc
```
    - Latest experimental version
```
echo "deb http://apt.openvstorage.org unstable main" > /etc/apt/sources.list.d/ovsaptrepo.list
apt-get update
```

-  Install the Open vStorage VolumeDriver with or without deduplication:
  *   Install the VolumeDriver without deduplication (better performance, smaller metadata footprint).
```
apt-get install volumedriver-no-dedup-server
```
  *   Install the VolumeDriver with deduplication (higher metadatafootprint).
```
apt-get install volumedriver-server
```

-  Install the Open vStorage HyperConverged or not:
  *   Install Open vStorage HyperConverged
```
apt-get install openvstorage-hc
```
  *   Install the Open vStorage packages *without* the Open vStorage Backend packages
```
apt-get install openvstorage
```

-   You can now [initialize the first Storage Router](#initialize).


#### CentOS
-   Add the Open vStorage RPM repo
-   Add the Open vStorage RPM repo
    - Latest stable version
```
echo '[openvstorage]
name=OVS repo
baseurl=http://yum.openvstorage.org/CentOS/7/x86_64/dists/fargo
enabled=1
gpgcheck=0' > /etc/yum.repos.d/ovs.repo
```
    - Latest experimental version
```
echo '[openvstorage]
name=OVS repo
baseurl=http://yum.openvstorage.org/CentOS/7/x86_64/dists/unstable
enabled=1
gpgcheck=0' > /etc/yum.repos.d/ovs.repo
```
-   First install the dependencies
```
yum install --nogpgcheck --enablerepo=fc22 librbd1 librados2 gcc volumedriver-server -y
```
-   Add the necessary external repos
```
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
wget -P /etc/yum.repos.d/ http://packages.efficios.com/repo.files/EfficiOS-RHEL7-x86-64.repo
```
-   Install the Open vStorage packages
```
yum install --nogpgcheck openvstorage -y
```
- As root, disable SELinux
```
setenforce 0
```
-   You can now [initialize the first Storage Router](#initialize).

