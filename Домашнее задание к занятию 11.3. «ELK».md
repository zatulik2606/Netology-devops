Домашнее задание к занятию 11.3. «ELK»
Инструкция по выполнению домашнего задания
________________________________________
Задание 1. Elasticsearch
Установите и запустите Elasticsearch, после чего поменяйте параметр cluster_name на случайный.
Приведите скриншот команды 'curl -X GET 'localhost:9200/_cluster/health?pretty', сделанной на сервере с установленным Elasticsearch. Где будет виден нестандартный cluster_name.

![Elastic](https://github.com/zatulik2606/Netology-devops/blob/screenshorts/elasticksearch.png)

______________________________________
Задание 2. Kibana
Установите и запустите Kibana.
Приведите скриншот интерфейса Kibana на странице http://<ip вашего сервера>:5601/app/dev_tools#/console, где будет выполнен запрос GET /_cluster/health?pretty.

![Kibana](https://github.com/zatulik2606/Netology-devops/blob/screenshorts/Kibana.png)
________________________________________
Задание 3. Logstash
Установите и запустите Logstash и Nginx. С помощью Logstash отправьте access-лог Nginx в Elasticsearch.
Приведите скриншот интерфейса Kibana, на котором видны логи Nginx.

![Logstash](https://github.com/zatulik2606/Netology-devops/blob/screenshorts/logstash.png)
________________________________________
Задание 4. Filebeat.
Установите и запустите Filebeat. Переключите поставку логов Nginx с Logstash на Filebeat.
Приведите скриншот интерфейса Kibana, на котором видны логи Nginx, которые были отправлены через Filebeat.

![Filebeat](https://github.com/zatulik2606/Netology-devops/blob/screenshorts/Filebeat.png)



