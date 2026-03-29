windows_common
==============

Базовая роль для настройки Windows-серверов через WinRM и Chocolatey.

Requirements
------------

Требуется наличие WinRM на целевых хостах и установленный Chocolatey (устанавливается ролью).

Role Variables
--------------

Доступные переменные (см. `defaults/main.yml`):
- `windows_packages`: Список Chocolatey-пакетов для установки.
- `windows_user_name`: Имя создаваемого пользователя.
- `windows_user_comment`: Описание пользователя.

Dependencies
------------

Отсутствуют зависимости от других ролей.

Example Playbook
----------------

Пример использования роли:

    - hosts: win_servers
      roles:
         - role: windows_common

License
-------

MIT-0

Author Information
------------------

your name
