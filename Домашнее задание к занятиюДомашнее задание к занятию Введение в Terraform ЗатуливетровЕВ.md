# Домашнее задание к занятию «Введение в Terraform»

### Цель задания

1. Установить и настроить Terrafrom.
2. Научиться использовать готовый код.

------

### Чеклист готовности к домашнему заданию

1. Скачайте и установите актуальную версию **terraform** >=1.4.X . Приложите скриншот вывода команды ```terraform --version```.
2. Скачайте на свой ПК данный git репозиторий. Исходный код для выполнения задания расположен в директории **01/src**.
3. Убедитесь, что в вашей ОС установлен docker.

------

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. Установка и настройка Terraform  [ссылка](https://cloud.yandex.ru/docs/tutorials/infrastructure-management/terraform-quickstart#from-yc-mirror)
2. Зеркало документации Terraform  [ссылка](https://registry.tfpla.net/browse/providers) 
3. Установка docker [ссылка](https://docs.docker.com/engine/install/ubuntu/) 
------

### Задание 1

1. Перейдите в каталог [**src**](https://github.com/netology-code/ter-homeworks/tree/main/01/src). Скачайте все необходимые зависимости, использованные в проекте. 
2. Изучите файл **.gitignore**. В каком terraform файле согласно этому .gitignore допустимо сохранить личную, секретную информацию?



*# own secret vars store.*
  
*personal.auto.tfvars*

  
 
  
3. Выполните код проекта. Найдите  в State-файле секретное содержимое созданного ресурса **random_password**, пришлите в качестве ответа конкретный ключ и его значение.


  
 *3AGArBFnP7TQMvCP* 
  
  

4. Раскомментируйте блок кода, примерно расположенный на строчках 29-42 файла **main.tf**.
Выполните команду ```terraform validate```. Объясните в чем заключаются намеренно допущенные ошибки? Исправьте их.

</p>
<pre><code>
 root@vagrant:~/ter-homeworks/01/src# terraform validate
╷
│ Error: Missing name for resource
│
│   on main.tf line 24, in resource "docker_image":
│   24: resource "docker_image" {
│
│ All resource blocks must have 2 labels (type, name).
╵
╷
│ Error: Invalid resource name
│
│   on main.tf line 29, in resource "docker_container" "1nginx":
│   29: resource "docker_container" "1nginx" {
│
│ A name must start with a letter or underscore and may contain only letters, digits, underscores, and dashes.

Error: Reference to undeclared resource
│
│   on main.tf line 31, in resource "docker_container" "nginx":
│   31:   name  = "example_${random_password.random_string_fake.resuld}"
│
│ A managed resource "random_password" "random_string_fake" has not been declared in the root module.






для 24 строки указать имя.
Для 29 строки убрать значение “1” в названии.
Для 31 строки задать корректное название

24 resource "docker_image" "nginx" {
 25   name     	= "nginx:latest"
 26   keep_locally = true
 27 }
 28
 29 resource "docker_container" "nginx" {
 30   image = docker_image.nginx.image_id
 31   name  = "random_password.random_string"

</code></pre>



5. Выполните код. В качестве ответа приложите вывод команды ```docker ps```

</p>
<pre><code>
root@vagrant:~/ter-homeworks/01/src# docker ps
CONTAINER ID   IMAGE      	COMMAND              	CREATED     	STATUS     	PORTS              	NAMES
c166930c54f8   448a08f1d2f9   "/docker-entrypoint.…"   9 seconds ago   Up 6 seconds   0.0.0.0:8000->80/tcp   random_password.random_string




</code></pre>



  
6. Замените имя docker-контейнера в блоке кода на ```hello_world```, выполните команду ```terraform apply -auto-approve```.
Объясните своими словами, в чем может быть опасность применения ключа  ```-auto-approve``` ? В качестве ответа дополнительно приложите вывод команды ```docker ps```

</p>
<pre><code

resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id
  name  = "hello-world"


root@vagrant:~/ter-homeworks/01/src# terraform apply -auto-approve
docker_image.nginx: Refreshing state... [id=sha256:448a08f1d2f94e8db6db9286fd77a3a4f3712786583720a12f1648abb8cace25nginx:latest]
random_password.random_string: Refreshing state... [id=none]
docker_container.nginx: Refreshing state... [id=c166930c54f8e9c07c41b5493b660d5f6f2c8850e7483cf982715fbe11ff4e83]




Опасность в том, что внесенные изменения будут применены, без оповещения на экране. Т.е. не будет выведен список всех предстоящих изменений, в запросе  нужно ответить просто  yes.



root@vagrant:~/ter-homeworks/01/src# docker ps
CONTAINER ID   IMAGE      	COMMAND              	CREATED     	STATUS     	PORTS              	NAMES
f722711869c3   448a08f1d2f9   "/docker-entrypoint.…"   7 seconds ago   Up 5 seconds   0.0.0.0:8000->80/tcp   hello-world



</code></pre>

7. Уничтожьте созданные ресурсы с помощью **terraform**. Убедитесь, что все ресурсы удалены. Приложите содержимое файла **terraform.tfstate**. 

</p>
<pre><code

Удаляем контейнер.

root@vagrant:~/ter-homeworks/01/src# docker ps
CONTAINER ID   IMAGE      	COMMAND              	CREATED          	STATUS          	PORTS              	NAMES
f722711869c3   448a08f1d2f9   "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:8000->80/tcp   hello-world
root@vagrant:~/ter-homeworks/01/src# docker stop f722711869c3
f722711869c3
root@vagrant:~/ter-homeworks/01/src# docker rm f722711869c3
f722711869c3




</code></pre>

</p>
<pre><code

Удаляем terraform файлы
root@vagrant:~/ter-homeworks/01/src# terraform destroy
random_password.random_string: Refreshing state... [id=none]
docker_image.nginx: Refreshing state... [id=sha256:448a08f1d2f94e8db6db9286fd77a3a4f3712786583720a12f1648abb8cace25nginx:latest]
docker_container.nginx: Refreshing state... [id=f722711869c3678013ce092ddb52d37322b3e022e7d38ae9edaaaeed6fe38f17]


Данные файла terraform.tfstate.
{
  "version": 4,
  "terraform_version": "1.4.6",
  "serial": 10,
  "lineage": "367c51a0-d7b1-beb6-315a-bfd2d1e4279f",
  "outputs": {},
  "resources": [],
  "check_results": null
}


</code></pre>


8. Объясните, почему при этом не был удален docker образ **nginx:latest** ? Ответ подкрепите выдержкой из документации провайдера.

</p>
<pre><code

По [ссылке](https://registry.terraform.io/providers/kreuzwerker/docker/latest/docs/resources/image) есть описание:


**keep_locally (Boolean) If true, then the Docker image won't be deleted on destroy operation. If this is false, it will delete the image from the docker local storage on destroy operation.**

keep_locally = true



</code></pre>




------
