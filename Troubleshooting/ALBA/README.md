### ALBA

### How to check why an ASD node isn't popping up in the GUI
There might be various reasons why an ASD node can't get discovered by Open vStorage. Check the `/var/log/upstart/ovs-workers.log` on any node for `add_cluster job`.

You will find an error similar the one below:

```
[2015-12-08 13:45:00,628: ERROR/MainProcess] Task alba.scheduled_alba_arakoon_checkup[cdf46aa6-e9a8-4a65-82ec-5c2372e21f49] raised unexpected: RuntimeError('Could not find any partitions with DB role',)
Traceback (most recent call last):
File "/usr/lib/python2.7/dist-packages/celery/app/trace.py", line 218, in trace_task
R = retval = fun(*args, **kwargs)
File "/usr/lib/python2.7/dist-packages/celery/app/trace.py", line 398, in _protected_call_
return self.run(*args, **kwargs)
File "/opt/OpenvStorage/ovs/lib/helpers/decorators.py", line 165, in new_function
output = function(*args, **kwargs)
File "/opt/OpenvStorage/ovs/lib/albacontroller.py", line 347, in scheduled_alba_arakoon_checkup
AlbaController._alba_arakoon_checkup(create_nsm_cluster=False)
File "/opt/OpenvStorage/ovs/lib/albacontroller.py", line 405, in _alba_arakoon_checkup
raise RuntimeError('Could not find any partitions with DB role')
RuntimeError: Could not find any partitions with DB role
```