# Справочник диагностических команд Ansible для Windows

Перечень команд и модулей для проверки состояния системы, управления пакетами и диагностики соединений.

---

## 1. Проверка соединения и окружения

### Базовая проверка связи
*   **Модуль `win_ping`:** `ansible win_servers -m win_ping`
    *   *Результат:* `pong` означает корректную настройку WinRM и наличие прав доступа.

### Проверка системных путей (PATH)
*   **Поиск исполняемого файла:** `ansible win_servers -m win_command -a "where.exe <name>"`
    *   *Пример:* `where.exe git` — вернет полный путь к файлу, если он зарегистрирован в системе.

---

## 2. Управление пакетами (ПО)

### Chocolatey (Менеджер пакетов)
*   **Установка Chocolatey:** `ansible win_servers -m win_chocolatey_bootstrap`
*   **Установка программы:** `ansible win_servers -m win_chocolatey -a "name=git state=present"`
*   **Удаление программы (с зависимостями):** `ansible win_servers -m win_chocolatey -a "name=git state=absent remove_dependencies=yes"`
*   **Удаление конкретного инсталлятора (при ошибках метапакета):** `ansible win_servers -m win_shell -a "choco uninstall git.install -y"`

### WinGet (Официальный менеджер Microsoft)
*   **Тихая установка:** `ansible win_servers -m win_shell -a "winget install --id Git.Git --silent --accept-source-agreements"`
*   **Тихое удаление:** `ansible win_servers -m win_shell -a "winget uninstall --id Git.Git --silent"`

---

## 3. Диагностика установленного ПО

### Поиск в списке программ (PowerShell)
*   **Поиск по маске:** `ansible win_servers -m win_shell -a "Get-Package -Name '*Chrome*'"`
*   **Список из реестра Uninstall:**
    ```bash
    ansible win_servers -m win_shell -a "Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* | Select-Object DisplayName, DisplayVersion"
    ```

### Проверка наличия файла/папки
*   **Модуль `win_stat`:** `ansible win_servers -m win_stat -a "path='C:\Program Files\Git'"`
    *   *Ключевое поле:* `stat.exists` (True/False).

---

## 4. Управление процессами и службами

### Завершение зависших процессов
*   **Принудительное закрытие:** `ansible win_servers -m win_shell -a "taskkill /F /IM <process_name>.exe /T"`
    *   *Пример:* `taskkill /F /IM git.exe /T` (используется перед удалением заблокированных программ).

### Управление службами (Services)
*   **Проверка статуса:** `ansible win_servers -m win_service -a "name=Spooler"`
*   **Перезапуск службы:** `ansible win_servers -m win_service -a "name=winrm state=restarted"`

---

## 5. Типовые нюансы (Quick Tips)

1.  **Метапакеты в Choco:** Если `name=git` не удаляется, удаляйте `name=git.install`.
2.  **Повышение прав:** Если команда требует прав админа, добавьте ключи `--become --become-method=runas`.
3.  **Неинтерактивность:** Всегда используйте флаги `-y` (для choco) или `--silent` (для winget), так как Ansible не может нажать "ОК" в окне Windows.
