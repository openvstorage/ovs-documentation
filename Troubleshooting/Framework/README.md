### Framework Troubleshooting

#### Watcher is stuck in pre-start
When a watcher is stuck in pre-start, a master service will be down. As a first step, figure out what master service is down by checking the logs under `/var/log/ovs/extenions.log`. This logfile contains a lot, so you might want to grep for `[extensions] - [watcher]`. Every couple of seconds, there will be either a report that everything works correct. In case something is wrong you will find what is wrong exactly.

A few examples:

```
2015-10-06 09:08:50,636 - [6625] - [DEBUG] - [extensions] - [watcher] - [volumedriver]   Error during arakoon (voldrv) test: Could not determine the Arakoon master node
2015-10-06 09:08:51,637 - [6625] - [DEBUG] - [extensions] - [watcher] - [volumedriver]   Arakoon (voldrv) not working correctly
2015-10-06 09:08:51,698 - [6627] - [DEBUG] - [extensions] - [watcher] - [framework]   Error during persistent store test: Could not determine the Arakoon master node
2015-10-06 09:08:52,699 - [6627] - [DEBUG] - [extensions] - [watcher] - [framework]   Persistent store not working correctly
```

In the above case Arakoon isn't running. Note that when a node boots up, the watcher might be started before the master services themself, so it doesn't always indicate a problem when you see this. The time in these lines might indicate whether it's an issue or not. On startup, you will see that everything turns out fine after a while when the master services are finally all started.

```
2015-10-06 12:18:38,884 - [6627] - [DEBUG] - [extensions] - [watcher] - [framework]   Error during rabbitMQ test on node 172.19.5.4:5672: 1
2015-10-06 12:18:38,885 - [6627] - [DEBUG] - [extensions] - [watcher] - [framework]   Error during rabbitMQ test on node 172.19.5.5:5672: 1
2015-10-06 12:18:38,886 - [6627] - [DEBUG] - [extensions] - [watcher] - [framework]   Error during rabbitMQ test on node 172.19.5.6:5672: 1
2015-10-06 12:18:38,886 - [6627] - [DEBUG] - [extensions] - [watcher] - [framework]   No working rabbitMQ node could be found
```

In the above example RabbitMQ isn't running.

#### Rebuilding a RabbitMQ cluster

* Stop the Open vStorage workers at all nodes by stopping RabbitMQ
```
rabbitmqctl stop_app
rabbitmqctl reset
```
* On the first master node start RabbitMQ again
```
rabbitmqctl start_app
```
* Have all other master nodes join the RabbitMQ cluster
```
rabbitmqctl join_cluster {<node_name first node>}
```

##### An example
* First node
```
root@ctl01:~# rabbitmqctl stop_app
Stopping node rabbit@ctl01 ...
...done.
root@ctl01:~# stop ovs-workers
stop: Unknown instance:
root@ctl01:~# rabbitmqctl list_queues
Listing queues ...
Error: {aborted,{no_exists,[rabbit_queue,
                            {amqqueue,{resource,<<"/">>,queue,'_'},
                                      '_','_','_','_','_','_','_','_','_',
                                      '_'}]}}
root@ctl01:~# rabbitmqctl cluster_status
Cluster status of node rabbit@ctl01 ...
[{nodes,[{disc,[rabbit@ctl01]}]}]
...done.
root@ctl01:~# rabbitmqctl start_app
Starting node rabbit@ctl01 ...
...done.
```
* Other master nodes
```
root@ctl02:~# rabbitmqctl stop_app
Stopping node rabbit@ctl02 ...
...done.
root@ctl02:~# rabbitmqctl reset
Resetting node rabbit@ctl02 ...
...done.
root@ctl02:~# rabbitmqctl join_cluster rabbit@ctl01
Clustering node rabbit@ctl02 with rabbit@ctl01 ...
...done.
root@ctl02:~# rabbitmqctl start_app
Starting node rabbit@ctl02 ...
...done.
```
* Check the cluster status
```
root@ctl02:~# rabbitmqctl cluster_status
Cluster status of node rabbit@ctl02 ...
[{nodes,[{disc,[rabbit@ctl01,rabbit@ctl02,rabbit@ctl03]}]},
 {running_nodes,[rabbit@ctl03,rabbit@ctl01,rabbit@ctl02]},
 {partitions,[]}]
...done.
```
* Check if RabbitMQ and the ovs-workers are back up
Login to the Open vStorage GUI and go to Administration > Support and check if there is a process ID for every Storage Router.
