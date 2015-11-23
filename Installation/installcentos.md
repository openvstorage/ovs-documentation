#### <a name="centos"></a>Install CentOS on all hosts
-   Install CentOS7
-   Install wget
```
yum install wget -y
```
-   Add the necessary external repos
```
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
wget -P /etc/yum.repos.d/ http://packages.efficios.com/repo.files/EfficiOS-RHEL7-x86-64.repo
```
-   Currently there is a dependency on the Fedora 22 repo so add it also
```
echo '[fc22]
name=fc22
baseurl=https://download.fedoraproject.org/pub/fedora/linux/releases/22/Everything/x86_64/os
enabled=0' > /etc/yum.repos.d/fc22.repo
rpm --import 'https://pgp.mit.edu/pks/lookup?op=get&search=0x11ADC0948E1431D5'
```
-   Add the Open vStorage RPM repo
```
echo '[openvstorage]
name=OVS repo
baseurl=http://yum.openvstorage.org/CentOS/7/x86_64/dists/denver
enabled=1
gpgcheck=0' > /etc/yum.repos.d/ovs.repo
```
-   First install the dependencies
```
yum install --nogpgcheck --enablerepo=fc22 librbd1 librados2 gcc volumedriver-server -y
```
-   Install the Open vStorage packages
```
yum install --nogpgcheck openvstorage -y
```
- As root, disable SELinux
```
setenforce 0
```
-   You can now go to the [Install the Open vStorage software](#installovs) section.

Repeat the above steps for all nodes in the Open vStorage Cluster.