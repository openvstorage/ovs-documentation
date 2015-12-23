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
