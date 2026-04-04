# 🛠 Ansible Modular Automation

Шаблон для модульной автоматизации инфраструктуры (Linux & Windows) с использованием ролей и выборочных сценариев.

## Архитектура проекта

Проект построен на **модульной архитектуре Ansible Roles**. Это позволяет повторно использовать код и легко масштабировать систему.

### Ключевые особенности:

* **Универсальность**: Поддержка Linux (Ubuntu/Debian) и Windows (WinRM).
* **Гибкость**: Возможность запускать роли целиком или выбирать конкретные задачи (tasks) через специализированные плейбуки.
* **Lifecycle Management**: Управление состоянием ресурсов (`present`/`absent`) через переменную `infra_state`.
* **Безопасность**: Интеграция с Ansible Vault для хранения секретов.

## 📁 Структура проекта

```text
.
├── site.yaml                 # Главный агрегатор (запуск всей инфраструктуры)
├── ansible.cfg               # Глобальные настройки Ansible
├── playbooks/                # Сценарии вызова
│   ├── linux.yml             # Полная роль для Linux
│   ├── windows.yml           # Полная роль для Windows
│   ├── linux_selective.yml   # ВЫБОРОЧНЫЕ задачи для Linux (конструктор)
│   └── windows_selective.yml # ВЫБОРОЧНЫЕ задачи для Windows (конструктор)
├── roles/                    # Библиотека ролей (логика)
│   ├── linux_common/         # Базовая настройка Linux
│   ├── windows_common/       # Базовая настройка Windows
│   └── new_role_name/        # Пример инициализации роли ansible-galaxy init
├── inventories/              # Реестры хостов (в разных форматах)
│   ├── inventory.ini         # Список серверов (классический формат)
│   ├── inventory.yaml        # Список серверов (современный YAML-формат)
│   └── group_vars/all/       # Глобальные переменные и секреты (vault)
└── docs/                     # Инструкции и справочные материалы
    ├── CODE_DETAILS.md       # РАЗБОР КОДА: переменные, блоки, логика
    ├── VAULT_GUIDE.md        # Работа с шифрованием данных (Ansible Vault)
    ├── LINUX_TESTING.md      # Тестирование и диагностика Linux-хостов
    ├── WINDOWS_SETUP.md      # Подготовка Windows (WinRM) к работе с Ansible
    └── WINDOWS_TESTING.md    # Тестирование и диагностика Windows-хостов
```

## 🚀 Быстрый старт

### 1. Подготовка среды

**Обновление системы и установка зависимостей (Debian/Ubuntu):**

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y build-essential libssl-dev libffi-dev python3-dev 
```

**Создание и активация виртуального окружения:**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

**Обновление инструментов сборки и установка Ansible:**

```bash
pip install --upgrade pip setuptools wheel
pip install ansible pywinrm passlib
```

### 2. Исполнение сценариев

**Полное развертывание всей инфраструктуры:**

```bash
ansible-playbook site.yaml --ask-vault-pass
```

**Выборочный запуск (только нужные компоненты):**

```bash
# Только установка пакетов и DNS для Linux
ansible-playbook playbooks/linux_selective.yml

# Только установка ПО и конфигов для Windows
ansible-playbook playbooks/windows_selective.yml
```

**Удаление конфигурации (Очистка хостов):**

```bash
ansible-playbook site.yaml -e "infra_state=absent"
```

## 📚 Документация проекта

* [Разбор логики кода (CODE_DETAILS)](docs/CODE_DETAILS.md) — Описание специфических переменных, логических блоков.
* [Инструкция по Vault](docs/VAULT_GUIDE.md) — Шифрование паролей ansible vault.
* [Настройка Windows](docs/WINDOWS_SETUP.md) — подготовка WinRM.
* [Диагностика Linux](docs/LINUX_TESTING.md) — проверка связи и прав.
* [Диагностика Windows](docs/WINDOWS_TESTING.md) — проверка WinRM.

## 🔗 Официальные ресурсы Ansible

* [Ansible Roles Guide](https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse_roles.html) — официальное руководство по ролям.
* [Ansible Galaxy](https://galaxy.ansible.com/) — хаб готовых ролей сообщества.
* [Ansible for Windows Guide](https://docs.ansible.com/ansible/latest/os_guide/windows_setup.html) — специфика автоматизации Windows.
