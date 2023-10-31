# Домашнее задание к занятию 5 «Тестирование roles»

## Подготовка к выполнению

1. Установите molecule: `pip3 install "molecule==3.5.2"` и драйвера `pip3 install molecule_docker molecule_podman`.
2. Выполните `docker pull aragast/netology:latest` —  это образ с podman, tox и несколькими пайтонами (3.7 и 3.9) внутри.

## Основная часть

Ваша цель — настроить тестирование ваших ролей. 

Задача — сделать сценарии тестирования для vector. 

Ожидаемый результат — все сценарии успешно проходят тестирование ролей.

### Molecule

1. Запустите  `molecule test -s centos_7` внутри корневой директории clickhouse-role, посмотрите на вывод команды. Данная команда может отработать с ошибками, это нормально. Наша цель - посмотреть как другие в реальном мире используют молекулу.
2. Перейдите в каталог с ролью vector-role и создайте сценарий тестирования по умолчанию при помощи `molecule init scenario --driver-name docker`.
3. Добавьте несколько разных дистрибутивов (centos:8, ubuntu:latest) для инстансов и протестируйте роль, исправьте найденные ошибки, если они есть.

<details><summary>Пример проверки для ubuntu</summary>
  
root@debian:~/08-05/roles/vector-role# molecule test -s ubuntu_latest
[DEPRECATION WARNING]: Ansible will require Python 3.8 or newer on the
controller starting with Ansible 2.12. Current version: 3.7.3 (default, Oct 11
2023, 09:51:27) [GCC 8.3.0]. This feature will be removed from ansible-core in
version 2.12. Deprecation warnings can be disabled by setting
deprecation_warnings=False in ansible.cfg.
INFO 	ubuntu_latest scenario test matrix: dependency, lint, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO 	Performing prerun...
INFO 	Set ANSIBLE_LIBRARY=/root/.cache/ansible-compat/6fcd2b/modules:/root/.ansible/plugins/modules:/usr/share/ansible/plugins/modules
INFO 	Set ANSIBLE_COLLECTIONS_PATH=/root/.cache/ansible-compat/6fcd2b/collections:/root/.ansible/collections:/usr/share/ansible/collections
INFO 	Set ANSIBLE_ROLES_PATH=/root/.cache/ansible-compat/6fcd2b/roles:/root/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles
INFO 	Running ubuntu_latest > dependency
WARNING  Skipping, missing the requirements file.
WARNING  Skipping, missing the requirements file.
INFO 	Running ubuntu_latest > lint
INFO 	Lint is disabled.
INFO 	Running ubuntu_latest > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO 	Running ubuntu_latest > destroy
INFO 	Sanity checks: 'docker'
[DEPRECATION WARNING]: Ansible will require Python 3.8 or newer on the
controller starting with Ansible 2.12. Current version: 3.7.3 (default, Oct 11
2023, 09:51:27) [GCC 8.3.0]. This feature will be removed from ansible-core in
version 2.12. Deprecation warnings can be disabled by setting
deprecation_warnings=False in ansible.cfg.

PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item=ubuntu_latest)

TASK [Wait for instance(s) deletion to complete] *******************************
FAILED - RETRYING: Wait for instance(s) deletion to complete (300 retries left).
ok: [localhost] => (item=ubuntu_latest)

TASK [Delete docker networks(s)] ***********************************************

PLAY RECAP *********************************************************************
localhost              	: ok=2	changed=1	unreachable=0	failed=0	skipped=1	rescued=0	ignored=0

INFO 	Running ubuntu_latest > syntax
[DEPRECATION WARNING]: Ansible will require Python 3.8 or newer on the
controller starting with Ansible 2.12. Current version: 3.7.3 (default, Oct 11
2023, 09:51:27) [GCC 8.3.0]. This feature will be removed from ansible-core in
version 2.12. Deprecation warnings can be disabled by setting
deprecation_warnings=False in ansible.cfg.

playbook: /root/08-05/roles/vector-role/molecule/ubuntu_latest/converge.yml
INFO 	Running ubuntu_latest > create
[DEPRECATION WARNING]: Ansible will require Python 3.8 or newer on the
controller starting with Ansible 2.12. Current version: 3.7.3 (default, Oct 11
2023, 09:51:27) [GCC 8.3.0]. This feature will be removed from ansible-core in
version 2.12. Deprecation warnings can be disabled by setting
deprecation_warnings=False in ansible.cfg.

PLAY [Create] ******************************************************************

TASK [Log into a Docker registry] **********************************************
skipping: [localhost] => (item=None)
skipping: [localhost]

TASK [Check presence of custom Dockerfiles] ************************************
ok: [localhost] => (item={'image': 'docker.io/pycontribs/ubuntu:latest', 'name': 'ubuntu_latest', 'pre_build_image': True})

TASK [Create Dockerfiles from image names] *************************************
skipping: [localhost] => (item={'image': 'docker.io/pycontribs/ubuntu:latest', 'name': 'ubuntu_latest', 'pre_build_image': True})

TASK [Discover local Docker images] ********************************************
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': {'image': 'docker.io/pycontribs/ubuntu:latest', 'name': 'ubuntu_latest', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 0, 'ansible_index_var': 'i'})

TASK [Build an Ansible compatible image (new)] *********************************
skipping: [localhost] => (item=molecule_local/docker.io/pycontribs/ubuntu:latest)

TASK [Create docker network(s)] ************************************************

TASK [Determine the CMD directives] ********************************************
ok: [localhost] => (item={'image': 'docker.io/pycontribs/ubuntu:latest', 'name': 'ubuntu_latest', 'pre_build_image': True})

TASK [Create molecule instance(s)] *********************************************
changed: [localhost] => (item=ubuntu_latest)

TASK [Wait for instance(s) creation to complete] *******************************
FAILED - RETRYING: Wait for instance(s) creation to complete (300 retries left).
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '827353572789.17002', 'results_file': '/root/.ansible_async/827353572789.17002', 'changed': True, 'failed': False, 'item': {'image': 'docker.io/pycontribs/ubuntu:latest', 'name': 'ubuntu_latest', 'pre_build_image': True}, 'ansible_loop_var': 'item'})

PLAY RECAP *********************************************************************
localhost              	: ok=5	changed=2	unreachable=0	failed=0	skipped=4	rescued=0	ignored=0

INFO 	Running ubuntu_latest > prepare
WARNING  Skipping, prepare playbook not configured.
INFO 	Running ubuntu_latest > converge
[DEPRECATION WARNING]: Ansible will require Python 3.8 or newer on the
controller starting with Ansible 2.12. Current version: 3.7.3 (default, Oct 11
2023, 09:51:27) [GCC 8.3.0]. This feature will be removed from ansible-core in
version 2.12. Deprecation warnings can be disabled by setting
deprecation_warnings=False in ansible.cfg.

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
ok: [ubuntu_latest]

TASK [Include vector-role] *****************************************************

TASK [vector-role : Get Vector distrib | CentOS] *******************************
skipping: [ubuntu_latest]

TASK [vector-role : Get Vector distrib | Ubuntu] *******************************
changed: [ubuntu_latest]

TASK [vector-role : Install Vector packages | CentOS] **************************
skipping: [ubuntu_latest]

TASK [vector-role : Install Vector packages | Ubuntu] **************************
changed: [ubuntu_latest]

TASK [vector-role : Deploy config Vector] **************************************
changed: [ubuntu_latest]

TASK [vector-role : Creates directory] *****************************************
changed: [ubuntu_latest]

TASK [vector-role : Create systemd unit Vector] ********************************
changed: [ubuntu_latest]

TASK [vector-role : Start Vector service] **************************************
skipping: [ubuntu_latest]

RUNNING HANDLER [vector-role : Start Vector service] ***************************
skipping: [ubuntu_latest]

PLAY RECAP *********************************************************************
ubuntu_latest          	: ok=6	changed=5	unreachable=0	failed=0	skipped=4	rescued=0	ignored=0

INFO 	Running ubuntu_latest > idempotence
[DEPRECATION WARNING]: Ansible will require Python 3.8 or newer on the
controller starting with Ansible 2.12. Current version: 3.7.3 (default, Oct 11
2023, 09:51:27) [GCC 8.3.0]. This feature will be removed from ansible-core in
version 2.12. Deprecation warnings can be disabled by setting
deprecation_warnings=False in ansible.cfg.

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
ok: [ubuntu_latest]

TASK [Include vector-role] *****************************************************

TASK [vector-role : Get Vector distrib | CentOS] *******************************
skipping: [ubuntu_latest]

TASK [vector-role : Get Vector distrib | Ubuntu] *******************************
ok: [ubuntu_latest]

TASK [vector-role : Install Vector packages | CentOS] **************************
skipping: [ubuntu_latest]

TASK [vector-role : Install Vector packages | Ubuntu] **************************
ok: [ubuntu_latest]

TASK [vector-role : Deploy config Vector] **************************************
ok: [ubuntu_latest]

TASK [vector-role : Creates directory] *****************************************
ok: [ubuntu_latest]

TASK [vector-role : Create systemd unit Vector] ********************************
ok: [ubuntu_latest]

TASK [vector-role : Start Vector service] **************************************
skipping: [ubuntu_latest]

PLAY RECAP *********************************************************************
ubuntu_latest          	: ok=6	changed=0	unreachable=0	failed=0	skipped=3	rescued=0	ignored=0

INFO 	Idempotence completed successfully.
INFO 	Running ubuntu_latest > side_effect
WARNING  Skipping, side effect playbook not configured.
INFO 	Running ubuntu_latest > verify
INFO 	Running Ansible Verifier
[DEPRECATION WARNING]: Ansible will require Python 3.8 or newer on the
controller starting with Ansible 2.12. Current version: 3.7.3 (default, Oct 11
2023, 09:51:27) [GCC 8.3.0]. This feature will be removed from ansible-core in
version 2.12. Deprecation warnings can be disabled by setting
deprecation_warnings=False in ansible.cfg.

PLAY [Verify] ******************************************************************

TASK [Get Vector version] ******************************************************
ok: [ubuntu_latest]

TASK [Assert Vector instalation] ***********************************************
ok: [ubuntu_latest] => {
	"changed": false,
	"msg": "All assertions passed"
}

TASK [Validation Vector configuration] *****************************************
ok: [ubuntu_latest]

TASK [Assert Vector validate config] *******************************************
ok: [ubuntu_latest] => {
	"changed": false,
	"msg": "All assertions passed"
}

PLAY RECAP *********************************************************************
ubuntu_latest          	: ok=4	changed=0	unreachable=0	failed=0	skipped=0	rescued=0	ignored=0

INFO 	Verifier completed successfully.
INFO 	Running ubuntu_latest > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO 	Running ubuntu_latest > destroy
[DEPRECATION WARNING]: Ansible will require Python 3.8 or newer on the
controller starting with Ansible 2.12. Current version: 3.7.3 (default, Oct 11
2023, 09:51:27) [GCC 8.3.0]. This feature will be removed from ansible-core in
version 2.12. Deprecation warnings can be disabled by setting
deprecation_warnings=False in ansible.cfg.

PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item=ubuntu_latest)

TASK [Wait for instance(s) deletion to complete] *******************************
FAILED - RETRYING: Wait for instance(s) deletion to complete (300 retries left).
changed: [localhost] => (item=ubuntu_latest)

TASK [Delete docker networks(s)] ***********************************************

PLAY RECAP *********************************************************************
localhost              	: ok=2	changed=2	unreachable=0	failed=0	skipped=1	rescued=0	ignored=0

INFO 	Pruning extra files from scenario ephemeral directory
</details>



4. Добавьте несколько assert в verify.yml-файл для  проверки работоспособности vector-role (проверка, что конфиг валидный, проверка успешности запуска и др.). 
5. Запустите тестирование роли повторно и проверьте, что оно прошло успешно.
5. Добавьте новый тег на коммит с рабочим сценарием в соответствии с семантическим версионированием.

[tag-1.0.2](https://github.com/zatulik2606/vector-role/commit/c9873a49b1ffa9fdff0f597bbce8c5dd0b8153dc) 


### Tox

1. Добавьте в директорию с vector-role файлы из [директории](./example).
2. Запустите `docker run --privileged=True -v <path_to_repo>:/opt/vector-role -w /opt/vector-role -it aragast/netology:latest /bin/bash`, где path_to_repo — путь до корня репозитория с vector-role на вашей файловой системе.
3. Внутри контейнера выполните команду `tox`, посмотрите на вывод.
5. Создайте облегчённый сценарий для `molecule` с драйвером `molecule_podman`. Проверьте его на исполнимость.
6. Пропишите правильную команду в `tox.ini`, чтобы запускался облегчённый сценарий.

В виду того. что aragast/netology:latest , судя по описанию из домашнего задания не поддерживает podman, пришлось искать др. вараинты с Docker. 
<details><summary>Пример запуска tox через docker exec</summary>
root@debian:~/08-05/roles/vector-role# docker exec -it aragast sh -c 'tox'
py37-ansible210 installed: ansible==2.10.7,ansible-base==2.10.17,ansible-compat==1.0.0,ansible-lint==5.1.1,arrow==1.2.3,bcrypt==4.0.1,binaryornot==0.4.4,bracex==2.3.post1,cached-property==1.5.2,Cerberus==1.3.5,certifi==2023.7.22,cffi==1.15.1,chardet==5.2.0,charset-normalizer==3.3.1,click==8.1.7,click-help-colors==0.9.2,colorama==0.4.6,commonmark==0.9.1,cookiecutter==2.4.0,cryptography==41.0.5,distro==1.8.0,enrich==1.2.7,idna==3.4,importlib-metadata==6.7.0,Jinja2==3.1.2,jmespath==1.0.1,lxml==4.9.3,MarkupSafe==2.1.3,molecule==3.4.0,molecule-podman==1.0.1,packaging==23.2,paramiko==2.12.0,pathspec==0.11.2,pluggy==0.13.1,pycparser==2.21,Pygments==2.16.1,PyNaCl==1.5.0,python-dateutil==2.8.2,python-slugify==8.0.1,PyYAML==5.4.1,requests==2.31.0,rich==10.16.2,ruamel.yaml==0.18.3,ruamel.yaml.clib==0.2.8,selinux==0.2.1,six==1.16.0,subprocess-tee==0.3.5,tenacity==8.2.3,text-unidecode==1.3,typing_extensions==4.7.1,urllib3==2.0.7,wcmatch==8.4.1,yamllint==1.29.0,zipp==3.15.0
py37-ansible210 run-test-pre: PYTHONHASHSEED='3582063929'
py37-ansible210 run-test: commands[0] | molecule test -s podman --destroy always
INFO 	podman scenario test matrix: dependency, lint, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO 	Performing prerun...
INFO 	Guessed /opt/vector-role as project root directory
INFO 	Using /root/.cache/ansible-lint/b984a4/roles/somovaa.vector symlink to current repository in order to enable Ansible to find the role using its expected full name.
INFO 	Added ANSIBLE_ROLES_PATH=~/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles:/root/.cache/ansible-lint/b984a4/roles
INFO 	Running podman > dependency
WARNING  Skipping, dependency is disabled.
WARNING  Skipping, dependency is disabled.
INFO 	Running podman > lint
COMMAND: ansible-lint
yamllint .

Loading custom .yamllint config file, this extends our internal yamllint config.
INFO 	Running podman > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO 	Running podman > destroy
INFO 	Sanity checks: 'podman'

PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item={'capabilities': ['SYS_ADMIN'], 'command': '/usr/sbin/init', 'image': 'pycontribs/centos:7', 'name': 'centos_7', 'tmpfs': ['/run', '/tmp'], 'volumes': ['/sys/fs/cgroup:/sys/fs/cgroup:ro']})

TASK [Wait for instance(s) deletion to complete] *******************************
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '406639209438.2930', 'results_file': '/root/.ansible_async/406639209438.2930', 'changed': True, 'failed': False, 'item': {'capabilities': ['SYS_ADMIN'], 'command': '/usr/sbin/init', 'image': 'pycontribs/centos:7', 'name': 'centos_7', 'tmpfs': ['/run', '/tmp'], 'volumes': ['/sys/fs/cgroup:/sys/fs/cgroup:ro']}, 'ansible_loop_var': 'item'})

PLAY RECAP *********************************************************************
localhost              	: ok=2	changed=2	unreachable=0	failed=0	skipped=0	rescued=0	ignored=0

INFO 	Running podman > syntax

playbook: /opt/vector-role/molecule/podman/converge.yml
INFO 	Running podman > create

PLAY [Create] ******************************************************************

TASK [get podman executable path] **********************************************
ok: [localhost]

TASK [save path to executable as fact] *****************************************
ok: [localhost]

TASK [Log into a container registry] *******************************************
skipping: [localhost] => (item="centos_7 registry username: None specified")

TASK [Check presence of custom Dockerfiles] ************************************
ok: [localhost] => (item=Dockerfile: None specified)

TASK [Create Dockerfiles from image names] *************************************
changed: [localhost] => (item="Dockerfile: None specified; Image: pycontribs/centos:7")

TASK [Discover local Podman images] ********************************************
ok: [localhost] => (item=centos_7)

TASK [Build an Ansible compatible image] ***************************************
changed: [localhost] => (item=pycontribs/centos:7)

TASK [Determine the CMD directives] ********************************************
ok: [localhost] => (item="centos_7 command: /usr/sbin/init")

TASK [Remove possible pre-existing containers] *********************************
changed: [localhost]

TASK [Discover local podman networks] ******************************************
skipping: [localhost] => (item=centos_7: None specified)

TASK [Create podman network dedicated to this scenario] ************************
skipping: [localhost]

TASK [Create molecule instance(s)] *********************************************
changed: [localhost] => (item=centos_7)

TASK [Wait for instance(s) creation to complete] *******************************
FAILED - RETRYING: Wait for instance(s) creation to complete (300 retries left).
changed: [localhost] => (item=centos_7)

PLAY RECAP *********************************************************************
localhost              	: ok=10   changed=5	unreachable=0	failed=0	skipped=3	rescued=0	ignored=0

INFO 	Running podman > prepare
WARNING  Skipping, prepare playbook not configured.
INFO 	Running podman > converge

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
ok: [centos_7]

TASK [Include vector-role] *****************************************************

TASK [vector-role : Create a directory] ****************************************
changed: [centos_7]

TASK [vector-role : Download for apt] ******************************************
skipping: [centos_7]

TASK [vector-role : Install vector for apt] ************************************
skipping: [centos_7]

TASK [vector-role : Download for yum] ******************************************
changed: [centos_7]

TASK [vector-role : Install package for yum] ***********************************
changed: [centos_7]

TASK [vector-role : Download for dnf] ******************************************
skipping: [centos_7]

TASK [vector-role : Install package for dnf] ***********************************
skipping: [centos_7]

TASK [vector-role : Start vector service] **************************************
changed: [centos_7]

PLAY RECAP *********************************************************************
centos_7               	: ok=5	changed=4	unreachable=0	failed=0	skipped=4	rescued=0	ignored=0

INFO 	Running podman > idempotence

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
ok: [centos_7]

TASK [Include vector-role] *****************************************************

TASK [vector-role : Create a directory] ****************************************
ok: [centos_7]

TASK [vector-role : Download for apt] ******************************************
skipping: [centos_7]

TASK [vector-role : Install vector for apt] ************************************
skipping: [centos_7]

TASK [vector-role : Download for yum] ******************************************
ok: [centos_7]

TASK [vector-role : Install package for yum] ***********************************
ok: [centos_7]

TASK [vector-role : Download for dnf] ******************************************
skipping: [centos_7]

TASK [vector-role : Install package for dnf] ***********************************
skipping: [centos_7]

TASK [vector-role : Start vector service] **************************************
ok: [centos_7]

PLAY RECAP *********************************************************************
centos_7               	: ok=5	changed=0	unreachable=0	failed=0	skipped=4	rescued=0	ignored=0

INFO 	Idempotence completed successfully.
INFO 	Running podman > side_effect
WARNING  Skipping, side effect playbook not configured.
INFO 	Running podman > verify
INFO 	Running Ansible Verifier

PLAY [Verify] ******************************************************************

TASK [Example assertion] *******************************************************
ok: [centos_7] => {
	"changed": false,
	"msg": "All assertions passed"
}

PLAY RECAP *********************************************************************
centos_7               	: ok=1	changed=0	unreachable=0	failed=0	skipped=0	rescued=0	ignored=0

INFO 	Verifier completed successfully.
INFO 	Running podman > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO 	Running podman > destroy

PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item={'capabilities': ['SYS_ADMIN'], 'command': '/usr/sbin/init', 'image': 'pycontribs/centos:7', 'name': 'centos_7', 'tmpfs': ['/run', '/tmp'], 'volumes': ['/sys/fs/cgroup:/sys/fs/cgroup:ro']})

TASK [Wait for instance(s) deletion to complete] *******************************
FAILED - RETRYING: Wait for instance(s) deletion to complete (300 retries left).
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '78122612421.4840', 'results_file': '/root/.ansible_async/78122612421.4840', 'changed': True, 'failed': False, 'item': {'capabilities': ['SYS_ADMIN'], 'command': '/usr/sbin/init', 'image': 'pycontribs/centos:7', 'name': 'centos_7', 'tmpfs': ['/run', '/tmp'], 'volumes': ['/sys/fs/cgroup:/sys/fs/cgroup:ro']}, 'ansible_loop_var': 'item'})

PLAY RECAP *********************************************************************
localhost              	: ok=2	changed=2	unreachable=0	failed=0	skipped=0	rescued=0	ignored=0

INFO 	Pruning extra files from scenario ephemeral directory
__________________________________________________________________________ summary ___________________________________________________________________________
  py37-ansible210: commands succeeded
  congratulations :)


</details>


8. Запустите команду `tox`. Убедитесь, что всё отработало успешно.
9. Добавьте новый тег на коммит с рабочим сценарием в соответствии с семантическим версионированием.

[tag-1.0.3](https://github.com/zatulik2606/vector-role/commit/3fdd46e98dd287a42052389c8918105b69bf6371)

После выполнения у вас должно получится два сценария molecule и один tox.ini файл в репозитории. Не забудьте указать в ответе теги решений Tox и Molecule заданий. В качестве решения пришлите ссылку на  ваш репозиторий и скриншоты этапов выполнения задания.



[Molecule](https://github.com/zatulik2606/vector-role/commit/c9873a49b1ffa9fdff0f597bbce8c5dd0b8153dc)



[Tox](https://github.com/zatulik2606/vector-role/commit/3fdd46e98dd287a42052389c8918105b69bf6371)
