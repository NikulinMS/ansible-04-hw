# Домашнее задание к занятию "`Работа с roles`" - `Никулин Михаил Сергеевич`



#### 1. Создайте в старой версии playbook файл `requirements.yml` и заполните его содержимым:
[requirements.yml](playbook%2Frequirements.yml)

   ```yaml
   ---
     - src: git@github.com:AlexeySetevoi/ansible-clickhouse.git
       scm: git
       version: "1.13"
       name: clickhouse 
   ```

#### 2. При помощи `ansible-galaxy` скачайте себе эту роль.

```shell
root@nikulin:/home/nikulinn/Documents/mnt-homeworks/08-ansible-04-role/playbook# ansible-galaxy role install -p roles -r requirements.yml 
Starting galaxy role install process
- extracting clickhouse to /home/nikulinn/Documents/mnt-homeworks/08-ansible-04-role/playbook/roles/clickhouse
- clickhouse (1.13) was installed successfully
```

#### 3. Создайте новый каталог с ролью при помощи:
`ansible-galaxy role init vector-role`

`ansible-galaxy role init lighthouse-role`

```shell
root@nikulin:/home/nikulinn/Documents/mnt-homeworks/08-ansible-04-role/playbook#  cd roles/
root@nikulin:/home/nikulinn/Documents/mnt-homeworks/08-ansible-04-role/playbook/roles# ansible-galaxy role init vector-role
- Role vector-role was created successfully
root@nikulin:/home/nikulinn/Documents/mnt-homeworks/08-ansible-04-role/playbook/roles# ansible-galaxy role init lighthouse-role
- Role lighthouse-role was created successfully

```

#### 4. На основе tasks из старого playbook заполните новую role. Разнесите переменные между `vars` и `default`. 
#### 5. Перенести нужные шаблоны конфигов в `templates`.
#### 6. Опишите в `README.md` обе роли и их параметры. Пример качественной документации ansible role [по ссылке](https://github.com/cloudalchemy/ansible-prometheus).
#### 7. Повторите шаги 3–6 для LightHouse. Помните, что одна роль должна настраивать один продукт.

#### vector-role:

role: [vector-role/tasks/main.yml](playbook%2Froles%2Fvector-role%2Ftasks%2Fmain.yml)

handlers: [vector-role/handlers/main.yml](playbook%2Froles%2Fvector-role%2Fhandlers%2Fmain.yml)

шаблон `templates/vector/vector.yaml.j2` перемещен в каталог роли [vector-role/templates/vector.yaml.j2](playbook%2Froles%2Fvector-role%2Ftemplates%2Fvector.yaml.j2)

vars: [vector-role/vars/main.yml](playbook%2Froles%2Fvector-role%2Fvars%2Fmain.yml)

в переменные роли вынесены параметры `пользователь и его группа в ОС, - владелец ПО vector`, переопределение которых на других окружениях не требуется:  
```yaml
vector_os_user: "vector"
vector_os_group: "vector"
```
defaults: [vector-role/defaults/main.yml](playbook%2Froles%2Fvector-role%2Fdefaults%2Fmain.yml)

в переменные по умолчанию вынесены параметры `версия vector`, `архтектура ОС`, 
`временный рабочий каталог для размещения дистрибутива с vector`, `каталог кофигов для vector`, 
которые с большой вероятностью могут быть переопределены по мере изменения версии vector; рабочий каталог и каталог 
конфигов тоже может быть изменен на других окружениях.
```yaml
vector_version: "0.21.1"
vector_os_arh: "x86_64"
vector_workdir: "/tmp/vector"
vector_data_dir: "/var/lib/vector"
```

#### lighthouse-role: 

role: [lighthouse-role/tasks/main.yml](playbook/roles/lighthouse-role/tasks/main.yml)  
handlers: [lighthouse-role/handlers/main.yml](playbook/roles/lighthouse-role/handlers/main.yml)  
шаблон `templates/nginx/ligthouse.conf.j2` перемещен в каталог роли [lighthouse-role/templates/nginx/ligthouse.conf.j2](playbook/roles/lighthouse-role/templates/nginx/ligthouse.conf.j2)  
vars:  [lighthouse-role/vars/main.yml](playbook/roles/lighthouse-role/vars/main.yml)  
в переменные роли вынесены параметры `репозиторий с исходным кодом lighthouse`, `пакеты, обязательные к установке`, 
являющиеся неотъемлемой частью роли и поэтому не требующие возможности переопределения:  
```yaml
lighthouse_code_src: "https://github.com/VKCOM/lighthouse.git"
lighthouse_code_src_version: d701335
lighthouse_packages:
  - nginx
  - git
```
defaults:  [lighthouse-role/defaults/main.yml](playbook/roles/lighthouse-role/defaults/main.yml)  
в переменные по умолчанию вынесены параметры `каталог установки lighthouse`, `порт веб-сервера`, `имя конфига веб-сервера`, 
которые с большой вероятностью могут быть переопределены на других окружениях.
```yaml
lighthouse_data_dir: "/lighthouse/"
lighthouse_nginx_port: 8080
lighthouse_nginx_conf: "lighthouse.conf"
```


#### 8. Выложите все roles в репозитории. Проставьте теги, используя семантическую нумерацию. Добавьте roles в `requirements.yml` в playbook.

[lighthouse-role, tag: 1.0.0](git@github.com:NikulinMS/lighthouse-role.git)

[vector-role, tag: 1.0.0.](git@github.com:NikulinMS/vector-role.git)

[requirements.yml](playbook%2Frequirements.yml)

#### 9. Переработайте playbook на использование roles. Не забудьте про зависимости LightHouse и возможности совмещения `roles` с `tasks`.

[site.yml](playbook%2Fsite.yml)

Работа playbook и предварительное скачивание требуемых ролей, описанных в `requirements.yml`:

<details>
<summary>ansible-galaxy role install -p roles -r requirements.yml</summary>>

```shell
root@nikulin:/home/nikulinn/Documents/mnt-homeworks/08-ansible-04-role/playbook# ansible-galaxy role install -p roles -r requirements.yml 
Starting galaxy role install process
- extracting clickhouse to /home/nikulinn/Documents/mnt-homeworks/08-ansible-04-role/playbook/roles/clickhouse
- clickhouse (1.13) was installed successfully
- extracting lighthouse-role to /home/nikulinn/Documents/mnt-homeworks/08-ansible-04-role/playbook/roles/lighthouse-role
- lighthouse-role (1.0.0) was installed successfully
- extracting vector-role to /home/nikulinn/Documents/mnt-homeworks/08-ansible-04-role/playbook/roles/vector-role
- vector-role (1.0.0) was installed successfully
root@nikulin:/home/nikulinn/Documents/mnt-homeworks/08-ansible-04-role/playbook# 

```
</details>

<details>
<summary>ansible-playbook -i inventory/prod.yml site.yml</summary>

```shell
root@nikulin:/home/nikulinn/Documents/mnt-homeworks/08-ansible-04-role/playbook# ansible-playbook -i inventory/prod.yml site.yml 

PLAY [Install Clickhouse] **********************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [clickhouse : Include OS Family Specific Variables] ***************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [clickhouse : include_tasks] **************************************************************************************************************************************************************************************************
included: /home/nikulinn/Documents/mnt-homeworks/08-ansible-04-role/playbook/roles/clickhouse/tasks/precheck.yml for clickhouse-01

TASK [clickhouse : Requirements check | Checking sse4_2 support] *******************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [clickhouse : Requirements check | Not supported distribution && release] *****************************************************************************************************************************************************
skipping: [clickhouse-01]

TASK [clickhouse : include_tasks] **************************************************************************************************************************************************************************************************
included: /home/nikulinn/Documents/mnt-homeworks/08-ansible-04-role/playbook/roles/clickhouse/tasks/params.yml for clickhouse-01

TASK [clickhouse : Set clickhouse_service_enable] **********************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [clickhouse : Set clickhouse_service_ensure] **********************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [clickhouse : include_tasks] **************************************************************************************************************************************************************************************************
included: /home/nikulinn/Documents/mnt-homeworks/08-ansible-04-role/playbook/roles/clickhouse/tasks/install/apt.yml for clickhouse-01

TASK [clickhouse : Install by APT | Apt-key add repo key] **************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [clickhouse : Install by APT | Remove old repo] *******************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [clickhouse : Install by APT | Repo installation] *****************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [clickhouse : Install by APT | Package installation] **************************************************************************************************************************************************************************
skipping: [clickhouse-01]

TASK [clickhouse : Install by APT | Package installation] **************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [clickhouse : Hold specified version during APT upgrade | Package installation] ***********************************************************************************************************************************************
changed: [clickhouse-01] => (item=clickhouse-client)
changed: [clickhouse-01] => (item=clickhouse-server)
changed: [clickhouse-01] => (item=clickhouse-common-static)

TASK [clickhouse : include_tasks] **************************************************************************************************************************************************************************************************
included: /home/nikulinn/Documents/mnt-homeworks/08-ansible-04-role/playbook/roles/clickhouse/tasks/configure/sys.yml for clickhouse-01

TASK [clickhouse : Check clickhouse config, data and logs] *************************************************************************************************************************************************************************
ok: [clickhouse-01] => (item=/var/log/clickhouse-server)
changed: [clickhouse-01] => (item=/etc/clickhouse-server)
changed: [clickhouse-01] => (item=/var/lib/clickhouse/tmp/)
changed: [clickhouse-01] => (item=/var/lib/clickhouse/)

TASK [clickhouse : Config | Create config.d folder] ********************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [clickhouse : Config | Create users.d folder] *********************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [clickhouse : Config | Generate system config] ********************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [clickhouse : Config | Generate users config] *********************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [clickhouse : Config | Generate remote_servers config] ************************************************************************************************************************************************************************
skipping: [clickhouse-01]

TASK [clickhouse : Config | Generate macros config] ********************************************************************************************************************************************************************************
skipping: [clickhouse-01]

TASK [clickhouse : Config | Generate zookeeper servers config] *********************************************************************************************************************************************************************
skipping: [clickhouse-01]

TASK [clickhouse : Config | Fix interserver_http_port and intersever_https_port collision] *****************************************************************************************************************************************
skipping: [clickhouse-01]

TASK [clickhouse : Notify Handlers Now] ********************************************************************************************************************************************************************************************

RUNNING HANDLER [clickhouse : Restart Clickhouse Service] **************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [clickhouse : include_tasks] **************************************************************************************************************************************************************************************************
included: /home/nikulinn/Documents/mnt-homeworks/08-ansible-04-role/playbook/roles/clickhouse/tasks/service.yml for clickhouse-01

TASK [clickhouse : Ensure clickhouse-server.service is enabled: True and state: restarted] *****************************************************************************************************************************************
changed: [clickhouse-01]

TASK [clickhouse : Wait for Clickhouse Server to Become Ready] *********************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [clickhouse : include_tasks] **************************************************************************************************************************************************************************************************
included: /home/nikulinn/Documents/mnt-homeworks/08-ansible-04-role/playbook/roles/clickhouse/tasks/configure/db.yml for clickhouse-01

TASK [clickhouse : Set ClickHose Connection String] ********************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [clickhouse : Gather list of existing databases] ******************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [clickhouse : Config | Delete database config] ********************************************************************************************************************************************************************************
skipping: [clickhouse-01]

TASK [clickhouse : Config | Create database config] ********************************************************************************************************************************************************************************
skipping: [clickhouse-01]

TASK [clickhouse : include_tasks] **************************************************************************************************************************************************************************************************
included: /home/nikulinn/Documents/mnt-homeworks/08-ansible-04-role/playbook/roles/clickhouse/tasks/configure/dict.yml for clickhouse-01

TASK [clickhouse : Config | Generate dictionary config] ****************************************************************************************************************************************************************************
skipping: [clickhouse-01]

TASK [clickhouse : include_tasks] **************************************************************************************************************************************************************************************************
skipping: [clickhouse-01]

PLAY [Install Vector] **************************************************************************************************************************************************************************************************************

TASK [vector-role : Create vector work directory] **********************************************************************************************************************************************************************************
changed: [vector-01]

TASK [vector-role : Get Vector distrib] ********************************************************************************************************************************************************************************************
changed: [vector-01]

TASK [vector-role : Unzip Vector archive] ******************************************************************************************************************************************************************************************
changed: [vector-01]

TASK [vector-role : Install Vector binary] *****************************************************************************************************************************************************************************************
changed: [vector-01]

TASK [vector-role : Check Vector installation] *************************************************************************************************************************************************************************************
changed: [vector-01]

TASK [vector-role : Create Vector etc directory] ***********************************************************************************************************************************************************************************
changed: [vector-01]

TASK [vector-role : Create Vector config vector.yaml] ******************************************************************************************************************************************************************************
changed: [vector-01]

TASK [vector-role : Create vector.service daemon] **********************************************************************************************************************************************************************************
changed: [vector-01]

TASK [vector-role : Modify Vector.service file ExecStart] **************************************************************************************************************************************************************************
changed: [vector-01]

TASK [vector-role : Modify Vector.service file ExecStartPre] ***********************************************************************************************************************************************************************
changed: [vector-01]

TASK [vector-role : Create user vector] ********************************************************************************************************************************************************************************************
changed: [vector-01]

TASK [vector-role : Create Vector data_dir] ****************************************************************************************************************************************************************************************
changed: [vector-01]

RUNNING HANDLER [vector-role : Start Vector service] *******************************************************************************************************************************************************************************
changed: [vector-01]

PLAY [Lighthouse] ******************************************************************************************************************************************************************************************************************

TASK [lighthouse-role : Pre-install Nginx & Git client] ****************************************************************************************************************************************************************************
changed: [lighthouse-01]

TASK [lighthouse-role : Clone Lighthouse source code by Git] ***********************************************************************************************************************************************************************
changed: [lighthouse-01]

TASK [lighthouse-role : Prepare nginx config] **************************************************************************************************************************************************************************************
changed: [lighthouse-01]

RUNNING HANDLER [lighthouse-role : Start Lighthouse service] ***********************************************************************************************************************************************************************
changed: [lighthouse-01]

PLAY RECAP *************************************************************************************************************************************************************************************************************************
clickhouse-01              : ok=27   changed=10   unreachable=0    failed=0    skipped=10   rescued=0    ignored=0   
lighthouse-01              : ok=4    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vector-01                  : ok=13   changed=13   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

</details>

#### 10. Выложите playbook в репозиторий.
#### 11. В ответе дайте ссылки на оба репозитория с roles и одну ссылку на репозиторий с playbook.

---