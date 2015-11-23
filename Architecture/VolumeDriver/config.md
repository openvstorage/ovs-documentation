#### VolumeDriver Config file

| component | key | default value | dynamically reconfigurable | remarks |
| --- | --- | -- | --- | --- |
 | volume_router | vrouter_local_io_sleep_before_retry_usecs | "100000" | yes | delay (microseconds) before rerrying a request that failed with a retryable error |
 | volume_router | vrouter_local_io_retries | "600" | yes | number of retry attempts for requests that failed with a retryable error |
 | volume_router | vrouter_volume_read_threshold | "0" | yes | number of remote read requests before auto-migrating a volume - 0 turns it off |
 | volume_router | vrouter_volume_write_threshold | "0" | yes | number of remote write requests before auto-migrating a volume - 0 turns it off |
 | volume_router | vrouter_file_read_threshold | "0" | yes | number of remote read requests before auto-migrating a file - 0 turns it off |
 | volume_router | vrouter_file_write_threshold | "0" | yes | number of remote write requests before auto-migrating a file - 0 turns it off |
 | volume_router | vrouter_redirect_timeout_ms | "0" | yes | timeout for redirected requests in milliseconds - 0 turns it off |
 | volume_router | vrouter_redirect_retries | "10" | yes | number of retries to after a redirect timed out |
 | volume_router | vrouter_id | --- | no | the vrouter_id of this node of the cluster. Must be one of the vrouter_id's s specified in the vrouter_cluster_nodes section |
 | volume_router | vrouter_sco_multiplier | "1024" | no | number of clusters in a sco |
 | volume_router | vrouter_routing_retries | "30" | yes | number of times the routing shall be retried in case the volume is not found (exponential backoff inbetween retries!) |
 | volume_router | vrouter_min_workers | "4" | yes | minimum number of worker threads to handle redirected requests |
 | volume_router | vrouter_max_workers | "16" | yes | maximum number of worker threads to handle redirected requests |
 | volume_router_cluster | vrouter_cluster_id | --- | no | cluster_id of the volumeroutercluster this node belongs to |
 | filesystem | fs_ignore_sync | "0" | yes | ignore sync requests - AT THE POTENTIAL EXPENSE OF DATA LOSS |
 | filesystem | fs_virtual_disk_format | --- | no | virtual disk format: vmdk or raw |
 | filesystem | fs_raw_disk_suffix | "" | no | Suffix to use when creating clones if fs_virtual_disk_format=raw |
 | filesystem | fs_max_open_files | "65536" | no | Maximum number of open files, is set using rlimit() on startup |
 | filesystem | fs_file_event_rules | --- | no | an array of filesystem event rules, each consisting of a "path_regex" and an array of "fs_calls" |
 | filesystem | fs_metadata_backend_type | "TCBT" | no | Type of metadata backend to use for volumes created via the filesystem interface |
 | filesystem | fs_metadata_backend_arakoon_cluster_id | "" | no | Arakoon cluster identifier for the volume metadata |
 | filesystem | fs_metadata_backend_arakoon_cluster_nodes | --- | no | an array of arakoon cluster node configurations for the volume metadata, each containing node_id, host and port |
 | filesystem | fs_metadata_backend_mds_nodes | --- | no | an array of MDS node configurations for the volume metadata, each containing host and port |
 | event_publisher | events_amqp_uris | --- | no | array of URIs, each consisting of an "amqp_uri" entry for a node of the AMQP cluster events shall be sent to |
 | event_publisher | events_amqp_exchange | "" | no | AMQP exchange events will be sent to |
 | event_publisher | events_amqp_routing_key | "" | no | AMQP routing key used for sending events |
 | failovercache | failovercache_path | --- | no | path to the directory the failovercache writes its data in  |
 | volume_registry | vregistry_arakoon_timeout_ms | "60000" | no | Arakoon client timeout in milliseconds for the volume registry |
 | volume_registry | vregistry_arakoon_cluster_id | --- | no | Arakoon cluster identifier for the volume registry |
 | volume_registry | vregistry_arakoon_cluster_nodes | --- | no | an array of arakoon cluster node configurations for the volume registry, each containing node_id, host and port |
 | file_driver | fd_cache_path | --- | no | cache for filedriver objects |
 | file_driver | fd_namespace | --- | no | backend namespace to use for filedriver objects |
 | file_driver | fd_extent_cache_capacity | "1024" | yes | number of extents the extent cache can hold |
 | metadata_server | mds_db_type | "ROCKSDB" | no | Type of database to use for metadata. Supported values: ROCKSDB |
 | metadata_server | mds_cached_pages | "256" | no | Capacity of the metadata page cache per volume |
 | metadata_server | mds_poll_secs | "300" | yes | Poll interval for the backend check in seconds |
 | metadata_server | mds_address | "localhost" | no | IP address or hostname the metadata server will listen on |
 | metadata_server | mds_port | --- | no | TCP port the metadata server will listen on |
 | metadata_server | mds_scratch_dir | --- | no | path to a temp directory that will be used to temporarily retrieve backend objects (TLogs) to |
 | metadata_server | mds_rocksdb_path | --- | no | path to the RocksDB directory |
 | threadpool_component | num_threads | "4" | yes | Number of threads writting SCOs to DSS |
 | volume_manager | metadata_path | --- | no | Directory, where to create subdirectories in for volume metadata storage |
 | volume_manager | tlog_path | --- | no | Directory, where to create subdirectories for volume tlogs |
 | volume_manager | open_scos_per_volume | "32" | no | Number of open SCOs per volume |
 | volume_manager | foc_throttle_usecs | "1000" | yes | Timeout when throttling the Failover Cache |
 | volume_manager | foc_queue_depth | "1024" | yes | Size of the queue of entries in the Failover Cache |
 | volume_manager | foc_write_trigger | "8" | yes | Trigger to start writing entries in the foc queue to the backend |
 | volume_manager | clean_interval | --- | yes | Interval between runs of scocache cleanups, in seconds.
Should be small when running on ramdisk, larger when running on sata.
scocache_cleanup_trigger / clean_interval should be larger than the aggregated write speed to the scocache. |
 | volume_manager | sap_persist_interval | "300" | yes | Interval between writing SAP data, in seconds |
 | volume_manager | failovercache_check_interval_in_seconds | "300" | yes | Interval between checks of the failovercache state on the volumes |
 | volume_manager | read_cache_default_behaviour | "CacheOnRead" | yes | Default read cache behaviour, should be CacheOnWrite, CacheOnRead or NoCache |
 | volume_manager | required_tlog_freespace | "209715200" | yes | Required free space in the tlog directory |
 | volume_manager | required_meta_freespace | "209715200" | yes | Required free space in the metadata directory |
 | volume_manager | freespace_check_interval | "10" | yes | Interval between checks of required freespace parameters, in seconds |
 | volume_manager | number_of_scos_in_tlog | "20" | no | The number of SCOs that trigger a tlog rollover |
 | volume_manager | non_disposable_scos_factor | "1.5" | no | Factor to multiply number_of_scos_in_tlog with to determine the amount of non-disposable data permitted per volume |
 | volume_manager | debug_metadata_path | "/opt/qbase3/var/lib/volumedriver/evidence" | no | place to store evidence when a volume is halted. |
 | volume_manager | arakoon_metadata_sequence_size | "10" | no | Size of Arakoon sequences used to send metadata pages to Arakoon |
 | scocache | trigger_gap | --- | no | scocache-mountpoint freespace threshold below which scocache-cleaner is triggered |
 | scocache | backoff_gap | --- | no | scocache-mountpoint freespace objective for scocache-cleaner |
 | scocache | scocache_mount_points | --- | no | An array of directories and sizes to be used as scocache mount points |
 | content_addressed_cache | read_cache_serialization_path | --- | no | Directory to store the serialization of the Read Cache |
 | content_addressed_cache | serialize_read_cache | "1" | no | Whether to serialize the readcache on exit or not |
 | content_addressed_cache | clustercache_mount_points | --- | no | An array of directories and sizes to be used as Read Cache mount points |
 | backend_connection_manager | backend_type | "LOCAL" | no | Type of backend connection one of LOCAL, REST or S3, the other parameters in this section are only used when their correct backendtype is set |
 | backend_connection_manager | rest_connection_host | "127.0.0.1" | no | When backend_type is REST: the REST host to connect to, otherwise ignored |
 | backend_connection_manager | rest_connection_port | "100" | no | When backend_type is REST: the REST port to connect to, otherwise ignored |
 | backend_connection_manager | rest_connection_policy_id | --- | yes | When backend_type is REST: the policy id to use for creation of namespaces, otherwise ignored |
 | backend_connection_manager | rest_connection_timeout_secs | "0" | no | When backend_type is REST: the REST connection timeout, otherwise ignored |
 | backend_connection_manager | rest_connection_metadata_format | "JSON" | no | When backend_type is REST: the REST metadata format, one of XML or JSON, otherwise ignored |
 | backend_connection_manager | rest_connection_entries_per_chunk | "1000" | no | When backend_type is REST: The number of rest connection entries per chunk, otherwise ignored |
 | backend_connection_manager | rest_connection_verbose_logging | "0" | no | When backend_type is REST: whether to do verbose REST logging, otherwise ignored |
 | backend_connection_manager | rest_connection_user | "" | no | When backend_type is REST: user to authenticate as |
 | backend_connection_manager | rest_connection_password | "" | no | When backend_type is REST: password to use for authentication |
 | backend_connection_manager | rest_connection_encryption_policy | "" | no | When backend_type is REST: encryption policy id to use for the backend encryption |
 | backend_connection_manager | local_connection_path | --- | no | When backend_type is LOCAL: path to use as LOCAL backend, otherwise ignored |
 | backend_connection_manager | s3_connection_host | "s3.amazonaws.com" | no | When backend_type is S3: the S3 host to connect to, otherwise ignored |
 | backend_connection_manager | s3_connection_port | "80" | no | When backend_type is S3: the S3 port to connect to, otherwise ignored |
 | backend_connection_manager | s3_connection_username | "wim.leus@cloudfounders.com" | no | When backend_type is S3: the S3 username, otherwise ignored |
 | backend_connection_manager | s3_connection_password | "" | no | When backend_type is S3: the S3 password |
 | backend_connection_manager | s3_connection_verbose_logging | "1" | no | When backend_type is S3: whether to do verbose logging |
 | backend_connection_manager | s3_connection_use_ssl | "0" | no | When backend_type is S3: whether to use SSL to encrypt the connection |
 | backend_connection_manager | s3_connection_ssl_verify_host | "1" | no | When backend_type is S3: whether to verify the SSL certificate's ssubject against the host |
 | backend_connection_manager | s3_connection_ssl_cert_file | "" | no | When backend_type is S3: path to a file holding the SSL certificate |
 | backend_connection_manager | s3_connection_flavour | "S3" | no | S3 backend flavour: S3 (default), GCS, WALRUS or SWIFT |
 | backend_connection_manager | alba_connection_host | "127.0.0.1" | no | When backend_type is ALBA: the ALBA host to connect to, otherwise ignored |
 | backend_connection_manager | alba_connection_port | "56789" | no | When the backend_type is ALBA: The ALBA port to connection to, otherwise ignored |