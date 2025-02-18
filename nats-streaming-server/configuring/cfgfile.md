# Configuration file

You can use a configuration file to configure the options specific to the NATS Streaming Server.

Use the `-sc` or `-stan_config` command line parameter to specify the file to use.

For the embedded NATS Server, you can use another configuration file and pass it to the Streaming Server using `-c` or `--config` command line parameters.

Since most options do not overlap, it is possible to combine all options into a single file and specify this file using either the `-sc` or `-c` command line parameter.

However, the option named `tls` is common to NATS Server and NATS Streaming Server. If you plan to use a single configuration file and configure TLS, you should have all the streaming configuration included in a `streaming` map. This is actually a good practice regardless if you use TLS or not, to protect against possible addition of new options in NATS Server that would conflict with the names of NATS Streaming options.

For instance, you could use a single configuration file with such content:

```text
# Some NATS Server TLS Configuration
listen: localhost:5222
tls: {
    cert_file: "/path/to/server/cert_file"
    key_file: "/path/to/server/key_file"
    verify: true
    timeout: 2
}

# NATS Streaming Configuration
streaming: {
    cluster_id: my_cluster

    tls: {
        client_cert: "/path/to/client/cert_file"
        client_key: "/path/to/client/key_file"
    }
}
```

However, if you want to avoid any possible conflict, simply use two different configuration files.

Note the order in which options are applied during the start of a NATS Streaming server:

1. Start with some reasonable default options.
2. If a configuration file is specified, override those options

   with all options defined in the file. This includes options that are defined

   but have no value specified. In this case, the zero value for the type of the

   option will be used.

3. Any command line parameter override all of the previous set options.

In general the configuration parameters are the same as the command line arguments. Below is the list of NATS Streaming parameters:

| Parameter | Meaning | Possible Values | Usage Example |
| :--- | :--- | :--- | :--- |
| cluster\_id | Cluster name | String, underscore possible | `cluster_id: "my_cluster_name"` |
| discover\_prefix | Subject prefix for server discovery by clients | NATS Subject | `discover_prefix: "_STAN.Discovery"` |
| store | Store type | `memory`, `file` or `sql` | `store: "file"` |
| dir | When using a file store, this is the root directory | File path | `dir: "/path/to/storage` |
| sd | Enable debug logging | `true` or `false` | `sd: true` |
| sv | Enable trace logging | `true` or `false` | `sv: true` |
| nats\_server\_url | If specified, connects to an external NATS Server, otherwise starts an embedded one | NATS URL | `nats_server_url: "nats://localhost:4222"` |
| secure | If true, creates a TLS connection to the server but without the need to use TLS configuration \(no NATS Server certificate verification\) | `true` or `false` | `secure: true` |
| tls | TLS Configuration | Map: `tls: { ... }` | [**See details below**](cfgfile.md#tls-configuration) |
| store\_limits | Store Limits | Map: `store_limits: { ... }` | [**See details below**](cfgfile.md#store-limits-configuration) |
| file\_options | File Store specific options | Map: `file_options: { ... }` | [**See details below**](cfgfile.md#file-options-configuration) |
| sql\_options | SQL Store specific options | Map: `sql_options: { ... }` | [**See details below**](cfgfile.md#sql-options-configuration) |
| hb\_interval | Interval at which the server sends an heartbeat to a client | Duration | `hb_interval: "10s"` |
| hb\_timeout | How long the server waits for a heartbeat response from the client before considering it a failed heartbeat | Duration | `hb_timeout: "10s"` |
| hb\_fail\_count | Count of failed heartbeats before server closes the client connection. The actual total wait is: \(fail count + 1\) \* \(hb interval + hb timeout\) | Number | `hb_fail_count: 2` |
| ft\_group | In Fault Tolerance mode, you can start a group of streaming servers with only one server being active while others are running in standby mode. This is the name of this FT group | String | `ft_group: "my_ft_group"` |
| partitioning | If set to true, a list of channels must be defined in store\_limits/channels section. This section then serves two purposes, overriding limits for a given channel or adding it to the partition | `true` or `false` | `partitioning: true` |
| cluster | Cluster Configuration | Map: `cluster: { ... }` | [**See details below**](cfgfile.md#cluster-configuration) |
| encrypt | Specify if server should encrypt messages \(only the payload\) when storing them | `true` or `false` | `encrypt: true` |
| encryption\_cipher | Cipher to use for encryption. Currently support AES and CHAHA \(ChaChaPoly\). Defaults to AES | `AES` or `CHACHA` | `encryption_cipher: "AES"` |
| encryption\_key | Encryption key. It is recommended to specify the key through the `NATS_STREAMING_ENCRYPTION_KEY` environment variable instead | String | `encryption_key: "mykey"` |

## TLS Configuration

Note that the Streaming Server uses a connection to a NATS Server, and so the NATS Streaming TLS Configuration is in fact a client-side TLS configuration.

| Parameter | Meaning | Possible Values | Usage Example |
| :--- | :--- | :--- | :--- |
| client\_cert | Client key for the streaming server | File path | `client_cert: "/path/to/client/cert_file"` |
| client\_key | Client certificate for the streaming server | File path | `client_key: "/path/to/client/key_file"` |
| client\_ca | Client certificate CA for the streaming server | File path | `client_ca: "/path/to/client/ca_file"` |

## Store Limits Configuration

| Parameter | Meaning | Possible Values | Usage Example |
| :--- | :--- | :--- | :--- |
| max\_channels | Maximum number of channels, 0 means unlimited | Number &gt;= 0 | `max_channels: 100` |
| max\_subs | Maximum number of subscriptions per channel, 0 means unlimited | Number &gt;= 0 | `max_subs: 100` |
| max\_msgs | Maximum number of messages per channel, 0 means unlimited | Number &gt;= 0 | `max_msgs: 10000` |
| max\_bytes | Total size of messages per channel, 0 means unlimited | Number &gt;= 0 | `max_bytes: 1GB` |
| max\_age | How long messages can stay in the log | Duration | `max_age: "24h"` |
| max\_inactivity | How long without any subscription and any new message before a channel can be automatically deleted | Duration | `max_inactivity: "24h"` |
| channels | A map of channel names with specific limits | Map: `channels: { ... }` | [**See details below**](cfgfile.md#channels) |

## Channels

The `channels` section is a map with the key being the channel name. For instance:

```text
   channels: {
       "foo": {
           max_msgs: 100
       }
   }
```

For a given channel, the possible parameters are:

| Parameter | Meaning | Possible Values | Usage Example |
| :--- | :--- | :--- | :--- |
| max\_subs | Maximum number of subscriptions per channel, 0 means unlimited | Number &gt;= 0 | `max_subs: 100` |
| max\_msgs | Maximum number of messages per channel, 0 means unlimited | Number &gt;= 0 | `max_msgs: 10000` |
| max\_bytes | Total size of messages per channel, 0 means unlimited | Bytes | `max_bytes: 1GB` |
| max\_age | How long messages can stay in the log | Duration | `max_age: "24h"` |
| max\_inactivity | How long without any subscription and any new message before a channel can be automatically deleted | Duration | `max_inactivity: "24h"` |

## File Options Configuration

| Parameter | Meaning | Possible Values | Usage Example |
| :--- | :--- | :--- | :--- |
| compact | Enable/disable file compaction. Only some of the files \(`clients.dat` and `subs.dat`\) are subject to compaction | `true` or `false` | `compact: true` |
| compact\_fragmentation | Compaction threshold \(in percentage\) | Number &gt;= 0 | `compact_fragmentation: 50` |
| compact\_interval | Minimum interval between attempts to compact files | Expressed in seconds | `compact_interval: 300` |
| compact\_min\_size | Minimum size of a file before compaction can be attempted | Bytes | `compact_min_size: 1GB` |
| buffer\_size | Size of buffers that can be used to buffer write operations | Bytes | `buffer_size: 2MB` |
| crc | Define if CRC of records should be computed on reads | `true` or `false` | `crc: true` |
| crc\_poly | You can select the CRC polynomial. Note that changing the value after records have been persisted would result in server failing to start complaining about data corruption | Number &gt;= 0 | `crc_poly: 3988292384` |
| sync\_on\_flush | Define if server should perform "file sync" operations during a flush | `true` or `false` | `sync_on_flush: true` |
| slice\_max\_msgs | Define the file slice maximum number of messages. If set to 0 and a channel count limit is set, then the server will set a slice count limit automatically | Number &gt;= 0 | `slice_max_msgs: 10000` |
| slice\_max\_bytes | Define the file slice maximum size \(including the size of index file\). If set to 0 and a channel size limit is set, then the server will set a slice bytes limit automatically | Bytes | `slice_max_bytes: 64MB` |
| slice\_max\_age | Define the period of time covered by a file slice, starting at when the first message is stored. If set to 0 and a channel age limit is set, then the server will set a slice age limit automatically | Duration | `slice_max_age: "24h"` |
| slice\_archive\_script | Define the location and name of a script to be invoked when the server discards a file slice due to limits. The script is invoked with the name of the channel, the name of data and index files. It is the responsibility of the script to then remove the unused files | File path | `slice_archive_script: "/home/nats-streaming/archive/script.sh"` |
| file\_descriptors\_limit | Channels translate to sub-directories under the file store's root directory. Each channel needs several files to maintain the state so the need for file descriptors increase with the number of channels. This option instructs the store to limit the concurrent use of file descriptors. Note that this is a soft limit and there may be cases when the store will use more than this number. A value of 0 means no limit. Setting a limit will probably have a performance impact | Number &gt;= 0 | `file_descriptors_limit: 100` |
| parallel\_recovery | When the server starts, the recovery of channels \(directories\) is done sequentially. However, when using SSDs, it may be worth setting this value to something higher than 1 to perform channels recovery in parallel | Number &gt;= 1 | `parallel_recovery: 4` |
| read\_buffer\_size | Size of buffers used to read ahead from message stores. This can significantly speed up sending messages to consumers after messages have been published. Default is 2MB. Set to 0 to disable | Bytes | `read_buffer_size: 2MB` |
| auto\_sync | Interval at which the store should be automatically flushed and sync'ed on disk. Default is every minute. Set to &lt;=0 to disable | Duration | `auto_sync: "2m"` |

## Cluster Configuration

| Parameter | Meaning | Possible Values | Usage Example |
| :--- | :--- | :--- | :--- |
| node\_id | ID of the node within the cluster if there is no stored ID | String \(no whitespace\) | `node_id: "node-a"` |
| bootstrap | Bootstrap the cluster if there is no existing state by electing self as leader | `true` or `false` | `bootstrap: true` |
| peers | List of cluster peer node IDs to bootstrap cluster state | List of node IDs | `peers: ["node-b", "node-c"]` |
| log\_path | Directory to store log replication data | File path | `log_path: "/path/to/storage"` |
| log\_cache\_size | Number of log entries to cache in memory to reduce disk IO | Number &gt;= 0 | `log_cache_size: 1024` |
| log\_snapshots | Number of log snapshots to retain | Number &gt;= 0 | `log_snapshots: 1` |
| trailing\_logs | Number of log entries to leave after a snapshot and compaction | Number &gt;= 0 | `trailing_logs: 256` |
| sync | Do a file sync after every write to the replication log and message store | `true` or `false` | `sync: true` |
| raft\_logging | Enable logging from the Raft library \(disabled by default\) | `true` or `false` | `raft_logging: true` |
| raft\_heartbeat\_timeout | Specifies the time in follower state without a leader before attempting an election | Duration | `raft_heartbeat_timeout: "2s"` |
| raft\_election\_timeout | Specifies the time in candidate state without a leader before attempting an election | Duration | `raft_election_timeout: "2s"` |
| raft\_lease\_timeout | Specifies how long a leader waits without being able to contact a quorum of nodes before stepping down as leader | Duration | `raft_lease_timeout: "1s"` |
| raft\_commit\_timeout | Specifies the time without an Apply\(\) operation before sending an heartbeat to ensure timely commit. Due to random staggering, may be delayed as much as 2x this value | Duration | `raft_commit_timeout: "100ms"` |

## SQL Options Configuration

| Parameter | Meaning | Possible Values | Usage Example |
| :--- | :--- | :--- | :--- |
| driver | Name of the SQL driver to use | `mysql` or `postgres` | `driver: "mysql"` |
| source | How to connect to the database. This is driver specific | String | `source: "ivan:pwd@/nss_db"` |
| no\_caching | Enable/Disable caching for messages and subscriptions operations. The default is `false`, which means that caching is enabled | `true` or `false` | `no_caching: false` |
| max\_open\_conns | Maximum number of opened connections to the database. Value &lt;= 0 means no limit. The default is 0 \(unlimited\) | Number | `max_open_conns: 5` |

