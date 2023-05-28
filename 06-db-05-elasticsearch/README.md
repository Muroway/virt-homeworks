# Домашнее задание к занятию "6.5. Elasticsearch"

## Задача 1

В этом задании вы потренируетесь в:
- установке elasticsearch
- первоначальном конфигурировании elastcisearch
- запуске elasticsearch в docker

Используя докер образ [centos:7](https://hub.docker.com/_/centos) как базовый и 
[документацию по установке и запуску Elastcisearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/targz.html):

- составьте Dockerfile-манифест для elasticsearch
- соберите docker-образ и сделайте `push` в ваш docker.io репозиторий
- запустите контейнер из получившегося образа и выполните запрос пути `/` c хост-машины

Требования к `elasticsearch.yml`:
- данные `path` должны сохраняться в `/var/lib`
- имя ноды должно быть `netology_test`

В ответе приведите:
- текст Dockerfile манифеста
```
cat Dockerfile
FROM centos:7

ENV ES_HOME=/var/lib/elasticsearch

ADD /elasticsearch-oss-7.10.2-x86_64.rpm /elasticsearch-oss-7.10.2-x86_64.rpm

RUN  yum install -y elasticsearch-oss-7.10.2-x86_64.rpm

RUN yum update -y

RUN echo "node.name: netology_test" >> /etc/elasticsearch/elasticsearch.yml;\
    echo "network.host: 0.0.0.0" >> /etc/elasticsearch/elasticsearch.yml;\
    echo "discovery.type: single-node" >> /etc/elasticsearch/elasticsearch.yml;\
    echo "path.repo: /var/lib/elasticsearch" >> /etc/elasticsearch/elasticsearch.yml;

VOLUME [ "/var/lib/elasticsearch" ]

EXPOSE 9200

WORKDIR /usr/share/elasticsearch

USER elasticsearch

CMD ["./bin/systemd-entrypoint"]
```
- ссылку на образ в репозитории dockerhub

[Ссылка на образ в docker.io](https://hub.docker.com/repository/docker/muroway/elastic_netology_ru/general)

- ответ `elasticsearch` на запрос пути `/` в json виде

```json
{
  "name" : "netology_test",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "el_9eaanSemZ5nAKy4HU1A",
  "version" : {
    "number" : "7.10.2",
    "build_flavor" : "oss",
    "build_type" : "rpm",
    "build_hash" : "747e1cc71def077253878a59143c1f785afa92b9",
    "build_date" : "2021-01-13T00:42:12.435326Z",
    "build_snapshot" : false,
    "lucene_version" : "8.7.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```


Подсказки:
- возможно вам понадобится установка пакета perl-Digest-SHA для корректной работы пакета shasum
- при сетевых проблемах внимательно изучите кластерные и сетевые настройки в elasticsearch.yml
- при некоторых проблемах вам поможет docker директива ulimit
- elasticsearch в логах обычно описывает проблему и пути ее решения

Далее мы будем работать с данным экземпляром elasticsearch.

## Задача 2

В этом задании вы научитесь:
- создавать и удалять индексы
- изучать состояние кластера
- обосновывать причину деградации доступности данных

Ознакомтесь с [документацией](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-create-index.html) 
и добавьте в `elasticsearch` 3 индекса, в соответствии со таблицей:

| Имя | Количество реплик | Количество шард |
|-----|-------------------|-----------------|
| ind-1| 0 | 1 |
| ind-2 | 1 | 2 |
| ind-3 | 2 | 4 |

```bash
curl -X PUT "localhost:9200/ind-1?pretty" -H 'Content-Type: application/json' -d'
{
  "settings": {
    "index": {
        "number_of_shards": 1,
        "number_of_replicas": 0  
    }
  }
}
'

curl -X PUT "localhost:9200/ind-2?pretty" -H 'Content-Type: application/json' -d'
{
  "settings": {
    "index": {
      "number_of_shards": 2,  
      "number_of_replicas": 1
    }
  }
}
'

curl -X PUT "localhost:9200/ind-3?pretty" -H 'Content-Type: application/json' -d'
{
  "settings": {
    "index": {
      "number_of_shards": 4,  
      "number_of_replicas": 2
    }
  }
}
'

```


Получите список индексов и их статусов, используя API и **приведите в ответе** на задание.

```bash
curl "localhost:9200/_cat/indices"
green  open ind-1 4FAOU39gS3qlZMbNMV2nbA 1 0 0 0 208b 208b
yellow open ind-3 8dw69TdhQSGwyNmSfeLFUw 4 2 0 0 832b 832b
yellow open ind-2 hiktf1uqSYKNzRefg7QA8w 2 1 0 0 416b 416b
```

Получите состояние кластера `elasticsearch`, используя API.

```bash
curl "localhost:9200/_cluster/health?pretty"
{
  "cluster_name" : "elasticsearch",
  "status" : "yellow",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 7,
  "active_shards" : 7,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 10,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 41.17647058823529
}
```

Как вы думаете, почему часть индексов и кластер находится в состоянии yellow?

Потому что ElasticSearch является отказоустойчивой системой хранения данных, которая расчитана для запуска не на одном сервере, а на группе (кластере) из нескольких связанных серверов (узлов, “nodes”). В нашем случает используется одна всего нода.


Удалите все индексы.


```bash
curl -X DELETE "localhost:9200/ind*?pretty"

```

Состояние после удаления индексов

```bash
vagrant@vagrant:~$ curl "localhost:9200/_cluster/health?pretty"
{
  "cluster_name" : "elasticsearch",
  "status" : "green",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 0,
  "active_shards" : 0,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 0,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 100.0
}
```

**Важно**

При проектировании кластера elasticsearch нужно корректно рассчитывать количество реплик и шард,
иначе возможна потеря данных индексов, вплоть до полной, при деградации системы.

## Задача 3
В этом задании вы научитесь:

- создавать бэкапы данных,
- восстанавливать индексы из бэкапов.
- Создайте директорию {путь до корневой директории с Elasticsearch в образе}/snapshots.

Используя API, зарегистрируйте эту директорию как snapshot repository c именем netology_backup.

Приведите в ответе запрос API и результат вызова API для создания репозитория.

```bash
curl -X PUT "localhost:9200/_snapshot/netology_backup?pretty" -H 'Content-Type: application/json' -d'
> {
>   "type": "fs",
>   "settings": {
>     "location": "/var/lib/elasticsearch/backup"
>   }
> }
> '
{
  "acknowledged" : true
}
```

Создайте индекс test с 0 реплик и 1 шардом и приведите в ответе список индексов.

```bash
curl -X PUT "localhost:9200/test?pretty" -H 'Content-Type: application/json' -d'
{
  "settings": {
    "index": {
      "number_of_shards": 1,  
      "number_of_replicas": 0
    }
  }
}
'

curl "localhost:9200/_cat/indices"
green open test G3jhHw0tRs20uPKRuf7Pbg 1 0 0 0 208b 208b

```

Создайте snapshot состояния кластера Elasticsearch.
```bash
curl -X PUT "localhost:9200/_snapshot/netology_backup/my_snapshot1?pretty"
```
***NOTE***

Статус снепшота

```bash
curl -X GET "localhost:9200/_snapshot/netology_backup/my_snapshot1/_status?pretty"
{
  "snapshots" : [
    {
      "snapshot" : "my_snapshot1",
      "repository" : "netology_backup",
      "uuid" : "FIUqujLkTTmZyvo-3dXV3Q",
      "state" : "SUCCESS",
      "include_global_state" : true,
      "shards_stats" : {
        "initializing" : 0,
        "started" : 0,
        "finalizing" : 0,
        "done" : 1,
        "failed" : 0,
        "total" : 1
      },
      "stats" : {
        "incremental" : {
          "file_count" : 1,
          "size_in_bytes" : 208
        },
        "total" : {
          "file_count" : 1,
          "size_in_bytes" : 208
        },
        "start_time_in_millis" : 1685296851988,
        "time_in_millis" : 214
      },
      "indices" : {
        "test" : {
          "shards_stats" : {
            "initializing" : 0,
            "started" : 0,
            "finalizing" : 0,
            "done" : 1,
            "failed" : 0,
            "total" : 1
          },
          "stats" : {
            "incremental" : {
              "file_count" : 1,
              "size_in_bytes" : 208
            },
            "total" : {
              "file_count" : 1,
              "size_in_bytes" : 208
            },
            "start_time_in_millis" : 1685296851988,
            "time_in_millis" : 214
          },
          "shards" : {
            "0" : {
              "stage" : "DONE",
              "stats" : {
                "incremental" : {
                  "file_count" : 1,
                  "size_in_bytes" : 208
                },
                "total" : {
                  "file_count" : 1,
                  "size_in_bytes" : 208
                },
                "start_time_in_millis" : 1685296851988,
                "time_in_millis" : 214
              }
            }
          }
        }
      }
    }
  ]
}
```

Приведите в ответе список файлов в директории со snapshot.

```bash
docker exec -it 41270e92c4ea bash
bash-4.2$ ls /var/lib/elasticsearch/backup/
index-0  index.latest  indices	meta-FIUqujLkTTmZyvo-3dXV3Q.dat  snap-FIUqujLkTTmZyvo-3dXV3Q.dat
```

***NOTE***
Удаление снепшота
```bash
curl -X DELETE "localhost:9200/_snapshot/netology_backup/my_snapshot2?pretty"
```

Удалите индекс test и создайте индекс test-2. Приведите в ответе список индексов.
```bash
curl -X DELETE "localhost:9200/test?pretty"

curl -X PUT "localhost:9200/test-2?pretty" -H 'Content-Type: application/json' -d'
> {
>   "settings": {
>     "index": {
>       "number_of_shards": 1,
>       "number_of_replicas": 0
>     }
>   }
> }
> '
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "test-2"
}
```


Восстановите состояние кластера Elasticsearch из snapshot, созданного ранее.
```bash
curl -X POST "localhost:9200/_snapshot/netology_backup/my_snapshot1/_restore?pretty"
{
  "accepted" : true
}
```


Приведите в ответе запрос к API восстановления и итоговый список индексов.
```bash
curl "localhost:9200/_cat/indices"
green open test-2 uYpeZVN4TxOGth_0BG4WGA 1 0 0 0 208b 208b
green open test   o-3xJ0BiSuaXDWEjzYZkeg 1 0 0 0 208b 208b
```


Подсказки:

возможно, вам понадобится доработать elasticsearch.yml в части директивы path.repo и перезапустить Elasticsearch.
Как cдавать задание
Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.