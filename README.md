# 6_5_net
## Задача 1
Поднял из образа elasticsearch 7 версии с доработанным `elasticsearch.yml`  
+ создание папок для хранения данных path в /var/lib/[logs, data]
+ имя ноды  -- netology_test
+ создание папки /usr/share/elasticsearch/snapshots для snapshots

Dockerfile:
```
FROM elasticsearch:7.17.6
LABEL version=es7_conf
MAINTAINER pashi
ADD elasticsearch.yml /usr/share/elasticsearch/config/
RUN mkdir /var/lib/logs \
    && chown elasticsearch:elasticsearch /var/lib/logs \
    && mkdir /var/lib/data \
    && chown elasticsearch:elasticsearch /var/lib/data \
    && chown -R elasticsearch:elasticsearch /usr/share/elasticsearch/
RUN mkdir /usr/share/elasticsearch/snapshots &&\
    chown elasticsearch:elasticsearch /usr/share/elasticsearch/snapshots
EXPOSE 9200
CMD ["/bin/bash"]
USER elasticsearch
CMD ["/usr/share/elasticsearch/bin/elasticsearch"]
```

elasticsearch.yml:
```
# ======================== Elasticsearch Configuration =========================
#
# NOTE: Elasticsearch comes with reasonable defaults for most settings.
#       Before you set out to tweak and tune the configuration, make sure you
#       understand what are you trying to accomplish and the consequences.
#
# The primary way of configuring a node is via this file. This template lists
# the most important settings you may want to configure for a production cluster.
#
# Please consult the documentation for further information on configuration options:
# https://www.elastic.co/guide/en/elasticsearch/reference/index.html
#
# ---------------------------------- Cluster -----------------------------------
#
# Use a descriptive name for your cluster:
#
cluster.name: netology_test
discovery.type: single-node
#
# ------------------------------------ Node ------------------------------------
#
# Use a descriptive name for the node:
#
#node.name: node-1
#
# Add custom attributes to the node:
#
#node.attr.rack: r1
#
# ----------------------------------- Paths ------------------------------------
#
# Path to directory where to store the data (separate multiple locations by comma):
#
path.data: /var/lib/data
#
# Path to log files:
#
path.logs: /var/lib/logs

#Settings REPOSITORY PATH
#for Image from YUM (esp)
#path.repo: /usr/share/elasticsearch/snapshots
#for Image from TAR (est)
path.repo: /usr/share/elasticsearch/snapshots
#
# ----------------------------------- Memory -----------------------------------
#
# Lock the memory on startup:
#
#bootstrap.memory_lock: true
#
# Make sure that the heap size is set to about half the memory available
# on the system and that the owner of the process is allowed to use this
# limit.
#
# Elasticsearch performs poorly when the system is swapping the memory.
#
# ---------------------------------- Network -----------------------------------
#
# Set the bind address to a specific IP (IPv4 or IPv6):
#
network.host: 0.0.0.0
#
# Set a custom port for HTTP:
#
#http.port: 9200
#
# For more information, consult the network module documentation.
#
# --------------------------------- Discovery ----------------------------------
#
# Pass an initial list of hosts to perform discovery when this node is started:
# The default list of hosts is ["127.0.0.1", "[::1]"]
#
discovery.seed_hosts: ["127.0.0.1", "[::1]"]
#
# Bootstrap the cluster using an initial set of master-eligible nodes:
#
#cluster.initial_master_nodes: ["node-1", "node-2"]
#
# For more information, consult the discovery and cluster formation module documentation.
#
# ---------------------------------- Gateway -----------------------------------
#
# Block initial recovery after a full cluster restart until N nodes are started:
#
#gateway.recover_after_nodes: 3
#
# For more information, consult the gateway module documentation.
#
# ---------------------------------- Various -----------------------------------
#
# Require explicit names when deleting indices:
#
#action.destructive_requires_name: true
```

Запускал на слабом компе, пришлось ограничить потребление оперативной памяти: `sudo docker run -d -m 512m --name elastic_conf --net elastic -p 9200:9200 -p 9300:9300 -t es_conf`  
Запушил образ в `https://hub.docker.com/repository/docker/pashicop/elastic_search/general`  
Вывод `/`:  
![prt](https://user-images.githubusercontent.com/97126500/187303254-5c88ef06-a9e6-4846-bf53-fe927c9626be.png)

## Задача 2

Создание индексов по таблице:
```
curl -X PUT localhost:9200/ind-1 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 1,  "number_of_replicas": 0 }}'
curl -X PUT localhost:9200/ind-2 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 2,  "number_of_replicas": 1 }}'
curl -X PUT localhost:9200/ind-3 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 4,  "number_of_replicas": 2 }}'  
```
Список индексов и их статусов:  
![prt2](https://user-images.githubusercontent.com/97126500/187305011-d3592d77-2b6c-47c0-b5ea-c404d629815a.png)
![prt3](https://user-images.githubusercontent.com/97126500/187305364-cae46def-4b65-45a8-947f-9f2a53143eac.png)

Состояние кластера:  
![prt4](https://user-images.githubusercontent.com/97126500/187305686-c68974cb-c8f3-4d7a-b7e0-4d655509123c.png)


