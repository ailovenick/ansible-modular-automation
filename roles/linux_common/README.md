linux_common
============

Базовая роль для настройки Linux-серверов в гибридной инфраструктуре.

Requirements
------------

Отсутствуют специальные требования.

Role Variables
--------------

Доступные переменные (см. `defaults/main.yml`):
- `linux_packages`: Список пакетов для установки.
- `linux_user_name`: Имя основного системного пользователя.
- `linux_user_shell`: Оболочка пользователя.
- `linux_config_src`: Имя исходного файла конфигурации.
- `linux_config_dest`: Путь назначения конфигурации.

Dependencies
------------

Отсутствуют зависимости от других ролей.

Example Playbook
----------------

Пример использования роли:

    - hosts: servers
      roles:
         - role: linux_common

License
-------

MIT-0

Author Information
------------------

your name
