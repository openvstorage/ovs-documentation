#### Open vStorage on CentOS

Next to Ubunu, Open vStorage should work also work on CentOS7. When you encounter issues or are stuck somewhere, do not hesitate to ask for help in the public [Open vStorage
Forum](https://groups.google.com/forum/#!forum/open-vstorage).

The below section only provides how to setup Open vStorage on CentOS. Please refer to the [general documentation](geoscale.md) for the complete setup instructions.

-   Install CentOS7
-   Install wget
```
yum install wget -y
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
-   Run the `ovs setup` command as explained in the [general documentation](geoscale.md).

