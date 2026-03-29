# ansible-modular-automation


Наглядный пример архитектуры проекта Ansible для одновременного выполнения плейбуков на хостах с разными операционными системами (Linux и Windows).

## 🌿 Версионирование и Архитектура (Ветви)

Данный репозиторий демонстрирует два подхода к организации Ansible-кода, разделенные по веткам:

> ### 📍 **Текущая архитектура (Ветка):** `tasks`
*   **`tasks`**: Линейная модульная архитектура на основе `include_tasks`. Отлично подходит для простых проектов, где важна прозрачность и чтение "сверху-вниз".
*   **`roles`**: Продвинутая архитектура на базе **Ansible Roles**. Соответствует (Best Practices) и предназначена для масштабируемых, переиспользуемых инфраструктур.

## 📁 Структура проекта

```text
.
├── site.yaml               # Главная точка входа (агрегатор плейбуков)
├── ansible.cfg             # Конфигурация Ansible
├── playbooks/              # Сценарии и логика состояний
│   ├── linux.yml           # Состояние для Linux-серверов
│   ├── windows.yml         # Состояние для Windows-серверов
│   └── tasks/              # Модульные компоненты (Tasks)
│       ├── linux/          # Задачи для Linux (ufw, apt, dns...)
│       └── windows/        # Задачи для Windows (firewall, chocolatey...)
├── inventories/            # Реестры целевых узлов
│   └── group_vars/all/     # Глобальные переменные и секреты (vault)
└── docs/                   # Справочная документация
```

## 🚀 Порядок развертывания

### 1. Подготовка локальной среды (Python VENV)
Для корректной работы Ansible и его модулей рекомендуется использовать изолированное виртуальное окружение:

**Обновление системы и системных зависимостей (Debian/Ubuntu):**
```bash
sudo apt update && sudo apt upgrade -y
# Установка необходимых компонентов сборки (если возникают ошибки компиляции)
sudo apt install -y build-essential libssl-dev libffi-dev python3-dev 
```

**Создание и активация виртуального окружения:**
```bash
python3 -m venv .venv
source .venv/bin/activate
```

**Установка Ansible и дополнительных библиотек:**
```bash
# Обновление базовых инструментов pip
pip install --upgrade pip setuptools wheel
# Установка Ansible и библиотеки для работы pywinrm(windows), passlib(для паролей)
pip install ansible pywinrm passlib
```

### 2. Управление секретами (Ansible Vault)
Порядок работы с зашифрованными данными описан в [Руководстве по Vault](docs/VAULT_GUIDE.md).
Шифрование файла секретов:
```bash
ansible-vault encrypt inventories/group_vars/all/vault.yml
```

### 3. Исполнение сценариев
```bash
# Проверка доступности узлов
ansible all -m ping --ask-vault-pass

# Применение конфигурации ко всей инфраструктуре
ansible-playbook site.yaml --ask-vault-pass
```

## 📚 Справочная информация
Технические спецификации в директории `docs/`:
*   [Конфигурация Windows (WinRM)](docs/WINDOWS_SETUP.md)
*   [Диагностика и контроль Windows](docs/WINDOWS_TESTING.md)
*   [Диагностика и контроль Linux](docs/LINUX_TESTING.md)
*   [Методология защиты данных (Vault)](docs/VAULT_GUIDE.md)

## 🔗 Официальные ресурсы Ansible
Внешние источники для глубокого изучения и поиска модулей:
*   [Ansible Documentation](https://docs.ansible.com/ansible/latest/index.html) — Основной портал документации.
*   [Ansible Module Index](https://docs.ansible.com/ansible/latest/collections/index.html) — Полный список доступных коллекций и модулей.
*   [Ansible for Windows Guide](https://docs.ansible.com/ansible/latest/os_guide/windows_setup.html) — Специфика автоматизации Windows-систем.
*   [Ansible Vault Guide](https://docs.ansible.com/ansible/latest/user_guide/vault.html) — Углубленное изучение работы с секретами и шифрованием.
