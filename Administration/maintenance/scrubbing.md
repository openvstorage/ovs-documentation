### Scrubbing
This explanation is mainly written for debugging purposes and trying to allow anyone to understand how scrubbing work is divided

##### General information
1. Automated process
2. Cannot be configured manually
3. Executed once every day at 3AM
4. Only executed on StorageRouters with a SCRUB role assigned

##### Debugging
Scrubbing work is divided among a number of threads that are spawned on the StorageRouters with a SCRUB partition
A thread will be spawned per vPool and per SCRUB partition up to a maximum (see below) and in a way to divide the load as fair as possible
Each thread will set up an ALBA proxy, therefore the amount of ALBA proxies will be identical to the amount of spawned threads
Amount of threads:

|   vPools  | Max threads    |
| --------- | -------------- |
|     1     |       5        |
|     2     |       5        |
|     3     |       2        |
|     4     |       2        |
|     5     |       2        |
|     6     |       1        |
|     7     |       1        |
|    ...    |       1        |

Example 1: 4 vPools and 3 StorageRouters with a SCRUB partition
In theory 4 * 3 threads could be spawned, but only 8 will be spawned like this:

|         |   SR 1   |   SR 2   |   SR 3   |
| ------- | -------- | -------- | -------- |
| vpool 1 | thread 1 | thread 2 |          |
| vpool 2 | thread 2 |          | thread 1 |
| vpool 3 |          | thread 1 | thread 2 |
| vpool 4 | thread 1 | thread 2 |          |

So in above example each vPool has 2 threads reponsible for scrubbing its vDisks
StorageRouter 1 will scrub the vDisks for vPool 1, vPool 2 and vPool 4
StorageRouter 2 will scrub the vDisks for vPool 1, vPool 3 and vPool 4
StorageRouter 3 will scrub the vDisks for vPool 2 and vPool 3

Example 2: 1 vPool and 2 StorageRouters with a SCRUB partition
In theory 5 threads could be spawned, but since we only have 2 SCRUB partitions, only 2 threads will be spawned

Example 3: 1 vPool and 7 StorageRouters with a SCRUB partition
We now have more available SCRUB partitions than max amount of threads (5), so now 5 random StorageRouters will be selected out of the 7 available

Each vPool will have its own queue filled with only the vDisks belonging to the vPool
This queue will then be emptied by the threads responsible for scrubbing of the vDisks of the vPool

Each thread is responsible for the following actions:
* Deploy ALBA proxy on StorageRouter its running on
* Perform the scrub work for vDisks of the corresponding vPool of the thread
* Remove the ALBA proxy once all scrubbing has finished (or failed)
