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
Docker манифест

</p>
<pre><code>

FROM centos:latest

'# Правки для yum
RUN sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-Linux-* &&\
	sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-Linux-*

'# Установка wget и perl-Digest-SHA
RUN yum install wget -y && \
	yum install perl-Digest-SHA -y

'# Задаем переменные
ENV ES_DIR="/opt/elasticsearch"
ENV ES_HOME="${ES_DIR}/elasticsearch-7.17.0"

'# Переход в рабочую дирректорию
WORKDIR ${ES_DIR}

'# Скачиваем и распаковывем elasticsearch
RUN wget --quiet https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.17.0-linux-x86_64.tar.gz && \
	wget --quiet https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.17.0-linux-x86_64.tar.gz.sha512 && \
	sha512sum --check --quiet elasticsearch-7.17.0-linux-x86_64.tar.gz.sha512 && \
	tar -xzf elasticsearch-7.17.0-linux-x86_64.tar.gz

'# Копируем elasticsearch.yml конфига в контейнер
COPY elasticsearch.yml ${ES_HOME}/config

'# Задаем переменную для пользователя
ENV ES_USER="elasticsearch"

'# Создаем пользователя
RUN useradd ${ES_USER}

'# Создаем дирректории в соответствии с конфигом
RUN mkdir -p "/var/lib/elasticsearch" && \
	mkdir -p "/var/log/elasticsearch"

'# Выдаем права на созданные директории пользователю elasticsearch
RUN chown -R ${ES_USER}: "${ES_DIR}" && \
	chown -R ${ES_USER}: "/var/lib/elasticsearch" && \
	chown -R ${ES_USER}: "/var/log/elasticsearch"

'# Вход по юзером elasticsearch
USER ${ES_USER}

'# Переходим в рабочую дирректорию
WORKDIR "${ES_HOME}"

'# Пробросываем порты контейнера
EXPOSE 9200
EXPOSE 9300

'# Запускаем файл при старте контейнера
ENTRYPOINT ["./bin/elasticsearch"]
</code></pre>



Конфигурация elasticsearch.yml

</p>
<pre><code>
---
discovery:
  type: single-node

cluster:
  name: netology

node:
  name: netology_test

network:
  host: 0.0.0.0

path:
  data: /var/lib/elasticsearch
  logs: /var/log/elasticsearch

</code></pre>



- ссылку на образ в репозитории dockerhub,

https://hub.docker.com/r/zatulik2606/elastic_netology/


- ответ `Elasticsearch` на запрос пути `/` в json-виде.

</p>
<pre><code>

[elasticsearch@a243cbeccdd2 elasticsearch-7.17.0]$ curl -X GET 'http://localhost:9200/'
{
  "name" : "netology_test",
  "cluster_name" : "netology",
  "cluster_uuid" : "zDzOUMBiSb-k_xHjkdvzVQ",
  "version" : {
	"number" : "7.17.0",
	"build_flavor" : "default",
	"build_type" : "tar",
	"build_hash" : "bee86328705acaa9a6daede7140defd4d9ec56bd",
	"build_date" : "2022-01-28T08:36:04.875279988Z",
	"build_snapshot" : false,
	"lucene_version" : "8.11.1",
	"minimum_wire_compatibility_version" : "6.8.0",
	"minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}


</code></pre>


Подсказки:

- возможно, вам понадобится установка пакета perl-Digest-SHA для корректной работы пакета shasum,
- при сетевых проблемах внимательно изучите кластерные и сетевые настройки в elasticsearch.yml,
- при некоторых проблемах вам поможет Docker-директива ulimit,
- Elasticsearch в логах обычно описывает проблему и пути её решения.

Далее мы будем работать с этим экземпляром Elasticsearch.

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

**Создаем индексы**

</p>
<pre><code>

curl -X PUT localhost:9200/ind-1 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 1,  "number_of_replicas": 0 }}'
curl -X PUT localhost:9200/ind-2 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 2,  "number_of_replicas": 1 }}'
curl -X PUT localhost:9200/ind-3 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 4,  "number_of_replicas": 2 }}'
</code></pre>


Получите состояние кластера `Elasticsearch`, используя API.

**Список индексов**

</p>
<pre><code>

[elasticsearch@a243cbeccdd2 elasticsearch-7.17.0]$ curl -X GET 'http://localhost:9200/_cat/indices?v'
health status index        	uuid               	pri rep docs.count docs.deleted store.size pri.store.size
green  open   .geoip_databases fZuC8q5BRym5D_6MLLekxg   1   0     	42        	0 	40.1mb     	40.1mb
green  open   ind-1        	y1PsN3MTQXWBa7bau5Pxew   1   0      	0        	0   	226b       	226b
yellow open   ind-3        	8Tn0pGIYQ6uoYcdLttEIKQ   4   2      	0        	0   	904b       	904b
yellow open   ind-2        	v5EK6L6iTC28JzhhShZViw   2   1      	0        	0   	452b       	452b


</code></pre>

**Статус индексов**

</p>
<pre><code>
elasticsearch@a243cbeccdd2 elasticsearch-7.17.0]$ curl -X GET 'http://localhost:9200/_cluster/health/ind-1?pretty'
{
  "cluster_name" : "netology",
  "status" : "green",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 1,
  "active_shards" : 1,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 0,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 100.0
}
[elasticsearch@a243cbeccdd2 elasticsearch-7.17.0]$ curl -X GET 'http://localhost:9200/_cluster/health/ind-2?pretty'
{
  "cluster_name" : "netology",
  "status" : "yellow",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 2,
  "active_shards" : 2,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 2,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 50.0
}

[elasticsearch@a243cbeccdd2 elasticsearch-7.17.0]$ curl -X GET 'http://localhost:9200/_cluster/health/ind-3?pretty'
{
  "cluster_name" : "netology",
  "status" : "yellow",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 4,
  "active_shards" : 4,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 8,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 50.0
}

</code></pre>

**Состояние кластера `Elasticsearch`, используя API**

</p>
<pre><code>

[elasticsearch@a243cbeccdd2 elasticsearch-7.17.0]$ curl -X GET localhost:9200/_cluster/health/?pretty=true
{
  "cluster_name" : "netology",
  "status" : "yellow",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 10,
  "active_shards" : 10,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 10,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 50.0
}
</code></pre>

Как вы думаете, почему часть индексов и кластер находятся в состоянии yellow?

</p>
<pre><code>

Индексы находятся в статусе Yellow потому что у них указано число реплик, а реплицировать некуда

</code></pre>

**Удалите все индексы.**

</p>
<pre><code>

curl -X DELETE 'http://localhost:9200/ind-1?pretty'
{
  "acknowledged" : true
}
$ curl -X DELETE 'http://localhost:9200/ind-2?pretty'
{
  "acknowledged" : true
}
$ curl -X DELETE 'http://localhost:9200/ind-3?pretty'
{
  "acknowledged" : true
}

</code></pre>


**Важно**

При проектировании кластера Elasticsearch нужно корректно рассчитывать количество реплик и шард,
иначе возможна потеря данных индексов, вплоть до полной, при деградации системы.

## Задача 3

В этом задании вы научитесь:

- создавать бэкапы данных,
- восстанавливать индексы из бэкапов.

Создайте директорию `{путь до корневой директории с Elasticsearch в образе}/snapshots`.

Используя API, [зарегистрируйте](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-register-repository.html#snapshots-register-repository) 
эту директорию как `snapshot repository` c именем `netology_backup`.


**Приведите в ответе** запрос API и результат вызова API для создания репозитория.

</p>
<pre><code>

curl -X PUT "localhost:9200/_snapshot/netology_backup?pretty" -H 'Content-Type: application/json' -d'
> {
>   "type": "fs",
>   "settings": {
> 	"location": "/var/lib/elasticsearch/snapshots",
> 	"compress": true
>   }
> }'
{
  "acknowledged" : true

</code></pre>



Создайте индекс `test` с 0 реплик и 1 шардом и **приведите в ответе** список индексов.

</p>
<pre><code>
curl -X PUT "localhost:9200/test?pretty" -H 'Content-Type: application/json' -d'
> {
>   "settings": {
> 	"number_of_shards": 1,
> 	"number_of_replicas": 0
>   }
> }
> '

curl 'localhost:9200/_cat/indices?v'
health status index        	uuid               	pri rep docs.count docs.deleted store.size pri.store.size
green  open   .geoip_databases vFusgBFBSwOvN_50SAmN4g   1   0     	42        	0 	40.1mb     	40.1mb
green  open   test         	NuAwkonpT9a_7sdAFy3HBw   1   0      	0        	0   	226b       	226b

</code></pre>


[Создайте `snapshot`](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-take-snapshot.html) 
состояния кластера `Elasticsearch`.


</p>
<pre><code>

curl -X PUT "localhost:9200/_snapshot/netology_backup/snapshot_1?wait_for_completion=true&pretty"
{
  "snapshot" : {
	"snapshot" : "snapshot_1",
	"uuid" : "kPWLX3sTRBGl0Ae_-ekxpA",
	"repository" : "netology_backup",
	"version_id" : 7170099,
	"version" : "7.17.0",
	"indices" : [
  	".geoip_databases",
  	".ds-ilm-history-5-2023.06.22-000001",
  	".ds-.logs-deprecation.elasticsearch-default-2023.06.22-000001",
  	"test"
	],
	"data_streams" : [
  	"ilm-history-5",
  	".logs-deprecation.elasticsearch-default"
	],
	"include_global_state" : true,
	"state" : "SUCCESS",
	"start_time" : "2023-06-22T14:11:35.751Z",
	"start_time_in_millis" : 1687443095751,
	"end_time" : "2023-06-22T14:11:38.161Z",
	"end_time_in_millis" : 1687443098161,
	"duration_in_millis" : 2410,
	"failures" : [ ],
	"shards" : {
  	"total" : 4,
  	"failed" : 0,
  	"successful" : 4
	},
	"feature_states" : [
  	{
    	"feature_name" : "geoip",
    	"indices" : [
      	".geoip_databases"
    	]
  	}
	]
  }
}



</code></pre>


**Приведите в ответе** список файлов в директории со `snapshot`.

</p>
<pre><code>

docker exec -it elasticsearch   ls -l /var/lib/elasticsearch/snapshots/
total 28
-rw-r--r-- 1 elasticsearch elasticsearch 1422 Jun 22 14:11 index-0
-rw-r--r-- 1 elasticsearch elasticsearch	8 Jun 22 14:11 index.latest
drwxr-xr-x 6 elasticsearch elasticsearch 4096 Jun 22 14:11 indices
-rw-r--r-- 1 elasticsearch elasticsearch 9779 Jun 22 14:11 meta-kPWLX3sTRBGl0Ae_-ekxpA.dat
-rw-r--r-- 1 elasticsearch elasticsearch  454 Jun 22 14:11 snap-kPWLX3sTRBGl0Ae_-ekxpA.dat

</code></pre>


Удалите индекс `test` и создайте индекс `test-2`. **Приведите в ответе** список индексов.
</p>
<pre><code>
curl -X DELETE "localhost:9200/test?pretty"
{
  "acknowledged" : true
}
[elasticsearch@cec1ef1d2876 elasticsearch-7.17.0]$ curl -X PUT "localhost:9200/test-2?pretty" -H 'Content-Type: application/json' -d'
> {
>   "settings": {
> 	"number_of_shards": 1,
> 	"number_of_replicas": 0
>   }
> }
> '
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "test-2"
}
[elasticsearch@cec1ef1d2876 elasticsearch-7.17.0]$ curl 'localhost:9200/_cat/indices?pretty'
green open test-2       	1S74_GB8TMiSwicxRH1ajg 1 0  0 0   226b   226b
green open .geoip_databases vFusgBFBSwOvN_50SAmN4g 1 0 42 0 40.1mb 40.1mb


</code></pre>


[Восстановите](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-restore-snapshot.html) состояние
кластера `Elasticsearch` из `snapshot`, созданного ранее. 

</p>
<pre><code>

curl -X POST "localhost:9200/_snapshot/netology_backup/snapshot_1/_restore?pretty" -H 'Content-Type: application/json' -d'
{
  "indices": "*",
  "include_global_state": true
}
'
curl 'localhost:9200/_cat/indices?pretty'
green open test-2       	1S74_GB8TMiSwicxRH1ajg 1 0  0 0   226b   226b
green open .geoip_databases vFusgBFBSwOvN_50SAmN4g 1 0 42 0 40.1mb 40.1mb


</code></pre>


**Приведите в ответе** запрос к API восстановления и итоговый список индексов.

Подсказки:

- возможно, вам понадобится доработать `elasticsearch.yml` в части директивы `path.repo` и перезапустить `Elasticsearch`.

---

### Как cдавать задание

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---

