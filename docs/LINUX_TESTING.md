# Справочник диагностических команд Ansible для Linux

Перечень команд и модулей для проверки состояния системы, управления пакетами и диагностики соединений через SSH.

---

## 1. Проверка соединения и окружения

### Базовая проверка связи (ICMP + SSH)
*   **Модуль `ping`:** `ansible lin_servers -m ping`
    *   *Результат:* `pong` означает успешную SSH-авторизацию и наличие Python на хосте.

### Сбор системных фактов (Facts)
*   **Модуль `setup`:** `ansible lin_servers -m setup`
    *   *Результат:* Полный JSON со всеми данными о системе (IP, диски, версия ядра, дистрибутив).

### Проверка путей к утилитам
*   **Поиск исполняемого файла:** `ansible lin_servers -m command -a "which git"`
    *   *Результат:* Путь к файлу (например, `/usr/bin/git`).

---

## 2. Управление пакетами (ПО)

### APT (Ubuntu, Debian)
*   **Обновление кеша и установка:** `ansible lin_servers -m apt -a "name=git state=present update_cache=yes" --become`
*   **Удаление программы:** `ansible lin_servers -m apt -a "name=git state=absent" --become`

### YUM / DNF (CentOS, RHEL, Fedora)
*   **Установка:** `ansible lin_servers -m dnf -a "name=git state=present" --become`
*   **Удаление:** `ansible lin_servers -m dnf -a "name=git state=absent" --become`

### PIP (Python пакеты)
*   **Установка пакета:** `ansible lin_servers -m pip -a "name=requests state=present"`

---

## 3. Диагностика системы и файлов

### Проверка статуса сервиса (systemd)
*   **Статус службы:** `ansible lin_servers -m service -a "name=nginx"`
*   **Перезапуск службы:** `ansible lin_servers -m service -a "name=nginx state=restarted" --become`

### Проверка наличия файла/папки
*   **Модуль `stat`:** `ansible lin_servers -m stat -a "path=/etc/hosts"`
    *   *Ключевое поле:* `stat.exists` (True/False).

### Чтение логов (последние 10 строк)
*   **Команда:** `ansible lin_servers -m shell -a "tail -n 10 /var/log/syslog" --become`

---

## 4. Управление процессами и правами

### Завершение процесса
*   **Принудительное закрытие:** `ansible lin_servers -m shell -a "pkill -f <process_name>"`
    *   *Пример:* `pkill -f nginx`

### Изменение прав доступа
*   **Смена владельца и прав:** `ansible lin_servers -m file -a "path=/var/www owner=www-data group=www-data mode=0755" --become`

---

## 5. Типовые нюансы (Quick Tips)

1.  **Повышение привилегий:** Почти все системные команды (установка ПО, управление службами) требуют ключа `--become` (автоматически использует `sudo`).
2.  **Пароль sudo:** Если `ansible.cfg` не настроен на автоматический ввод, добавьте ключ `-K` (или `--ask-become-pass`) при запуске команды.
3.  **Shell vs Command:** Используйте `command` для простых задач и `shell`, если вам нужны пайпы (`|`), перенаправления (`>`) или переменные окружения.
