# Домашнее задание к занятию 12 «GitLab»

## Подготовка к выполнению


1. Или подготовьте к работе Managed GitLab от yandex cloud [по инструкции](https://cloud.yandex.ru/docs/managed-gitlab/operations/instance/instance-create) .
Или создайте виртуальную машину из публичного образа [по инструкции](https://cloud.yandex.ru/marketplace/products/yc/gitlab ) .
2. Создайте виртуальную машину и установите на нее gitlab runner, подключите к вашему серверу gitlab  [по инструкции](https://docs.gitlab.com/runner/install/linux-repository.html) .

3. (* Необязательное задание повышенной сложности. )  Если вы уже знакомы с k8s попробуйте выполнить задание, запустив gitlab server и gitlab runner в k8s  [по инструкции](https://cloud.yandex.ru/docs/tutorials/infrastructure-management/gitlab-containers). 

4. Создайте свой новый проект.
5. Создайте новый репозиторий в GitLab, наполните его [файлами](./repository).
6. Проект должен быть публичным, остальные настройки по желанию.

## Основная часть

### DevOps

В репозитории содержится код проекта на Python. Проект — RESTful API сервис. Ваша задача — автоматизировать сборку образа с выполнением python-скрипта:

1. Образ собирается на основе [centos:7](https://hub.docker.com/_/centos?tab=tags&page=1&ordering=last_updated).
2. Python версии не ниже 3.7.
3. Установлены зависимости: `flask` `flask-jsonpify` `flask-restful`.
4. Создана директория `/python_api`.
5. Скрипт из репозитория размещён в /python_api.
6. Точка вызова: запуск скрипта.
7. При комите в любую ветку должен собираться docker image с форматом имени hello:gitlab-$CI_COMMIT_SHORT_SHA . Образ должен быть выложен в Gitlab registry или yandex registry.   

Не нашел как добавить "hello:gitlab-$CI_COMMIT_SHORT_SHA" оставил как есь.

~~~
image: docker:20.10.5
services:
    - docker:20.10.5-dind
stages:
  - build
  - deploy


docker-build:
  stage: build
  script:
    - docker login -u "$CI_REGISTRY_USER" -p "$CI_REGISTRY_PASSWORD" $CI_REGISTRY
    - docker build --pull -t $CI_REGISTRY/zatulik/netology/python-api:latest .
  rules:
    - if: $CI_COMMIT_BRANCH
      exists:
        - Dockerfile

docker-deploy:
  stage: deploy
  script:
    - docker login -u "$CI_REGISTRY_USER" -p "$CI_REGISTRY_PASSWORD" $CI_REGISTRY
    - docker build --pull -t $CI_REGISTRY/zatulik/netology/python-api:latest  . 
    - docker push $CI_REGISTRY/zatulik/netology/python-api:latest

~~~

### Product Owner

Вашему проекту нужна бизнесовая доработка: нужно поменять JSON ответа на вызов метода GET `/rest/api/get_info`, необходимо создать Issue в котором указать:

1. Какой метод необходимо исправить.
2. Текст с `{ "message": "Already started" }` на `{ "message": "Running"}`.
3. Issue поставить label: feature.


Составитл задачу.

![Task](https://github.com/zatulik2606/Netology-devops/blob/screenshorts/task.jpg)

### Developer

Пришёл новый Issue на доработку, вам нужно:

1. Создать отдельную ветку, связанную с этим Issue.
2. Внести изменения по тексту из задания.
3. Подготовить Merge Request, влить необходимые изменения в `master`, проверить, что сборка прошла успешно.

Выполнил задачу для Developer 

![Task](https://github.com/zatulik2606/Netology-devops/blob/screenshorts/developer.jpg)



### Tester

Разработчики выполнили новый Issue, необходимо проверить валидность изменений:

1. Поднять докер-контейнер с образом `python-api:latest` и проверить возврат метода на корректность.
2. Закрыть Issue с комментарием об успешности прохождения, указав желаемый результат и фактически достигнутый.

Выполнил проверку

~~~

 sudo docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS     NAMES
a2cd7428a9e2   71d9cbaf2f22   "python3 python-api.…"   2 minutes ago   Up 2 minutes             trusting_darwin


$ curl localhost:5290/get_info
{"version": 3, "method": "GET", "message": "Running"}
~~~

## Итог

В качестве ответа пришлите подробные скриншоты по каждому пункту задания:

- файл gitlab-ci.yml;

  
[gitlab-ci](https://github.com/zatulik2606/Netology-devops/blob/main/.gitlab-ci.yml)

- Dockerfile;

  
  [Dockerfile](https://github.com/zatulik2606/Netology-devops/blob/main/Dockerfile)

  
- лог успешного выполнения пайплайна;

![log](https://github.com/zatulik2606/Netology-devops/blob/screenshorts/pipeline.jpg)
  
- решённый Issue.

![Closedissue](https://github.com/zatulik2606/Netology-devops/blob/screenshorts/closedissie.jpg)

### Важно 
После выполнения задания выключите и удалите все задействованные ресурсы в Yandex Cloud.
