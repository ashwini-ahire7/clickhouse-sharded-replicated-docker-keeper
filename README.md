# clickhouse-sharded-replicated-docker-clickhouse-keeper
docker-compose for creating sharded ClickHouse cluster with ClickHouse keeper for supporting distrubuted tables and replication


Note : 
If you want to run specific version of clickhouse then just update image for specific version in docker-compose.yaml file. 

Example : image: clickhouse/clickhouse-server:23.5.2.7-alpine. 
Update for all ClickHouse Nodes in Cluster. 

Visit docker hub to get correct image tag : https://hub.docker.com/r/clickhouse/clickhouse-server/tags


```
root@ip-10-0-6-24:~/release_check# git clone https://github.com/ashwini-ahire7/clickhouse-sharded-replicated-docker-keeper.git
Cloning into 'clickhouse-sharded-replicated-docker-keeper'...
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), done.
root@ip-10-0-6-24:~/release_check/clickhouse-sharded-replicated-docker-keeper#

root@ip-10-0-6-24:~/release_check/clickhouse-sharded-replicated-docker-keeper# ls -lhtr
total 116K
-rw-r--r-- 1 root root  73K Jun 11 11:55 config.xml
-rw-r--r-- 1 root root  389 Jun 11 11:55 README.md
-rw-r--r-- 1 root root  442 Jun 12 05:45 clusters.xml
-rw-r--r-- 1 root root  366 Jun 12 05:59 zookeeper.xml
-rw-r--r-- 1 root root  153 Jun 12 06:02 macros_2.xml
-rw-r--r-- 1 root root  153 Jun 12 06:03 macros_1.xml
-rw-r--r-- 1 root root   66 Jun 12 06:05 listen_host.xml
-rw-r--r-- 1 root root 1.2K Jun 12 06:09 keeper_1.xml
-rw-r--r-- 1 root root 1.2K Jun 12 06:09 keeper_2.xml
-rw-r--r-- 1 root root 1.2K Jun 12 06:12 keeper_3.xml
-rw-r--r-- 1 root root 2.9K Jun 12 06:31 docker-compose.yaml
root@ip-10-0-6-24:~/release_check/clickhouse-sharded-replicated-docker-keeper#
root@ip-10-0-6-24:~/release_check/clickhouse-sharded-replicated-docker-keeper# docker-compose up -d
Creating network "clickhouse-sharded-replicated-docker-keeper_my-network" with driver "bridge"
Creating clickhouse03 ... done
Creating clickhouse02 ... done
Creating clickhouse01 ... done
root@ip-10-0-6-24:~/release_check/clickhouse-sharded-replicated-docker-keeper#

oot@ip-10-0-6-24:~/release_check/clickhouse-sharded-replicated-docker# docker ps -a
CONTAINER ID   IMAGE                                          COMMAND            CREATED          STATUS          PORTS                                                                                                                                                                                  NAMES
13f794975992   clickhouse/clickhouse-server:23.5.2.7-alpine   "/entrypoint.sh"   29 seconds ago   Up 27 seconds   9009/tcp, 0.0.0.0:2183->2181/tcp, :::2183->2181/tcp, 0.0.0.0:8125->8123/tcp, :::8125->8123/tcp, 0.0.0.0:9002->9000/tcp, :::9002->9000/tcp, 0.0.0.0:9446->9444/tcp, :::9446->9444/tcp   clickhouse02
2abb7e241d7a   clickhouse/clickhouse-server:23.5.2.7-alpine   "/entrypoint.sh"   29 seconds ago   Up 27 seconds   9009/tcp, 0.0.0.0:2184->2181/tcp, :::2184->2181/tcp, 0.0.0.0:8126->8123/tcp, :::8126->8123/tcp, 0.0.0.0:9003->9000/tcp, :::9003->9000/tcp, 0.0.0.0:9447->9444/tcp, :::9447->9444/tcp   clickhouse03
bd658a9e1c73   clickhouse/clickhouse-server:23.5.2.7-alpine   "/entrypoint.sh"   29 seconds ago   Up 27 seconds   9009/tcp, 0.0.0.0:2182->2181/tcp, :::2182->2181/tcp, 0.0.0.0:8124->8123/tcp, :::8124->8123/tcp, 0.0.0.0:9001->9000/tcp, :::9001->9000/tcp, 0.0.0.0:9445->9444/tcp, :::9445->9444/tcp   clickhouse01


clickhouse01:/# clickhouse-client
ClickHouse client version 23.5.2.7 (official build).
Connecting to localhost:9000 as user default.
Connected to ClickHouse server version 23.5.2 revision 54462.

Warnings:
 * Linux is not using a fast clock source. Performance can be degraded. Check /sys/devices/system/clocksource/clocksource0/current_clocksource
 * Linux threads max count is too low. Check /proc/sys/kernel/threads-max
 * Available memory at server startup is too low (2GiB).

clickhouse01 :) show databases;

SHOW DATABASES

Query id: b71a4f66-6da6-445d-94cd-65cb9bc259ea

┌─name───────────────┐
│ INFORMATION_SCHEMA │
│ default            │
│ information_schema │
│ system             │
└────────────────────┘

4 rows in set. Elapsed: 0.005 sec.

clickhouse01 :)

clickhouse01 :) select version();

SELECT version()

Query id: 82efadd2-34cc-464f-9e7f-ac17607625e2

┌─version()─┐
│ 23.5.2.7  │
└───────────┘

1 row in set. Elapsed: 0.003 sec.

clickhouse01 :)

clickhouse01 :) SELECT *
FROM system.clusters where cluster='cluster_demo_ash';

SELECT *
FROM system.clusters
WHERE cluster = 'cluster_demo_ash'

Query id: 9b48f5c4-3ecc-4993-b52b-1a6152416c14

┌─cluster──────────┬─shard_num─┬─shard_weight─┬─replica_num─┬─host_name────┬─host_address─┬─port─┬─is_local─┬─user────┬─default_database─┬─errors_count─┬─slowdowns_count─┬─estimated_recovery_time─┬─database_shard_name─┬─database_replica_name─┬─is_active─┐
│ cluster_demo_ash │         1 │            1 │           1 │ clickhouse01 │ 172.20.0.2   │ 9000 │        1 │ default │                  │            0 │               0 │                       0 │                     │                       │      ᴺᵁᴸᴸ │
│ cluster_demo_ash │         2 │            1 │           1 │ clickhouse02 │ 172.20.0.3   │ 9000 │        0 │ default │                  │            0 │               0 │                       0 │                     │                       │      ᴺᵁᴸᴸ │
└──────────────────┴───────────┴──────────────┴─────────────┴──────────────┴──────────────┴──────┴──────────┴─────────┴──────────────────┴──────────────┴─────────────────┴─────────────────────────┴─────────────────────┴───────────────────────┴───────────┘

2 rows in set. Elapsed: 0.003 sec.

clickhouse01 :)
clickhouse01 :) CREATE DATABASE db1 ON CLUSTER 'cluster_demo_ash';

CREATE DATABASE db1 ON CLUSTER cluster_demo_ash

Query id: 70f8e375-1994-4090-a5f6-65fe8257882a

┌─host─────────┬─port─┬─status─┬─error─┬─num_hosts_remaining─┬─num_hosts_active─┐
│ clickhouse01 │ 9000 │      0 │       │                   1 │                0 │
│ clickhouse02 │ 9000 │      0 │       │                   0 │                0 │
└──────────────┴──────┴────────┴───────┴─────────────────────┴──────────────────┘

2 rows in set. Elapsed: 0.136 sec.

clickhouse01 :) CREATE TABLE db1.table1 on cluster '{cluster}' (
    `id` UInt64,
    `column1` String
)
ENGINE = MergeTree
ORDER BY column1

CREATE TABLE db1.table1 ON CLUSTER `{cluster}`
(
    `id` UInt64,
    `column1` String
)
ENGINE = MergeTree
ORDER BY column1

Query id: 1efafc4a-a29e-4ebe-a8a0-9d9b68bf7ae8

┌─host─────────┬─port─┬─status─┬─error─┬─num_hosts_remaining─┬─num_hosts_active─┐
│ clickhouse02 │ 9000 │      0 │       │                   1 │                0 │
│ clickhouse01 │ 9000 │      0 │       │                   0 │                0 │
└──────────────┴──────┴────────┴───────┴─────────────────────┴──────────────────┘

2 rows in set. Elapsed: 0.130 sec.

clickhouse01 :)
clickhouse01 :)
clickhouse01 :)  show tables from db1;

SHOW TABLES FROM db1

Query id: 919af3dd-1279-44cb-8bb6-f7b0d7954028

┌─name───┐
│ table1 │
└────────┘

1 row in set. Elapsed: 0.004 sec.

clickhouse01 :)

clickhouse01 :) INSERT INTO db1.table1
    (id, column1)
VALUES
    (1, 'abc'),
    (2, 'def')

INSERT INTO db1.table1 (id, column1) FORMAT Values

Query id: 10796617-cd63-4418-9d18-12f083f539ed

Ok.

2 rows in set. Elapsed: 0.007 sec.

clickhouse01 :) select * from db1.table1;

SELECT *
FROM db1.table1

Query id: e1ddd37c-42ac-4b8b-9de0-9524052883ad

┌─id─┬─column1─┐
│  1 │ abc     │
│  2 │ def     │
└────┴─────────┘

2 rows in set. Elapsed: 0.005 sec.

clickhouse01 :)

clickhouse01 :)  select * from db1.table1;

SELECT *
FROM db1.table1

Query id: 46da8851-fcaa-4535-b265-3674ec81bbfc

┌─id─┬─column1─┐
│  1 │ abc     │
│  2 │ def     │
└────┴─────────┘

2 rows in set. Elapsed: 0.003 sec.

clickhouse02 :) INSERT INTO db1.table1
    (id, column1)
VALUES
    (3, 'ghi'),
    (4, 'jkl')

INSERT INTO db1.table1 (id, column1) FORMAT Values

Query id: 2a6ba40f-9109-4dfb-aeda-c0e45209fdb0

Ok.

2 rows in set. Elapsed: 0.005 sec.

clickhouse02 :)  select * from db1.table1;

SELECT *
FROM db1.table1

Query id: 7013c3ee-ee94-4415-afb4-039e6248d31f

┌─id─┬─column1─┐
│  3 │ ghi     │
│  4 │ jkl     │
└────┴─────────┘

2 rows in set. Elapsed: 0.004 sec.

clickhouse01 :) CREATE TABLE db1.dist_table (
    id UInt64,
    column1 String
)
ENGINE = Distributed(cluster_demo_ash,db1,table1)

CREATE TABLE db1.dist_table
(
    `id` UInt64,
    `column1` String
)
ENGINE = Distributed(cluster_demo_ash, db1, table1)

Query id: 8c278bf0-a831-4d07-a61e-ecfa97b177b4

Ok.

0 rows in set. Elapsed: 0.007 sec.

clickhouse01 :)

clickhouse01 :) SELECT *
FROM db1.dist_table

SELECT *
FROM db1.dist_table

Query id: 47943212-c599-49a7-b91f-1f3409d88dde

┌─id─┬─column1─┐
│  1 │ abc     │
│  2 │ def     │
└────┴─────────┘
┌─id─┬─column1─┐
│  3 │ ghi     │
│  4 │ jkl     │
└────┴─────────┘

4 rows in set. Elapsed: 0.011 sec.

clickhouse01 :)


=====================================

clickhouse01 :) CREATE DATABASE db_uuid
      ON CLUSTER '{cluster}' ENGINE = Atomic

CREATE DATABASE db_uuid ON CLUSTER `{cluster}`
ENGINE = Atomic

Query id: e5309254-a0a3-40da-916a-5d49630d0f87

┌─host─────────┬─port─┬─status─┬─error─┬─num_hosts_remaining─┬─num_hosts_active─┐
│ clickhouse01 │ 9000 │      0 │       │                   1 │                0 │
│ clickhouse02 │ 9000 │      0 │       │                   0 │                0 │
└──────────────┴──────┴────────┴───────┴─────────────────────┴──────────────────┘

2 rows in set. Elapsed: 0.131 sec.

clickhouse01 :)

clickhouse02 :) CREATE TABLE db_uuid.uuid_table1 ON CLUSTER '{cluster}'    (
     id UInt64,
     column1 String
   )
   ENGINE = ReplicatedMergeTree('/clickhouse/tables/{shard}/db_uuid/{uuid}', '{replica}' )
   ORDER BY (id);

CREATE TABLE db_uuid.uuid_table1 ON CLUSTER `{cluster}`
(
    `id` UInt64,
    `column1` String
)
ENGINE = ReplicatedMergeTree('/clickhouse/tables/{shard}/db_uuid/{uuid}', '{replica}')
ORDER BY id

Query id: 9d2b7a92-ba5b-4e9d-8abd-573f4381045a

┌─host─────────┬─port─┬─status─┬─error─┬─num_hosts_remaining─┬─num_hosts_active─┐
│ clickhouse01 │ 9000 │      0 │       │                   1 │                1 │
└──────────────┴──────┴────────┴───────┴─────────────────────┴──────────────────┘
┌─host─────────┬─port─┬─status─┬─error─┬─num_hosts_remaining─┬─num_hosts_active─┐
│ clickhouse02 │ 9000 │      0 │       │                   0 │                0 │
└──────────────┴──────┴────────┴───────┴─────────────────────┴──────────────────┘

2 rows in set. Elapsed: 0.209 sec.

clickhouse02 :)

clickhouse01 :)  show tables from db_uuid;

SHOW TABLES FROM db_uuid

Query id: 87349cf4-137a-4b67-89e0-6e44a357e7f2

┌─name────────┐
│ uuid_table1 │
└─────────────┘

1 row in set. Elapsed: 0.004 sec.

clickhouse01 :)
clickhouse01 :) create table db_uuid.dist_uuid_table1 on cluster '{cluster}' (
     id UInt64,
     column1 String
   )
   ENGINE = Distributed('cluster_demo_ash','db_uuid','uuid_table1')

CREATE TABLE db_uuid.dist_uuid_table1 ON CLUSTER `{cluster}`
(
    `id` UInt64,
    `column1` String
)
ENGINE = Distributed('cluster_demo_ash', 'db_uuid', 'uuid_table1')

Query id: 7884ba1a-f3b0-45fb-8fbe-065c80cab619

┌─host─────────┬─port─┬─status─┬─error─┬─num_hosts_remaining─┬─num_hosts_active─┐
│ clickhouse02 │ 9000 │      0 │       │                   1 │                0 │
│ clickhouse01 │ 9000 │      0 │       │                   0 │                0 │
└──────────────┴──────┴────────┴───────┴─────────────────────┴──────────────────┘

2 rows in set. Elapsed: 0.129 sec.

clickhouse01 :)
clickhouse01 :)
clickhouse01 :) INSERT INTO db_uuid.uuid_table1
   ( id, column1)
   VALUES
   ( 1, 'abc');

INSERT INTO db_uuid.uuid_table1 (id, column1) FORMAT Values

Query id: 813e9fab-52c4-4c77-a22f-314c80e4fecd

Ok.

1 row in set. Elapsed: 0.019 sec.

clickhouse01 :)
clickhouse01 :) select * from db_uuid.uuid_table1;

SELECT *
FROM db_uuid.uuid_table1

Query id: 8ed0c2e6-c01e-453b-8613-9714421a59bc

┌─id─┬─column1─┐
│  1 │ abc     │
└────┴─────────┘

1 row in set. Elapsed: 0.006 sec.

clickhouse01 :)

clickhouse02 :) select * from db_uuid.uuid_table1;

SELECT *
FROM db_uuid.uuid_table1

Query id: 33de1591-26d1-4791-8f7a-54cbaa74a3c7

Ok.

0 rows in set. Elapsed: 0.007 sec.

clickhouse02 :)
clickhouse02 :) INSERT INTO db_uuid.uuid_table1
   ( id, column1)
   VALUES
   ( 2, 'def');

INSERT INTO db_uuid.uuid_table1 (id, column1) FORMAT Values

Query id: f1c6172f-5399-4fbb-9e0d-02e00ee551b0

Ok.

1 row in set. Elapsed: 0.026 sec.

clickhouse02 :) select * from db_uuid.uuid_table1;

SELECT *
FROM db_uuid.uuid_table1

Query id: 774bb38a-9aa8-4f8f-bda1-3c51131d3df2

┌─id─┬─column1─┐
│  2 │ def     │
└────┴─────────┘

1 row in set. Elapsed: 0.005 sec.

clickhouse02 :)

clickhouse01 :) SELECT * FROM db_uuid.dist_uuid_table1;



SELECT *
FROM db_uuid.dist_uuid_table1

Query id: 868fb6a4-66e3-4017-bd81-739b8a05ec6d

┌─id─┬─column1─┐
│  1 │ abc     │
└────┴─────────┘
┌─id─┬─column1─┐
│  2 │ def     │
└────┴─────────┘

2 rows in set. Elapsed: 0.011 sec.
```
