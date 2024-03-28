[![Gitter chat](https://badges.gitter.im/gitterHQ/gitter.png)](https://gitter.im/big-data-europe/Lobby)

# Changes

Version 2.0.0 introduces uses wait_for_it script for the cluster startup

# Hadoop Docker

## Supported Hadoop Versions
See repository branches for supported hadoop versions

## Quick Start

To deploy an example HDFS cluster, run:
```
  docker-compose up -d
```

Run example wordcount job:
```
  make wordcount
```

Or deploy in swarm:
```
docker stack deploy -c docker-compose-v3.yml hadoop
```

`docker-compose` creates a docker network that can be found by running `docker network list`, e.g. `dockerhadoop_default`.

Run `docker network inspect` on the network (e.g. `dockerhadoop_default`) to find the IP the hadoop interfaces are published on. Access these interfaces with the following URLs:

* Namenode: http://<dockerhadoop_IP_address>:9870/dfshealth.html#tab-overview
* History server: http://<dockerhadoop_IP_address>:8188/applicationhistory
* Datanode: http://<dockerhadoop_IP_address>:9864/
* Nodemanager: http://<dockerhadoop_IP_address>:8042/node
* Resource manager: http://<dockerhadoop_IP_address>:8088/

## Configure Environment Variables

The configuration parameters can be specified in the hadoop.env file or as environmental variables for specific services (e.g. namenode, datanode etc.):
```
  CORE_CONF_fs_defaultFS=hdfs://namenode:8020
```

CORE_CONF corresponds to core-site.xml. fs_defaultFS=hdfs://namenode:8020 will be transformed into:
```
  <property><name>fs.defaultFS</name><value>hdfs://namenode:8020</value></property>
```
To define dash inside a configuration parameter, use triple underscore, such as YARN_CONF_yarn_log___aggregation___enable=true (yarn-site.xml):
```
  <property><name>yarn.log-aggregation-enable</name><value>true</value></property>
```

The available configurations are:
* /etc/hadoop/core-site.xml CORE_CONF
* /etc/hadoop/hdfs-site.xml HDFS_CONF
* /etc/hadoop/yarn-site.xml YARN_CONF
* /etc/hadoop/httpfs-site.xml HTTPFS_CONF
* /etc/hadoop/kms-site.xml KMS_CONF
* /etc/hadoop/mapred-site.xml  MAPRED_CONF

If you need to extend some other configuration file, refer to base/entrypoint.sh bash script.

# UPDATE REPLICAS IN NAMENODE SERVER

### update this property dfs.replication
### name value #ofReplicas

```
echo "<configuration>
    <property><name>dfs.namenode.datanode.registration.ip-hostname-check</name><value>false</value></property>
    <property><name>dfs.webhdfs.enabled</name><value>true</value></property>
    <property><name>dfs.permissions.enabled</name><value>false</value></property>
    <property><name>dfs.namenode.name.dir</name><value>file:///hadoop/dfs/name</value></property>
    <property><name>dfs.namenode.rpc-bind-host</name><value>0.0.0.0</value></property>
    <property><name>dfs.namenode.servicerpc-bind-host</name><value>0.0.0.0</value></property>
    <property><name>dfs.namenode.http-bind-host</name><value>0.0.0.0</value></property>
    <property><name>dfs.namenode.https-bind-host</name><value>0.0.0.0</value></property>
    <property><name>dfs.client.use.datanode.hostname</name><value>true</value></property>
    <property><name>dfs.datanode.use.datanode.hostname</name><value>true</value></property>
    <property><name>dfs.namenode.datanode.registration.ip-hostname-check</name><value>false</value></property>
    <property><name>dfs.webhdfs.enabled</name><value>true</value></property>
    <property><name>dfs.permissions.enabled</name><value>false</value></property>
    <property><name>dfs.namenode.name.dir</name><value>file:///hadoop/dfs/name</value></property>
    <property><name>dfs.namenode.rpc-bind-host</name><value>0.0.0.0</value></property>
    <property><name>dfs.namenode.servicerpc-bind-host</name><value>0.0.0.0</value></property>
    <property><name>dfs.namenode.http-bind-host</name><value>0.0.0.0</value></property>
    <property><name>dfs.namenode.https-bind-host</name><value>0.0.0.0</value></property>
    <property><name>dfs.client.use.datanode.hostname</name><value>true</value></property>
    <property><name>dfs.datanode.use.datanode.hostname</name><value>true</value></property>
    <property><name>dfs.replication</name><value>5</value></property>
</configuration>" > $HADOOP_HOME/etc/hadoop/hdfs-site.xml
```
