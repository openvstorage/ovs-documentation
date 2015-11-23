#### How to troubleshoot an invalid outputstream exception when creating a new volume

##### Description

If you fail to create new volumes with the following error messages in the cinder and volumedriver logs, your root partition might have filled up and caused the alba proxy to exit. (Naturally it's also possible that different root causes made the alba proxy die)

Error message in the cinder-scheduler.log on the controller

```
root@ctl01:/var/log/cinder# less cinder-scheduler.log
2015-09-28 16:01:42.216 893 ERROR cinder.scheduler.filter_scheduler [req-7e4b7a6f-43f6-4178-9bc1-bf9492e83969 222d7c955a4341f7b17b0879bc1cf773 c774d7aaaeac49989029f6ab3450278a - - -]
Error scheduling None from last vol-service: tcpcmp01@tcp-vpool1#tcp-vpool1 : [u'Traceback (most recent call last):\n', u'  File "/usr/lib/python2.7/dist-packages/taskflow/engines/action_engine/executor.py",
line 34, in _execute_task\n    result = task.execute(**arguments)\n', u'  File "/usr/lib/python2.7/dist-packages/cinder/volume/flows/manager/create_volume.py", line 638, in execute\n    **volume_spec)\n', u'
File "/usr/lib/python2.7/dist-packages/cinder/volume/flows/manager/create_volume.py", line 579, in _create_from_image\n    model_update = self.driver.create_volume(volume_ref)\n', u'
File "/usr/lib/python2.7/dist-packages/osprofiler/profiler.py", line 105, in wrapper\n    return f(*args, **kwargs)\n', u'  File "/usr/lib/python2.7/dist-packages/cinder/volume/drivers/openvstorage.py",
line 146, in create_volume\n    size = size)\n', u'  File "/usr/lib/python2.7/dist-packages/celery/local.py", line 167, in <lambda>\n    __call__ = lambda x, *a, **kw: x._get_current_object()(*a, **kw)\n', u'
File "/usr/lib/python2.7/dist-packages/celery/app/task.py", line 419, in __call__\n    return self.run(*args, **kwargs)\n', u'  File "/opt/OpenvStorage/ovs/lib/vdisk.py", line 383nnnnn, in create_volume\n
output = check_output(\'truncate -s {0}G "{1}"\'.format(size, location), shell=True).strip()\n', u'  File "/usr/lib/python2.7/subprocess.py", line 573, in check_output\n    raise CalledProcessError(retcode,
cmd, output=output)\n', u'CalledProcessError: Command \'truncate -s 30G "/mnt/tcp-vpool1/kaboom.raw"\' returned non-zero exit status 1\n']
```

Error message in the tcp-vpool1.log (volumedriver) on the compute node
```
root@tcpcmp01:/var/log/ovs/volumedriver# less tcp-vpool1.log | grep kaboom
2015-09-28 16:01:39:970797 +0200 CEST -- error -- FileSystem -- do_mknod: Failed to create object Volume-f1560536-1b12-4aa0-b5be-d8129f387e23 @ "/kaboom.raw", mode 0644, uid 116, gid 121: invalid outputstream -- [0x00007f6c6affd700]
2015-09-28 16:01:39:972509 +0200 CEST -- error -- FuseInterface -- convert_exceptions: "/kaboom.raw": caught exception invalid outputstream: returning I/O error -- [0x00007f6c6affd700]
2015-09-28 16:01:41:904035 +0200 CEST -- error -- FileSystem -- do_mknod: Failed to create object Volume-a4689940-6109-48c3-8df1-5b6f6d6d0ecd @ "/kaboom.raw", mode 0644, uid 116, gid 121: invalid outputstream -- [0x00007f6c8dffb700]
2015-09-28 16:01:41:908321 +0200 CEST -- error -- FuseInterface -- convert_exceptions: "/kaboom.raw": caught exception invalid outputstream: returning I/O error -- [0x00007f6c8dffb700]
2015-09-28 16:01:45:599412 +0200 CEST -- error -- FileSystem -- do_mknod: Failed to create object Volume-528c86a4-c08a-4976-8fa5-8d44903d71d2 @ "/kaboom.raw", mode 0644, uid 116, gid 121: invalid outputstream -- [0x00007f6c68ffe700]
2015-09-28 16:01:45:603692 +0200 CEST -- error -- FuseInterface -- convert_exceptions: "/kaboom.raw": caught exception invalid outputstream: returning I/O error -- [0x00007f6c68ffe700]
```

##### Step to resolution
###### Determine the root cause
Check if the alba proxy died
```
root@tcpcmp01:~# ps auxf | grep alba

root     22073  0.3  0.3 721072 197876 ?       Ssl  Sep28   2:52 /usr/bin/alba proxy-start --config /opt/OpenvStorage/config/storagedriver/storagedriver/tcp-vpool1_alba.json
```
###### Check filesystem usage
If you see nothing in the /var/log/upstart/ovs-albaproxy-...log that tells you a specific problem, it's likely that the filesystem filled up at some time.
Check this with df -h.

###### Start or restart the ovs-albaproxy
```
root@tcpcmp01:~# start ovs-albaproxy_tcp-vpool1
```

###### Check if the process is running
```

root@tcpcmp01:~# ps auxf | grep alba

root     22073  0.3  0.3 721072 197876 ?       Ssl  Sep28   2:52 /usr/bin/alba proxy-start --config /opt/OpenvStorage/config/storagedriver/storagedriver/tcp-vpool1_alba.json
```