# Домашнее задание к занятию 2 «Работа с Playbook»

## Подготовка к выполнению

1. * Необязательно. Изучите, что такое [ClickHouse](https://www.youtube.com/watch?v=fjTNS2zkeBs) и [Vector](https://www.youtube.com/watch?v=CgEhyffisLY).
2. Создайте свой публичный репозиторий на GitHub с произвольным именем или используйте старый.
3. Скачайте [Playbook](./playbook/) из репозитория с домашним заданием и перенесите его в свой репозиторий.
4. Подготовьте хосты в соответствии с группами из предподготовленного playbook.

## Основная часть

1. Подготовьте свой inventory-файл `prod.yml`.
2. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает [vector](https://vector.dev). Конфигурация vector должна деплоиться через template файл jinja2. От вас не требуется использовать все возможности шаблонизатора, просто вставьте стандартный конфиг в template файл. Информация по шаблонам по [ссылке](https://www.dmosk.ru/instruktions.php?object=ansible-nginx-install). не забудьте сделать handler на перезапуск vector в случае изменения конфигурации!
3. При создании tasks рекомендую использовать модули: `get_url`, `template`, `unarchive`, `file`.
4. Tasks должны: скачать дистрибутив нужной версии, выполнить распаковку в выбранную директорию, установить vector.

```bash
root@killakazzak:~/08-ansible-02-playbook-hw/playbook# ansible-playbook -i inventory/prod.yml site.yml 

PLAY [Install Clickhouse] ****************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Get Clickhouse distribution] *******************************************************************************************************************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 0, "group": "root", "item": "clickhouse-common-static", "mode": "0644", "msg": "Request failed", "owner": "root", "response": "HTTP Error 404: Not Found", "size": 246310036, "state": "file", "status_code": 404, "uid": 0, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get Clickhouse distribution fallback] **********************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Install Clickhouse packages] *******************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Flush handlers] ********************************************************************************************************************************************************************************************************************

TASK [Ensure Clickhouse service is running] **********************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Wait for ClickHouse to be ready] ***************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Update ClickHouse config to listen on all interfaces] ******************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Open TCP port 9000 in the firewall] ************************************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [Open TCP port 8123 in the firewall] ************************************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [Reload firewall] *******************************************************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [Create database] *******************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

PLAY [Install and configure Vector] ******************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Get the latest version of Vector] **************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Set vector_version variable] *******************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Download Vector distribution] ******************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Create directory for Vector installation] ******************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Extract Vector distribution] *******************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Check contents of Vector installation directory] ***********************************************************************************************************************************************************************************
changed: [clickhouse-01]

PLAY RECAP *******************************************************************************************************************************************************************************************************************************
clickhouse-01              : ok=17   changed=4    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0   
```
5. Запустите `ansible-lint site.yml` и исправьте ошибки, если они есть.

![image](https://github.com/user-attachments/assets/3c3f5ad5-6735-42da-b926-3f9d9fce7022)

6. Попробуйте запустить playbook на этом окружении с флагом `--check`.

```bash
ansible-playbook -i inventory/prod.yml site.yml --check
```

```
root@killakazzak:~/08-ansible-02-playbook-hw/playbook# ansible-playbook -i inventory/prod.yml site.yml --check

PLAY [Install Clickhouse] ****************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Download Clickhouse packages] ******************************************************************************************************************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 0, "group": "root", "item": "clickhouse-common-static", "mode": "0644", "msg": "Request failed", "owner": "root", "response": "HTTP Error 404: Not Found", "size": 246310036, "state": "file", "status_code": 404, "uid": 0, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get Clickhouse distribution fallback] **********************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Install Clickhouse packages] *******************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Flush handlers] ********************************************************************************************************************************************************************************************************************

TASK [Ensure Clickhouse service is running] **********************************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [Wait for ClickHouse to be ready] ***************************************************************************************************************************************************************************************************
skipping: [clickhouse-01]

TASK [Update ClickHouse config to listen on all interfaces] ******************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Open TCP port 9000 in the firewall] ************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Open TCP port 8123 in the firewall] ************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Create database] *******************************************************************************************************************************************************************************************************************
skipping: [clickhouse-01]

PLAY [Install and configure Vector] ******************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Set vector_version variable] *******************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Download Vector distribution] ******************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Create directory for Vector installation] ******************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Extract Vector distribution] *******************************************************************************************************************************************************************************************************
skipping: [clickhouse-01]

TASK [Check contents of Vector installation directory] ***********************************************************************************************************************************************************************************
skipping: [clickhouse-01]

PLAY RECAP *******************************************************************************************************************************************************************************************************************************
clickhouse-01              : ok=11   changed=1    unreachable=0    failed=0    skipped=4    rescued=1    ignored=0   
```
7. Запустите playbook на `prod.yml` окружении с флагом `--diff`. Убедитесь, что изменения на системе произведены.

```bash
ansible-playbook -i inventory/prod.yml site.yml --diff
```
```
root@killakazzak:~/08-ansible-02-playbook-hw/playbook# ansible-playbook -i inventory/prod.yml site.yml --diff

PLAY [Install Clickhouse] *********************************************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Download Clickhouse packages] ***********************************************************************************************************************************************************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 0, "group": "root", "item": "clickhouse-common-static", "mode": "0644", "msg": "Request failed", "owner": "root", "response": "HTTP Error 404: Not Found", "size": 246310036, "state": "file", "status_code": 404, "uid": 0, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get Clickhouse distribution fallback] ***************************************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Install Clickhouse packages] ************************************************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Flush handlers] *************************************************************************************************************************************************************************************************************************************************************

TASK [Ensure Clickhouse service is running] ***************************************************************************************************************************************************************************************************************************************
changed: [clickhouse-01]

TASK [Wait for ClickHouse to be ready] ********************************************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Update ClickHouse config to listen on all interfaces] ***********************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Open TCP port 9000 in the firewall] *****************************************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Open TCP port 8123 in the firewall] *****************************************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Create database] ************************************************************************************************************************************************************************************************************************************************************
changed: [clickhouse-01]

PLAY [Install and configure Vector] ***********************************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Set vector_version variable] ************************************************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Download Vector distribution] ***********************************************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Create directory for Vector installation] ***********************************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Extract Vector distribution] ************************************************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Check contents of Vector installation directory] ****************************************************************************************************************************************************************************************************************************
ok: [clickhouse-01]

PLAY RECAP ************************************************************************************************************************************************************************************************************************************************************************
clickhouse-01              : ok=15   changed=2    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0
```

8. Повторно запустите playbook с флагом `--diff` и убедитесь, что playbook идемпотентен.

```bash
ansible-playbook -i inventory/prod.yml site.yml --diff
```

![image](https://github.com/user-attachments/assets/f90d9efc-db5b-439b-a06c-9a27d92286ad)

9. Подготовьте README.md-файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги. Пример качественной документации ansible playbook по [ссылке](https://github.com/opensearch-project/ansible-playbook). Так же приложите скриншоты выполнения заданий №5-8

# README.md

## Описание

Данный Ansible playbook предназначен для установки и настройки ClickHouse и Vector на удалённых хостах, определённых в инвентарном файле. Playbook выполняет следующие основные задачи:

1. Устанавливает ClickHouse, загружая необходимые пакеты и настраивая сервис.
2. Обеспечивает, чтобы ClickHouse слушал на всех интерфейсах.
3. Открывает необходимые порты в файрволе.
4. Создаёт базу данных в ClickHouse.
5. Устанавливает и настраивает Vector, загружая его дистрибутив и распаковывая его в указанную директорию.

## Параметры

### Переменные

- `clickhouse_version`: Версия ClickHouse, которую необходимо установить.
- `clickhouse_packages`: Список пакетов ClickHouse, которые будут загружены.
- `clickhouse_host`: Хост, на котором будет создана база данных.
- `vector_install_dir`: Директория, в которую будет установлен Vector (по умолчанию `/opt/vector`).
- `vector_config_template`: Шаблон конфигурации для Vector (по умолчанию `vector_config.j2`).
- `vector_arch`: Архитектура системы (по умолчанию `x86_64`).

### Теги

Playbook не содержит явных тегов, но вы можете добавить их для управления выполнением отдельных задач. Например, вы можете использовать теги для установки ClickHouse или Vector отдельно.

## Использование

1. Убедитесь, что у вас установлен Ansible.
2. Настройте инвентарный файл, указав хосты, на которых будет выполняться playbook.
3. Установите необходимые переменные в вашем playbook или в инвентарном файле.
4. Запустите playbook с помощью следующей команды:

   ```bash
   ansible-playbook -i inventory_file playbook.yml
   ```

   Замените `inventory_file` на путь к вашему инвентарному файлу и `playbook.yml` на имя вашего playbook.

## Примечания

- Убедитесь, что у вас есть доступ к интернету на целевых хостах для загрузки пакетов.
- Проверьте, что файрвол на целевых хостах настроен правильно, чтобы разрешить доступ к необходимым портам (9000 и 8123).
- Вы можете настроить шаблон конфигурации Vector в соответствии с вашими требованиями, изменив файл `vector_config.j2`.

10. Готовый playbook выложите в свой репозиторий, поставьте тег `08-ansible-02-playbook` на фиксирующий коммит, в ответ предоставьте ссылку на него.
---

### Как оформить решение задания

Выполненное домашнее задание пришлите в виде ссылки на .md-файл в вашем репозитории.
---
