```bash
redis-cli -p 6379 -h 10.129.58.88 INFO
```

[/home/kali/Vault/Target/10.129.58.88/result/autorecon/10.129.58.88/scans/tcp6379/tcp_6379_redis_info.txt](file:///home/kali/Vault/Target/10.129.58.88/result/autorecon/10.129.58.88/scans/tcp6379/tcp_6379_redis_info.txt):

```
# Server
redis_version:5.0.7
redis_git_sha1:00000000
redis_git_dirty:0
redis_build_id:66bd629f924ac924
redis_mode:standalone
os:Linux 5.4.0-77-generic x86_64
arch_bits:64
multiplexing_api:epoll
atomicvar_api:atomic-builtin
gcc_version:9.3.0
process_id:752
run_id:7d57688c0add7176e5a62e3a5be62867af927c11
tcp_port:6379
uptime_in_seconds:983
uptime_in_days:0
hz:10
configured_hz:10
lru_clock:12999854
executable:/usr/bin/redis-server
config_file:/etc/redis/redis.conf

# Clients
connected_clients:1
client_recent_max_input_buffer:4
client_recent_max_output_buffer:0
blocked_clients:0

# Memory
used_memory:859624
used_memory_human:839.48K
used_memory_rss:5120000
used_memory_rss_human:4.88M
used_memory_peak:859624
used_memory_peak_human:839.48K
used_memory_peak_perc:100.00%
used_memory_overhead:846142
used_memory_startup:796224
used_memory_dataset:13482
used_memory_dataset_perc:21.26%
allocator_allocated:1201688
allocator_active:1458176
allocator_resident:4218880
total_system_memory:2084024320
total_system_memory_human:1.94G
used_memory_lua:41984
used_memory_lua_human:41.00K
used_memory_scripts:0
used_memory_scripts_human:0B
number_of_cached_scripts:0
maxmemory:0
maxmemory_human:0B
maxmemory_policy:noeviction
allocator_frag_ratio:1.21
allocator_frag_bytes:256488
allocator_rss_ratio:2.89
allocator_rss_bytes:2760704
rss_overhead_ratio:1.21
rss_overhead_bytes:901120
mem_fragmentation_ratio:6.43
mem_fragmentation_bytes:4323272
mem_not_counted_for_evict:0
mem_replication_backlog:0
mem_clients_slaves:0
mem_clients_normal:49694
mem_aof_buffer:0
mem_allocator:jemalloc-5.2.1
active_defrag_running:0
lazyfree_pending_objects:0

# Persistence
loading:0
rdb_changes_since_last_save:0
rdb_bgsave_in_progress:0
rdb_last_save_time:1774607452
rdb_last_bgsave_status:ok
rdb_last_bgsave_time_sec:0
rdb_current_bgsave_time_sec:-1
rdb_last_cow_size:409600
aof_enabled:0
aof_rewrite_in_progress:0
aof_rewrite_scheduled:0
aof_last_rewrite_time_sec:-1
aof_current_rewrite_time_sec:-1
aof_last_bgrewrite_status:ok
aof_last_write_status:ok
aof_last_cow_size:0

# Stats
total_connections_received:6
total_commands_processed:5
instantaneous_ops_per_sec:0
total_net_input_bytes:276
total_net_output_bytes:3316
instantaneous_input_kbps:0.00
instantaneous_output_kbps:0.00
rejected_connections:0
sync_full:0
sync_partial_ok:0
sync_partial_err:0
expired_keys:0
expired_stale_perc:0.00
expired_time_cap_reached_count:0
evicted_keys:0
keyspace_hits:0
keyspace_misses:0
pubsub_channels:0
pubsub_patterns:0
latest_fork_usec:411
migrate_cached_sockets:0
slave_expires_tracked_keys:0
active_defrag_hits:0
active_defrag_misses:0
active_defrag_key_hits:0
active_defrag_key_misses:0

# Replication
role:master
connected_slaves:0
master_replid:2d3e01f183055881e5a9c7ed37e63dcfce4b8d97
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0

# CPU
used_cpu_sys:1.195798
used_cpu_user:1.027476
used_cpu_sys_children:0.002055
used_cpu_user_children:0.000000

# Cluster
cluster_enabled:0

# Keyspace
db0:keys=4,expires=0,avg_ttl=0


```
```bash
redis-cli -p 6379 -h 10.129.58.88 CONFIG GET '*'
```

[/home/kali/Vault/Target/10.129.58.88/result/autorecon/10.129.58.88/scans/tcp6379/tcp_6379_redis_config.txt](file:///home/kali/Vault/Target/10.129.58.88/result/autorecon/10.129.58.88/scans/tcp6379/tcp_6379_redis_config.txt):

```
dbfilename
dump.rdb
requirepass

masterauth

cluster-announce-ip

unixsocket

logfile
/var/log/redis/redis-server.log
pidfile
/var/run/redis/redis-server.pid
slave-announce-ip

replica-announce-ip

maxmemory
0
proto-max-bulk-len
536870912
client-query-buffer-limit
1073741824
maxmemory-samples
5
lfu-log-factor
10
lfu-decay-time
1
timeout
0
active-defrag-threshold-lower
10
active-defrag-threshold-upper
100
active-defrag-ignore-bytes
104857600
active-defrag-cycle-min
5
active-defrag-cycle-max
75
active-defrag-max-scan-fields
1000
auto-aof-rewrite-percentage
100
auto-aof-rewrite-min-size
67108864
hash-max-ziplist-entries
512
hash-max-ziplist-value
64
stream-node-max-bytes
4096
stream-node-max-entries
100
list-max-ziplist-size
-2
list-compress-depth
0
set-max-intset-entries
512
zset-max-ziplist-entries
128
zset-max-ziplist-value
64
hll-sparse-max-bytes
3000
lua-time-limit
5000
slowlog-log-slower-than
10000
latency-monitor-threshold
0
slowlog-max-len
128
port
6379
cluster-announce-port
0
cluster-announce-bus-port
0
tcp-backlog
511
databases
16
repl-ping-slave-period
10
repl-ping-replica-period
10
repl-timeout
60
repl-backlog-size
1048576
repl-backlog-ttl
3600
maxclients
10000
watchdog-period
0
slave-priority
100
replica-priority
100
slave-announce-port
0
replica-announce-port
0
min-slaves-to-write
0
min-replicas-to-write
0
min-slaves-max-lag
10
min-replicas-max-lag
10
hz
10
cluster-node-timeout
15000
cluster-migration-barrier
1
cluster-slave-validity-factor
10
cluster-replica-validity-factor
10
repl-diskless-sync-delay
5
tcp-keepalive
300
cluster-require-full-coverage
yes
cluster-slave-no-failover
no
cluster-replica-no-failover
no
no-appendfsync-on-rewrite
no
slave-serve-stale-data
yes
replica-serve-stale-data
yes
slave-read-only
yes
replica-read-only
yes
slave-ignore-maxmemory
yes
replica-ignore-maxmemory
yes
stop-writes-on-bgsave-error
yes
daemonize
yes
rdbcompression
yes
rdbchecksum
yes
activerehashing
yes
activedefrag
no
protected-mode
no
repl-disable-tcp-nodelay
no
repl-diskless-sync
no
aof-rewrite-incremental-fsync
yes
rdb-save-incremental-fsync
yes
aof-load-truncated
yes
aof-use-rdb-preamble
yes
lazyfree-lazy-eviction
no
lazyfree-lazy-expire
no
lazyfree-lazy-server-del
no
slave-lazy-flush
no
replica-lazy-flush
no
dynamic-hz
yes
maxmemory-policy
noeviction
loglevel
notice
supervised
systemd
appendfsync
everysec
syslog-facility
local0
appendonly
no
dir
/var/lib/redis
save
900 1 300 10 60 10000
client-output-buffer-limit
normal 0 0 0 slave 268435456 67108864 60 pubsub 33554432 8388608 60
unixsocketperm
0
slaveof

notify-keyspace-events

bind
0.0.0.0 ::1


```
```bash
redis-cli -p 6379 -h 10.129.58.88 CLIENT LIST
```

[/home/kali/Vault/Target/10.129.58.88/result/autorecon/10.129.58.88/scans/tcp6379/tcp_6379_redis_client-list.txt](file:///home/kali/Vault/Target/10.129.58.88/result/autorecon/10.129.58.88/scans/tcp6379/tcp_6379_redis_client-list.txt):

```
id=9 addr=10.10.16.40:54250 fd=8 name= age=1 idle=1 flags=N db=0 sub=0 psub=0 multi=-1 qbuf=0 qbuf-free=0 obl=0 oll=0 omem=0 events=r cmd=NULL
id=11 addr=10.10.16.40:54260 fd=9 name= age=0 idle=0 flags=N db=0 sub=0 psub=0 multi=-1 qbuf=26 qbuf-free=32742 obl=0 oll=0 omem=0 events=r cmd=client


```
