# Домашнее задание к занятию 5. «Elasticsearch»

## Задача 1

В этом задании вы потренируетесь в:

- установке Elasticsearch,
- первоначальном конфигурировании Elasticsearch,
- запуске Elasticsearch в Docker.

Используя Docker-образ [centos:7](https://hub.docker.com/_/centos) как базовый и 
[документацию по установке и запуску Elastcisearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/targz.html):

- составьте Dockerfile-манифест для Elasticsearch,
- соберите Docker-образ и сделайте `push` в ваш docker.io-репозиторий,
- запустите контейнер из получившегося образа и выполните запрос пути `/` c хост-машины.

Требования к `elasticsearch.yml`:

- данные `path` должны сохраняться в `/var/lib`,
- имя ноды должно быть `netology_test`.

В ответе приведите:

- текст Dockerfile-манифеста,
- ссылку на образ в репозитории dockerhub,
- ответ `Elasticsearch` на запрос пути `/` в json-виде.

Подсказки:

- возможно, вам понадобится установка пакета perl-Digest-SHA для корректной работы пакета shasum,
- при сетевых проблемах внимательно изучите кластерные и сетевые настройки в elasticsearch.yml,
- при некоторых проблемах вам поможет Docker-директива ulimit,
- Elasticsearch в логах обычно описывает проблему и пути её решения.

Далее мы будем работать с этим экземпляром Elasticsearch.

## Ответ 1

### 1) Dockerfile-манифест
```
FROM ubuntu:latest

RUN apt update && apt install -y wget gpg ca-certificates

RUN wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg \
    && echo "deb [trusted=yes signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] http://mirror.yandex.ru/mirrors/elastic/7/ stable main" | tee /etc/apt/sources.list.d/elastic-7.x.list

RUN apt update && apt install -y elasticsearch

COPY elasticsearch.yml /etc/elasticsearch/
RUN mkdir -p /var/lib/elasticsearch
RUN chown -R elasticsearch:elasticsearch /etc/elasticsearch /var/lib/elasticsearch

USER elasticsearch
EXPOSE 9200 9300

CMD ["/usr/share/elasticsearch/bin/elasticsearch"]
```

### 2) ссылка на образ в репозитории dockerhub

[Dockerhub](https://hub.docker.com/repository/docker/shatskiyo/docker-elasticsearch/general)

### 3) ответ `Elasticsearch` на запрос пути `/` в json-виде.

```
{
  "name" : "netology_test",
  "cluster_name" : "my-elasticsearch-cluster",
  "cluster_uuid" : "91LzLYeARICQoEKCtUuq1A",
  "version" : {
    "number" : "7.17.9",
    "build_flavor" : "default",
    "build_type" : "deb",
    "build_hash" : "ef48222227ee6b9e70e502f0f0daa52435ee634d",
    "build_date" : "2023-01-31T05:34:43.305517834Z",
    "build_snapshot" : false,
    "lucene_version" : "8.11.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

![alt text](https://github.com/shatskiy-O/virt-homeworks/blob/virt-11/06-db-05-elasticsearch/images/1.png)

## Задача 2

В этом задании вы научитесь:

- создавать и удалять индексы,
- изучать состояние кластера,
- обосновывать причину деградации доступности данных.

Ознакомьтесь с [документацией](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-create-index.html) 
и добавьте в `Elasticsearch` 3 индекса в соответствии с таблицей:

| Имя | Количество реплик | Количество шард |
|-----|-------------------|-----------------|
| ind-1| 0 | 1 |
| ind-2 | 1 | 2 |
| ind-3 | 2 | 4 |

Получите список индексов и их статусов, используя API, и **приведите в ответе** на задание.

Получите состояние кластера `Elasticsearch`, используя API.

Как вы думаете, почему часть индексов и кластер находятся в состоянии yellow?

Удалите все индексы.

**Важно**

При проектировании кластера Elasticsearch нужно корректно рассчитывать количество реплик и шард,
иначе возможна потеря данных индексов, вплоть до полной, при деградации системы.

## Ответ 2

![alt text](https://github.com/shatskiy-O/virt-homeworks/blob/virt-11/06-db-05-elasticsearch/images/2.png)

Состояние yellow для кластера и некоторых индексов обусловлено тем, что не все реплики шардов были назначены. Поскольку в нашей конфигурации только один узел данных, реплики шардов для ind-2 и ind-3 не могут быть распределены, так как для обеспечения отказоустойчивости реплики должны находиться на разных узлах.

Наша система работает корректно, но статус yellow указывает на потенциальные проблемы с доступностью данных: если узел выйдет из строя, данные в репликах будут недоступны.

## Задача 3

В этом задании вы научитесь:

- создавать бэкапы данных,
- восстанавливать индексы из бэкапов.

Создайте директорию `{путь до корневой директории с Elasticsearch в образе}/snapshots`.

Используя API, [зарегистрируйте](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-register-repository.html#snapshots-register-repository) 
эту директорию как `snapshot repository` c именем `netology_backup`.

**Приведите в ответе** запрос API и результат вызова API для создания репозитория.

Создайте индекс `test` с 0 реплик и 1 шардом и **приведите в ответе** список индексов.

[Создайте `snapshot`](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-take-snapshot.html) 
состояния кластера `Elasticsearch`.

**Приведите в ответе** список файлов в директории со `snapshot`.

Удалите индекс `test` и создайте индекс `test-2`. **Приведите в ответе** список индексов.

[Восстановите](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-restore-snapshot.html) состояние
кластера `Elasticsearch` из `snapshot`, созданного ранее. 

**Приведите в ответе** запрос к API восстановления и итоговый список индексов.

Подсказки:

- возможно, вам понадобится доработать `elasticsearch.yml` в части директивы `path.repo` и перезапустить `Elasticsearch`.

---

## Ответ 3


### Создание Репозитория для Снимков

```
curl -X PUT "localhost:9200/_snapshot/netology_backup" -H 'Content-Type: application/json' -d'
{
  "type": "fs",
  "settings": {
    "location": "/usr/share/elasticsearch/snapshots"
  }
}
'
```
```
{"acknowledged":true}
```
![alt text](https://github.com/shatskiy-O/virt-homeworks/blob/virt-11/06-db-05-elasticsearch/images/3.png)

### Создание Индекса test

```
curl -X PUT "localhost:9200/test" -H 'Content-Type: application/json' -d'
{
  "settings" : {
      "index" : {
          "number_of_shards" : 1, 
          "number_of_replicas" : 0
      }
  }
}
'
```
```
health status index            uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   .geoip_databases HaW1gTpPSfSPJpQb5XUg7w   1   0         41            0     38.9mb         38.9mb
green  open   test             kpe3p75KT5epMHCyfGiD3A   1   0          0            0       226b           226b
```
![alt text](https://github.com/shatskiy-O/virt-homeworks/blob/virt-11/06-db-05-elasticsearch/images/4.png)

### Создание Snapshot состояния кластера Elasticsearch

```
curl -X PUT "localhost:9200/_snapshot/netology_backup/snapshot_1?wait_for_completion=true"
```
### Удаление Индекса test и Создание Индекса test-2 и восстановление состояния кластера из snapshot.

```
curl -X POST "localhost:9200/_snapshot/netology_backup/snapshot_1/_restore" -H 'Content-Type: application/json' -d'
{
  "ignore_unavailable": true,
  "include_global_state": false,
  "rename_pattern": "(.+)",
  "rename_replacement": "restored_$1"
}
'
```

![alt text](https://github.com/shatskiy-O/virt-homeworks/blob/virt-11/06-db-05-elasticsearch/images/5.png)

### Как cдавать задание

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
