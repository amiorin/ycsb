## Intro
* The goal of this benchmark is to measure the random reads of these three DBs
* Redis is used as a reference, no other db can go faster than Redis
* Download HBase 1.2.2, Accumulo 1.8, and Redis 3.2.3, Hadoop 2.7.3 and Zookeeper 3.4.9
* Don't use HDFS, but file:///your/home/path/data
* Accumulo needs zookeeper and hadoop jars, but you can use zookeeper of HBase standalone
* YCSB runs on the same machine of the DB
* Don't use splits
* Compact before starting the random reads
* Build native extensions for Accumulo

## Urls
|||
---|---
http://localhost:9995 | Accumulo
http://localhost:16010 | HBase

## Setup
```sh
export APACHE=~/apache
export PATH=$APACHE/accumulo/bin:$APACHE/hbase/bin:$PATH
```

## HBase
```sh
bin/hbase shell
create 'usertable', 'family'

./bin/ycsb load hbase10 -s -P workloads/workloadc \
     -threads 4 \
     -cp /Users/amiorin/apache/hbase/conf \
     -p table=usertable \
     -p columnfamily=family

./bin/ycsb run hbase10 -s -P workloads/workloadc \
     -threads 4 \
     -cp /Users/amiorin/apache/hbase/conf \
     -p table=usertable \
     -p columnfamily=family
```

## HBase async
```sh
./bin/ycsb load asynchbase -s -P workloads/workloadc \
     -p columnfamily=family \
     -p table=usertable \
     -threads 4

./bin/ycsb run asynchbase -s -P workloads/workloadc \
     -p columnfamily=family \
     -p table=usertable \
     -threads 4
```

## Accumulo
```sh
accumulo init --instance-name accumulo --password foobar
start-all.sh
accumulo shell -u root -p foobar
createtable usertable
config -t usertable -s table.cache.block.enable=true
config -t usertable -s table.bloom.enabled=true

./bin/ycsb load accumulo -s -P workloads/workloadc \
     -p accumulo.zooKeepers=localhost:2181 \
     -p accumulo.columnFamily=ycsb \
     -p accumulo.instanceName=accumulo \
     -p accumulo.username=root \
     -p accumulo.password=foobar \
     -threads 4

./bin/ycsb run accumulo -s -P workloads/workloadc \
     -p accumulo.zooKeepers=localhost:2181 \
     -p accumulo.columnFamily=ycsb \
     -p accumulo.instanceName=accumulo \
     -p accumulo.username=root \
     -p accumulo.password=foobar \
     -threads 4
```

## Redis
```sh
./bin/ycsb load redis -s -P workloads/workloadc \
     -p "redis.host=127.0.0.1" \
     -p "redis.port=6380" \
     -threads 4

./bin/ycsb run redis -s -P workloads/workloadc \
     -p "redis.host=127.0.0.1" \
     -p "redis.port=6379" \
     -threads 4
```

## Results

* Accumulo with 8 threads

```
[OVERALL], RunTime(ms), 421495.0
[OVERALL], Throughput(ops/sec), 2372.507384429234
[TOTAL_GCS_PS_Scavenge], Count, 748.0
[TOTAL_GC_TIME_PS_Scavenge], Time(ms), 2346.0
[TOTAL_GC_TIME_%_PS_Scavenge], Time(%), 0.5565902323870984
[TOTAL_GCS_PS_MarkSweep], Count, 0.0
[TOTAL_GC_TIME_PS_MarkSweep], Time(ms), 0.0
[TOTAL_GC_TIME_%_PS_MarkSweep], Time(%), 0.0
[TOTAL_GCs], Count, 748.0
[TOTAL_GC_TIME], Time(ms), 2346.0
[TOTAL_GC_TIME_%], Time(%), 0.5565902323870984
[READ], Operations, 1000000.0
[READ], AverageLatency(us), 3353.770635
[READ], MinLatency(us), 685.0
[READ], MaxLatency(us), 279295.0
[READ], 95thPercentileLatency(us), 6111.0
[READ], 99thPercentileLatency(us), 15311.0
[READ], Return=OK, 1000000
[CLEANUP], Operations, 8.0
[CLEANUP], AverageLatency(us), 2649.625
[CLEANUP], MinLatency(us), 112.0
[CLEANUP], MaxLatency(us), 17695.0
[CLEANUP], 95thPercentileLatency(us), 17695.0
[CLEANUP], 99thPercentileLatency(us), 17695.0
```

* HBase with 8 threads

```
[OVERALL], RunTime(ms), 113882.0
[OVERALL], Throughput(ops/sec), 8781.018949438892
[TOTAL_GCS_PS_Scavenge], Count, 404.0
[TOTAL_GC_TIME_PS_Scavenge], Time(ms), 615.0
[TOTAL_GC_TIME_%_PS_Scavenge], Time(%), 0.5400326653904919
[TOTAL_GCS_PS_MarkSweep], Count, 0.0
[TOTAL_GC_TIME_PS_MarkSweep], Time(ms), 0.0
[TOTAL_GC_TIME_%_PS_MarkSweep], Time(%), 0.0
[TOTAL_GCs], Count, 404.0
[TOTAL_GC_TIME], Time(ms), 615.0
[TOTAL_GC_TIME_%], Time(%), 0.5400326653904919
[READ], Operations, 1000000.0
[READ], AverageLatency(us), 898.501848
[READ], MinLatency(us), 130.0
[READ], MaxLatency(us), 651775.0
[READ], 95thPercentileLatency(us), 1610.0
[READ], 99thPercentileLatency(us), 3405.0
[READ], Return=OK, 1000000
[CLEANUP], Operations, 16.0
[CLEANUP], AverageLatency(us), 6574.5
[CLEANUP], MinLatency(us), 2.0
[CLEANUP], MaxLatency(us), 104639.0
[CLEANUP], 95thPercentileLatency(us), 363.0
[CLEANUP], 99thPercentileLatency(us), 104639.0
```

* asynchbase 4 threads

```
[OVERALL], RunTime(ms), 104397.0
[OVERALL], Throughput(ops/sec), 9578.819314731267
[TOTAL_GCS_PS_Scavenge], Count, 306.0
[TOTAL_GC_TIME_PS_Scavenge], Time(ms), 214.0
[TOTAL_GC_TIME_%_PS_Scavenge], Time(%), 0.20498673333524908
[TOTAL_GCS_PS_MarkSweep], Count, 0.0
[TOTAL_GC_TIME_PS_MarkSweep], Time(ms), 0.0
[TOTAL_GC_TIME_%_PS_MarkSweep], Time(%), 0.0
[TOTAL_GCs], Count, 306.0
[TOTAL_GC_TIME], Time(ms), 214.0
[TOTAL_GC_TIME_%], Time(%), 0.20498673333524908
[READ], Operations, 1000000.0
[READ], AverageLatency(us), 410.166084
[READ], MinLatency(us), 109.0
[READ], MaxLatency(us), 97023.0
[READ], 95thPercentileLatency(us), 678.0
[READ], 99thPercentileLatency(us), 1257.0
[READ], Return=OK, 1000000
[CLEANUP], Operations, 4.0
[CLEANUP], AverageLatency(us), 1240.0
[CLEANUP], MinLatency(us), 1.0
[CLEANUP], MaxLatency(us), 4951.0
[CLEANUP], 95thPercentileLatency(us), 4951.0
[CLEANUP], 99thPercentileLatency(us), 4951.0
```
