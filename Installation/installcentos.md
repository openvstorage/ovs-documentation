#### <a name="centos"></a>Install CentOS on all hosts
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

Repeat the above steps for all nodes in the Open vStorage Cluster.
