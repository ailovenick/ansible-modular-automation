# Методология защиты данных с Ansible Vault

Использование **Ansible Vault** для безопасного хранения конфиденциальной информации.

## 1. Процедура шифрования данных
Шифрование файла в корне проекта:
```bash
ansible-vault encrypt group_vars/all/vault.yml
```
Требуется ввод **Vault Password** (мастер-пароль защиты).

## 2. Редактирование защищенных данных
Изменение значений переменных в зашифрованном файле:
```bash
ansible-vault edit group_vars/all/vault.yml
```
Снятие мастер пароля:
```bash
ansible-vault decrypt group_vars/all/vault.yml
```

## 3. Исполнение сценариев с зашифрованными данными
Порядок запуска плейбуков с использованием флага `--ask-vault-pass`:

```bash
# Применение ко всей инфраструктуре
ansible-playbook -i inventories/inventory.yaml site.yaml --ask-vault-pass

# Применение к сегменту Linux
ansible-playbook -i inventories/inventory.yaml playbooks/linux.yml --ask-vault-pass

# Применение к сегменту Windows
ansible-playbook -i inventories/inventory.yaml playbooks/windows.yml --ask-vault-pass
```

## 4. Преимущества подхода
*   Отсутствие паролей в открытом виде в системе контроля версий.
*   Централизованное управление секретами через единую переменную.
*   Единообразие процесса смены паролей (правка одного файла).
