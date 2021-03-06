[![Gitter chat](https://badges.gitter.im/gitterHQ/gitter.png)](https://gitter.im/big-data-europe/Lobby)

# docker-hive

This repository is base on https://github.com/big-data-europe/docker-hive, add Chinese support and Hive 3.1.2 support.

This is a docker container for Apache Hive 2.3.2 and 3.1.2. It is based on https://github.com/big-data-europe/docker-hadoop so check there for Hadoop configurations.
This deploys Hive and starts a hiveserver2 on port 10000.
Metastore is running with a connection to postgresql database.
The hive configuration is performed with HIVE_SITE_CONF_ variables (see hadoop-hive.env for an example).

To run Hive 2.3.2 with postgresql metastore:
```
    docker-compose up -d
```

To run Hive 3.1.2 with postgresql metastore:
```
    docker build -t hive:v3.1.2 --build-arg HIVE_VERSION=3.1.2 .
    docker-compose -f docker-compose-v3.yml up -d
```

To deploy in Docker Swarm:
```
    docker stack deploy -c docker-compose.yml hive
```

To run a PrestoDB 0.181 with Hive connector:

```
  docker-compose up -d presto-coordinator
```

This deploys a Presto server listens on port `8080`

## Testing
Load data into Hive:
```
  $ docker-compose exec hive-server bash
  # /opt/hive/bin/beeline -u jdbc:hive2://localhost:10000
  > CREATE TABLE pokes (foo INT, bar STRING);
  > LOAD DATA LOCAL INPATH '/opt/hive/examples/files/kv1.txt' OVERWRITE INTO TABLE pokes;
```

Then query it from PrestoDB. You can get [presto.jar](https://prestosql.io/docs/current/installation/cli.html) from PrestoDB website:
```
  $ wget https://repo1.maven.org/maven2/io/prestosql/presto-cli/308/presto-cli-308-executable.jar
  $ mv presto-cli-308-executable.jar presto.jar
  $ chmod +x presto.jar
  $ ./presto.jar --server localhost:8080 --catalog hive --schema default
  presto> select * from pokes;
```

## Work with Flink Hive Connector
- Copy core-site.xml hdfs-site.xml hive-site.xml from container docker-hive_hive-server_1.
- Add ```${host_ip}  hive-metastore hive-metastore-postgresql namenode ${docker-hive_datanode_1_container_id}``` /etc/hosts to Flink cluster.

## Contributors
* Ivan Ermilov [@earthquakesan](https://github.com/earthquakesan) (maintainer)
* Yiannis Mouchakis [@gmouchakis](https://github.com/gmouchakis)
* Ke Zhu [@shawnzhu](https://github.com/shawnzhu)
