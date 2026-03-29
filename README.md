# ansible-modular-automation

Наглядный пример архитектуры проекта Ansible для одновременного выполнения плейбуков на хостах с разными операционными системами (Linux и Windows).

## 🌿 Версионирование и Архитектура (Ветви)

Данный репозиторий демонстрирует два подхода к организации Ansible-кода, разделенные по веткам:

> ### 📍 **Текущая архитектура (Ветка):** `roles`
*   **`tasks`**: Линейная модульная архитектура на основе `include_tasks`. Отлично подходит для простых проектов, где важна прозрачность и чтение "сверху-вниз".
*   **`roles`**: Продвинутая архитектура на базе **Ansible Roles**. Соответствует (Best Practices) и предназначена для масштабируемых, переиспользуемых инфраструктур.

## 📁 Структура проекта

```text
.
├── site.yaml               # Главная точка входа (агрегатор плейбуков)
├── ansible.cfg             # Конфигурация Ansible
├── roles/                  # Директория ролей (логика состояний)
│   ├── linux_common/       # Роль для базовой настройки Linux
│   ├── windows_common/     # Роль для базовой настройки Windows
│   └── new_role_name/      # Шаблон новой роли (пустая заготовка)
├── playbooks/              # Сценарии вызова ролей
│   ├── linux.yml           # Применение ролей для Linux-серверов
│   └── windows.yml         # Применение ролей для Windows-серверов
├── inventories/            # Реестры целевых узлов
│   └── group_vars/all/     # Глобальные переменные и секреты (vault)
└── docs/                   # Справочная документация
```

## 🚀 Порядок развертывания

### 1. Подготовка локальной среды (Python VENV)
Для корректной работы Ansible рекомендуется использовать изолированное виртуальное окружение:

**Обновление системы и зависимостей (Debian/Ubuntu):**
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y build-essential libssl-dev libffi-dev python3-dev 
```

**Создание и активация виртуального окружения:**
```bash
python3 -m venv .venv
source .venv/bin/activate
```

**Установка Ansible и необходимых библиотек:**
```bash
pip install --upgrade pip setuptools wheel
pip install ansible pywinrm passlib
```

### 2. Инициализация и установка ролей
Для расширения функционала можно использовать готовые роли из сообщества (Ansible Galaxy) или создавать свои.

**Создание новой локальной роли:**
```bash
# Инициализация структуры папок для новой роли
ansible-galaxy role init roles/new_role_name
```

**Установка сторонней роли:**
```bash
# Установка готовой роли в директорию roles/
ansible-galaxy role install geerlingguy.nginx -p roles/
```

**Где хранятся роли:**
*   **`roles/`**: Место для локальных ролей (`linux_common`, `windows_common`) и загруженных сторонних ролей.

### 3. Управление секретами (Ansible Vault)
Порядок работы с зашифрованными данными описан в [Руководстве по Vault](docs/VAULT_GUIDE.md).
Шифрование файла секретов:
```bash
ansible-vault encrypt inventories/group_vars/all/vault.yml
```

### 4. Исполнение сценариев
```bash
# Проверка доступности узлов
ansible all -m ping --ask-vault-pass

# Применение конфигурации ко всей инфраструктуре
ansible-playbook site.yaml --ask-vault-pass
```

## 🛠 Управление состоянием (Lifecycle Management)

В проекте реализована поддержка жизненного цикла ресурсов через единую переменную `infra_state`. Это позволяет не только разворачивать инфраструктуру, но и корректно удалять её.

### Переменная `infra_state`
По умолчанию переменная имеет значение `present`. Она управляет состоянием пакетов, пользователей, сервисов и фаервола.

*   **`present`** (по умолчанию): Установка и запуск всех компонентов.
*   **`absent`**: Полная очистка целевого узла (удаление пакетов, пользователей и остановка служб).

### Примеры использования

**Развертывание (стандартный запуск):**
```bash
ansible-playbook playbooks/linux.yml
```

**Полное удаление конфигурации с хостов:**
```bash
ansible-playbook playbooks/linux.yml -e "infra_state=absent"
```

### Логика переключения (Ternary Filter)
Для обеспечения корректной работы модулей, которые не поддерживают состояние `absent` (например, `service` или `ufw`), используется фильтр `ternary`. Это позволяет динамически подставлять нужные параметры:
*   Если `infra_state == 'absent'`, сервис переходит в `stopped`, а фаервол в `disabled`.
*   В противном случае ресурсы переходят в `started` и `enabled`.

## 📚 Справочная информация
Технические спецификации в директории `docs/`:
*   [Конфигурация Windows (WinRM)](docs/WINDOWS_SETUP.md)
*   [Диагностика и контроль Windows](docs/WINDOWS_TESTING.md)
*   [Диагностика и контроль Linux](docs/LINUX_TESTING.md)
*   [Методология защиты данных (Vault)](docs/VAULT_GUIDE.md)

## 🔗 Официальные ресурсы Ansible
*   [Ansible Roles Guide](https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse_roles.html) — Официальное руководство по ролям.
*   [Ansible Galaxy](https://galaxy.ansible.com/) — Хаб готовых ролей сообщества.
*   [Ansible for Windows Guide](https://docs.ansible.com/ansible/latest/os_guide/windows_setup.html) — Специфика автоматизации Windows.
