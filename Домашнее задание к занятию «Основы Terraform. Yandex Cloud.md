# Домашнее задание к занятию «Основы Terraform. Yandex Cloud»

### Цель задания

1. Создать свои ресурсы в облаке Yandex Cloud с помощью Terraform.
2. Освоить работу с переменными Terraform.


### Чеклист готовности к домашнему заданию

1. Зарегистрирован аккаунт в Yandex Cloud. Использован промокод на грант.
2. Установлен инструмент Yandex Cli.
3. Исходный код для выполнения задания расположен в директории [**02/src**](https://github.com/netology-code/ter-homeworks/tree/main/02/src).


### Задание 0

1. Ознакомьтесь с [документацией к security-groups в Yandex Cloud](https://cloud.yandex.ru/docs/vpc/concepts/security-groups?from=int-console-help-center-or-nav).
2. Запросите preview доступ к данному функционалу в ЛК Yandex Cloud. Обычно его выдают в течении 24-х часов.
https://console.cloud.yandex.ru/folders/<ваш cloud_id>/vpc/security-groups.   
Этот функционал понадобится к следующей лекции. 


### Задание 1

1. Изучите проект. В файле variables.tf объявлены переменные для yandex provider.
2. Переименуйте файл personal.auto.tfvars_example в personal.auto.tfvars. Заполните переменные (идентификаторы облака, токен доступа). Благодаря .gitignore этот файл не попадет в публичный репозиторий. **Вы можете выбрать иной способ безопасно передать секретные данные в terraform.**
3. Сгенерируйте или используйте свой текущий ssh ключ. Запишите его открытую часть в переменную **vms_ssh_root_key**.
4. Инициализируйте проект, выполните код. Исправьте намеренное допущенные ошибки. Ответьте в чем заключается их суть?
5. Ответьте, как в процессе обучения могут пригодиться параметры```preemptible = true``` и ```core_fraction=5``` в параметрах ВМ? Ответ в документации Yandex cloud.

В качестве решения приложите:
- скриншот ЛК Yandex Cloud с созданной ВМ,

![yandexVM](https://github.com/zatulik2606/Netology-devops/blob/screenshorts/virtualmachineyandex.png)
- скриншот успешного подключения к консоли ВМ через ssh,

![sshconnect](https://github.com/zatulik2606/Netology-devops/blob/screenshorts/sshconnect.png)
- ответы на вопросы.

**По ошибкам**
</p>
<pre><code>
Изменение названий

12 data "yandex_compute_image" "ubuntu-2004-lts" {

 13   family = "ubuntu-2004-lts"


24 	initialize_params {

 25   	image_id = data.yandex_compute_image.ubuntu-2004-lts.image_id




Корректировка параметра standard (ранее был standart и v4), изменение количества ядер на 2


15 resource "yandex_compute_instance" "platform" {

 16   name    	= "netology-develop-platform-web"
 
 17   platform_id = "standard-v2"
 
 18   resources {
 
 19 	cores     	= 2
 
 20 	memory    	= 1
 
 21 	core_fraction = 5
 
 </code></pre>

*По вопросам:*
</p>
<pre><code>

  preemptible VM - прерываемые ВМ, хороши тем, что если при выполнении ДЗ забыть ее выключить, она будет выключена через случайный момент в промежутке 22-24 часа с момента запуска. Так же этот тип ВМ удобен тем, что если в указанной зоне не будет хватать ресурсов для запуска обычной ВМ, прерываемая будет выключена автоматически.

  core_fraction - уровень производительности CPU. От его значения зависит стоимость ВМ и вычислительные возможности. Виртуальные машины с уровнем производительности меньше 100% имеют доступ к вычислительной мощности физических ядер как минимум на протяжении указанного процента от единицы времени

</code></pre>



### Задание 2

1. Изучите файлы проекта.
2. Замените все "хардкод" **значения** для ресурсов **yandex_compute_image** и **yandex_compute_instance** на **отдельные** переменные. К названиям переменных ВМ добавьте в начало префикс **vm_web_** .  Пример: **vm_web_name**.
2. Объявите нужные переменные в файле variables.tf, обязательно указывайте тип переменной. Заполните их **default** прежними значениями из main.tf. 
3. Проверьте terraform plan (изменений быть не должно). 


Заменили так .

</p>
<pre><code>
data "yandex_compute_image" "ubuntu-2004-lts" {
    family = var.vm_web_family
}
resource "yandex_compute_instance" "platform" {
  name = var.vm_web_name
  platform_id = var.vm_web_platform
</code></pre>  
  

В файле variables.tf, добавил эти переменные
</p>
<pre><code>	 	 	
variable "vm_web_name" {
type = string
default = "netology-develop-platform-web"
description = "name_of_instance"
}

variable "vm_web_family" {
type = string
default = "ubuntu-2004-lts"
description = "name_of_instance"
}
variable "vm_web_platform" {
type = string
default = "standard-v2"
description = "type_of_standard"
}
</code></pre>

Изменений нет

![nochanges](https://github.com/zatulik2606/Netology-devops/blob/screenshorts/nochangeslast.png)



### Задание 3

1. Создайте в корне проекта файл 'vms_platform.tf' . Перенесите в него все переменные первой ВМ.
2. Скопируйте блок ресурса и создайте с его помощью вторую ВМ(в файле main.tf): **"netology-develop-platform-db"** ,  cores  = 2, memory = 2, core_fraction = 20. Объявите ее переменные с префиксом **vm_db_** в том же файле('vms_platform.tf').
3. Примените изменения.

Изменения в файле

</p>
<pre><code>	
variable "vm_db_name" {
type = string
default = "netology-develop-platform-db"
description = "name_of_db"
}



variable "vm_db_family" {
type = string
default = "ubuntu-2004-lts"
description = "name_of_instance"
}



variable "vm_db_platform" {
type = string
default = "standard-v2"
description = "type_of_standard"
}
</code></pre>

машины создались.

![2Machine](https://github.com/zatulik2606/Netology-devops/blob/screenshorts/zadanie32machine.png)


### Задание 4

1. Объявите в файле outputs.tf output типа map, содержащий { instance_name = external_ip } для каждой из ВМ.
2. Примените изменения.

В качестве решения приложите вывод значений ip-адресов команды ```terraform output```

Вывод output ниже

</p>
<pre><code>	

root@vagrant:~/ter-homeworks/02/src# terraform output
db_instance_public_ip = "51.250.85.202"
web_instance_public_ip = "130.193.51.2"



</code></pre>


### Задание 5

1. В файле locals.tf опишите в **одном** local-блоке имя каждой ВМ, используйте интерполяцию ${..} с несколькими переменными по примеру из лекции.
2. Замените переменные с именами ВМ из файла variables.tf на созданные вами local переменные.
3. Примените изменения.

Задал новые переменные.

</p>
<pre><code>	

variable "vm_name" {
  type    	= string
  default 	= "platform"
  description = "platform"
}

variable "tutor" {
  type    	= string
  default 	= "netology"
  description = "netology"
}

variable "kurs" {
  type    	= string
  default 	= "develop"
  description = "develop"
}

variable "web" {
  type    	= string
  default 	= "web"
  description = "web"
}

variable "db" {
  type    	= string
  default 	= "db"
  description = "db"
}


</code></pre>

Теперь local выгядит так:
</p>
<pre><code>	


locals {
  vm_web = "${var.tutor}-${var.kurs}-${var.vm_name}-${var.web}"
}

locals {
  vm_db = "${var.tutor}-${var.kurs}-${var.vm_name}-${var.db}"
}
locals {
	vm_db_resources 	= {core = "2", mem = "2", frac = "20"}
	vm_web_resources	= {core = "2", mem = "1", frac = "5"}
	sport           	= "1"
	key             	= "ubuntu:${var.vms_ssh_root_key}"
}


</code></pre>

Создал файл с локальными переменными

</p>
<pre><code>	

locals {
  vm_web = "netology-develop-platform-web"
}

locals {
  vm_db = "netology-develop-platform-db"
}

</code></pre>

Так изменил в main

</p>
<pre><code>	

resource "yandex_compute_instance" "platform" {
  name    	= local.vm_web


resource "yandex_compute_instance" "platform-db" {
  name    	= local.vm_db


</code></pre>

Применил изменения.
</p>
<pre><code>	

root@vagrant:~/ter-homeworks/02/src# terraform apply
data.yandex_compute_image.ubuntu: Reading...
yandex_vpc_network.develop: Refreshing state... [id=enp7kbuhthkdu5mnmue5]
data.yandex_compute_image.ubuntu-2004-lts: Reading...
data.yandex_compute_image.ubuntu: Read complete after 1s [id=fd84n8eontaojc77hp0u]
data.yandex_compute_image.ubuntu-2004-lts: Read complete after 1s [id=fd84n8eontaojc77hp0u]
yandex_vpc_subnet.develop: Refreshing state... [id=e9bdt72uke0i18lc20h1]
yandex_compute_instance.platform-db: Refreshing state... [id=fhm0aqj52806j8k5dqsd]
yandex_compute_instance.platform: Refreshing state... [id=fhm5rp6epkhtkco97mvh]

No changes. Your infrastructure matches the configuration.

Terraform has compared your real infrastructure against your configuration and found no differences, so no changes are needed.

Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

Outputs:

db_instance_public_ip = "51.250.85.202"
web_instance_public_ip = "130.193.51.2"


</code></pre>



### Задание 6

1. Вместо использования 3-х переменных  ".._cores",".._memory",".._core_fraction" в блоке  resources {...}, объедените их в переменные типа **map** с именами "vm_web_resources" и "vm_db_resources". В качестве продвинутой практики попробуйте создать одну map переменную **vms_resources** и уже внутри нее конфиги обеих ВМ(вложенный map).
2. Так же поступите с блоком **metadata {serial-port-enable, ssh-keys}**, эта переменная должна быть общая для всех ваших ВМ.
3. Найдите и удалите все более не используемые переменные проекта.
4. Проверьте terraform plan (изменений быть не должно).

Добавил переменные:

</p>
<pre><code>	

locals {
    vm_db_resources     = {core = "2", mem = "2", frac = "20"}
    vm_web_resources    = {core = "2", mem = "1", frac = "5"}
    sport               = "1"
    key                 = "ubuntu:${var.vms_ssh_root_key}"
}

</code></pre>

Также изменил main.tf

</p>
<pre><code>	

metadata = {
    serial-port-enable    = local.sport
    ssh-keys              = local.key    
}


</code></pre>

Изменений нет, конфиги спрятаны:
</p>
<pre><code>	

 ![yandexVM](https://github.com/zatulik2606/Netology-devops/blob/screenshorts/nochanges.png)

</code></pre>



