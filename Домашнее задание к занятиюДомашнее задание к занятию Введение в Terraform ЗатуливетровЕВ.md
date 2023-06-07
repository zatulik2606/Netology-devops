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


  
 *xG3JFEe5wpUv0EwM* 
  
  

4. Раскомментируйте блок кода, примерно расположенный на строчках 29-42 файла **main.tf**.
Выполните команду ```terraform validate```. Объясните в чем заключаются намеренно допущенные ошибки? Исправьте их.

</p>
<pre><code>
root@vagrant:~/ter-homeworks/01/src# terraform validate
╷
│ Error: Missing name for resource
│
│   on main.tf line 23, in resource "docker_image":
│   23: resource "docker_image" {
│
│ All resource blocks must have 2 labels (type, name).
╵
╷
│ Error: Invalid resource name
│
│   on main.tf line 28, in resource "docker_container" "1nginx":
│   28: resource "docker_container" "1nginx" {
│
│ A name must start with a letter or underscore and may contain only letters, digits, underscores, and dashes.

Необходимо для 23 строки указать имя.
Для 28 строки убрать значение “1” в названии.

resource "docker_image" "nginx" {
  name     	= "nginx:latest"
  keep_locally = true
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id
  name  = "example_${random_password.random_string_fake.resuld}"
</code></pre>



5. Выполните код. В качестве ответа приложите вывод команды ```docker ps```

</p>
<pre><code>
root@vagrant:~/ter-homeworks/01/src# docker ps
CONTAINER ID   IMAGE      	COMMAND              	CREATED     	STATUS     	PORTS              	NAMES
7e8a7548ae28   448a08f1d2f9   "/docker-entrypoint.…"   6 seconds ago   Up 5 seconds   0.0.0.0:8000->80/tcp   example



</code></pre>



  
6. Замените имя docker-контейнера в блоке кода на ```hello_world```, выполните команду ```terraform apply -auto-approve```.
Объясните своими словами, в чем может быть опасность применения ключа  ```-auto-approve``` ? В качестве ответа дополнительно приложите вывод команды ```docker ps```

</p>
<pre><code

resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id
  name  = "hello-world"


root@vagrant:~/ter-homeworks/01/src# terraform apply -auto-approve
random_password.random_string: Refreshing state... [id=none]
docker_image.nginx: Refreshing state... [id=sha256:448a08f1d2f94e8db6db9286fd77a3a4f3712786583720a12f1648abb8cace25nginx:latest]
docker_container.nginx: Refreshing state... [id=7e8a7548ae28d470202a5e26d4413480837e86dcb9283e5969ac2621d13f5902]



Опасность в том, что внесенные изменения будут применены, без оповещения на экране. Т.е. не будет выведен список всех предстоящих изменений, в запросе  нужно ответить просто  yes.



root@vagrant:~/ter-homeworks/01/src# docker ps
CONTAINER ID   IMAGE      	COMMAND              	CREATED      	STATUS      	PORTS              	NAMES
55418d3872c2   448a08f1d2f9   "/docker-entrypoint.…"   12 seconds ago   Up 11 seconds   0.0.0.0:8000->80/tcp   hello-world


</code></pre>

7. Уничтожьте созданные ресурсы с помощью **terraform**. Убедитесь, что все ресурсы удалены. Приложите содержимое файла **terraform.tfstate**. 

</p>
<pre><code

Удаляем контейнер.

root@vagrant:~/ter-homeworks/01/src# docker ps
CONTAINER ID   IMAGE      	COMMAND              	CREATED     	STATUS     	PORTS              	NAMES
55418d3872c2   448a08f1d2f9   "/docker-entrypoint.…"   4 minutes ago   Up 4 minutes   0.0.0.0:8000->80/tcp   hello-world
root@vagrant:~/ter-homeworks/01/src# docker stop 55418d3872c2
55418d3872c2
root@vagrant:~/ter-homeworks/01/src# docker rm 55418d3872c2
55418d3872c2


</code></pre>

</p>
<pre><code

Удаляем terraform файлы

root@vagrant:~/ter-homeworks/01/src# terraform destroy
docker_image.nginx: Refreshing state... [id=sha256:448a08f1d2f94e8db6db9286fd77a3a4f3712786583720a12f1648abb8cace25nginx:latest]
random_password.random_string: Refreshing state... [id=none]
docker_container.nginx: Refreshing state... [id=55418d3872c21f6b255c1f4fa8832f8efbda82e85dda44ecbbb032817a9c80de]


</code></pre>

</p>
<pre><code

Данные файла terraform.tfstate.
{
  "version": 4,
  "terraform_version": "1.4.6",
  "serial": 11,
  "lineage": "a5054f07-cfce-7d77-fe96-1ea1dc807025",
  "outputs": {},
  "resources": [],
  "check_results": null
}


</code></pre>


8. Объясните, почему при этом не был удален docker образ **nginx:latest** ? Ответ подкрепите выдержкой из документации провайдера.

</p>
<pre><code

keep_locally = true



</code></pre>




------
