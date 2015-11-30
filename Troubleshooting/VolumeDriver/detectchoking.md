#### How to detect choking of the Backend

##### Description
Open vStorage uses the SSDs as write buffer before sending data to the Storage Backend. In case the Backend is too slow and can't ingest in time the data from the write buffer, the Volume Driver will slow down the ingest coming from the VMs. This throttling can be found as *choking* in the Volume Driver log of the node which has the problem.

```
tail -f ovs/volumedriver/vpool_name.log | grep -i chok
```

