# Ansible Lab: Hybrid Infrastructure Management

Система управления гибридной инфраструктурой (Linux и Windows) на базе Ansible.

## 📁 Структура проекта

- **`site.yml`** — Точка входа конфигурации (агрегатор плейбуков).
- **`ansible.cfg`** — Параметры среды Ansible.
- **`playbooks/`** — Сценарии и логика состояний.
  - `linux.yml` — Описание состояния Linux-серверов.
  - `windows.yml` — Описание состояния Windows-серверов.
  - **`tasks/`** — Модульные компоненты (ПО, конфигурации, учетные записи, службы).
- **`inventories/`** — Реестры целевых узлов.
- **`group_vars/`** — Определение переменных и секретных данных.
- **`docs/`** — Справочная документация и технические руководства.

## 🚀 Порядок развертывания

### 1. Подготовка среды
Требования к установленному ПО и библиотекам:
```bash
pip install pywinrm
```

### 2. Управление секретами (Ansible Vault)
Порядок работы с зашифрованными данными описан в [Руководстве по Vault](docs/VAULT_GUIDE.md).
Шифрование файла секретов:
```bash
ansible-vault encrypt group_vars/all/vault.yml
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
