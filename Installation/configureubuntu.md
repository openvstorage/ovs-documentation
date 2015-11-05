##### Configure Ubuntu
-   Update the root password to allow direct root login via ssh. Login
    and type
```
sudo su -
passwd
```
-   Make sure that PermitRootLogin is set to yes (instead of
    without-password) in /etc/ssh/sshd_config and restart ssh by typing
    **restart ssh**.
-   Decrease the swapiness of the OS:
```
echo 1 > /proc/sys/vm/swappiness
echo "vm.swappiness=1" >> /etc/sysctl.conf
```
-   Update the VM dirty_background_bytes:
```
echo "134217728" > /proc/sys/vm/dirty_background_bytes
echo "vm.dirty_background_bytes = 134217728" >> /etc/sysctl.conf
```
-   Configure an NTP Server
*   Install ntpd:
```
sudo apt-get install ntp
```
*   Edit /etc/ntp.conf to add/remove NTP servers. By default these servers are configured:
```
# Use servers from the NTP Pool Project. Approved by Ubuntu Technical Board
# on 2011-02-08 (LP: #104525). See http://www.pool.ntp.org/join.html for
# more information.
server 0.ubuntu.pool.ntp.org
server 1.ubuntu.pool.ntp.org
server 2.ubuntu.pool.ntp.org
server 3.ubuntu.pool.ntp.org
```
 * After changing the config file you have to reload the ntpd:
```
sudo /etc/init.d/ntp reload
```


