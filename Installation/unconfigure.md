## Unconfigure Open vStorage


### <a name="unconfigurecinder" </a> Manually unconfigure the Cinder changes
- Revert the libvirtd changes on all Nova nodes in /etc/libvirt/libvirtd.conf,  /etc/libvirt/qemu.conf,  /etc/default/libvirt-bin and /etc/init/libvirt-bin.conf.
- Restart libvirt-bin
```
stop libvirt-bin && start libvirt-bin || start libvirt-bin
ps -ef | grep libvirtd
```
- Remove or revert to the default value the changes made to the instances_path from /etc/nova/nova.conf. Restart n-api and n-cpu services.
- Remove the added users to the
```
deluser stack ovs; deluser libvirt-qemu ovs
```
- Disable messaging and consumer
    - Stop the events consumer by "executing service ovs-openstack-events-consumer stop".
    - Remove the lines containing "notification\*" and "notify\*" from /etc/nova/nova.conf.
    - Remove the lines containing "notification*" from /etc/cinder/cinder.conf.
    - Restart the n-api and c-api services
-  Remove the patched code
    - In opt/stack/nova/nova/virt/libvirt/volume.py: find the line starting with "class LibvirtFileVolumeDriver" and remove the whole class definition.
    - In /opt/stack/nova/nova/virt/libvirt/driver.py: find the line "'file=nova.virt.libvirt.volume.LibvirtFileVolumeDriver'," and remove it.
    - In /opt/stack/cinder/cinder/brick/initiator/connector.py: find the line "elif protocol in ["LOCAL", "FILE"]: " and replace with "elif protocol == "LOCAL": ".
    - Restart the c-vol and n-cpu services.
