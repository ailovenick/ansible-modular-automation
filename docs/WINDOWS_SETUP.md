# Справочник конфигурации Windows для Ansible (WinRM)

Техническая спецификация параметров и состояний системы для обеспечения удаленного управления.

---

## 1. Параметры Windows-хоста (WinRM)

### Сетевые интерфейсы и порты
*   **Протокол HTTP (незащищенный):** TCP 5985
*   **Протокол HTTPS (защищенный):** TCP 5986 (Рекомендуемый стандарт)
*   **Тип трафика:** Входящий (Inbound)

### Требуемое состояние службы WinRM
*   **Служба (Service):** `WinRM` (Windows Remote Management)
*   **Статус:** `Running`
*   **Тип запуска:** `Automatic`
*   **Слушатели (Listeners):** Наличие активных слушателей на портах 5985/5986.

### Скрипт автоматизированной подготовки
*   **Ресурс:** [ConfigureRemotingForAnsible.ps1](https://raw.githubusercontent.com/ansible/ansible-documentation/devel/examples/scripts/ConfigureRemotingForAnsible.ps1)
*   **Функции:** Включение WinRM, создание самоподписанного сертификата, настройка правил брандмауэра.

---

## 2. Спецификация Linux-контроллера

### Программные зависимости
*   **Интерпретатор:** Python 3.x
*   **Базовая библиотека:** `pywinrm >= 0.3.0`
*   **Расширение для Kerberos:** `pywinrm[kerberos]` (необходимо для доменной аутентификации)

### Системные зависимости (для Kerberos)
*   **Пакеты (Debian/Ubuntu):** `python3-dev`, `libkrb5-dev`, `krb5-user`
*   **Метод установки:** `pip install "pywinrm[kerberos]>=0.3.0"`

---

## 3. Переменные инвентаризации (Inventory Variables)

### Сценарий А: Локальная аутентификация (Workgroup / NTLM)
```ini
[win_local:vars]
ansible_connection=winrm
ansible_port=5986
ansible_winrm_transport=ntlm
ansible_winrm_server_cert_validation=ignore
ansible_user=admin
# Метод повышения привилегий (sudo не поддерживается)
ansible_become_method=runas
```

### Сценарий Б: Доменная аутентификация (Active Directory / Kerberos)
```ini
[win_domain:vars]
ansible_connection=winrm
ansible_port=5986
ansible_winrm_transport=kerberos
ansible_winrm_server_cert_validation=ignore
# Формат пользователя: user@DOMAIN.COM (верхний регистр обязателен)
ansible_user=username@DOMAIN.COM
# Область Kerberos (Realms)
ansible_winrm_realm=DOMAIN.COM
# Метод повышения привилегий (sudo не поддерживается)
ansible_become_method=runas
```

---

## 4. Диагностические команды

### Проверка сетевой доступности (из Linux)
*   **Проверка порта:** `nc -zvw 5 <IP_HOST> 5986`
*   **Проверка модуля Ansible:** `ansible <HOST_GROUP> -m win_ping`

### Типовые ошибки и причины
*   **401 Unauthorized:** Несоответствие учетных данных или метода аутентификации (`ntlm`/`kerberos`).
*   **Connection Timeout:** Блокировка порта брандмауэром или отсутствие слушателя WinRM.
*   **SSL Certificate Error:** Отсутствие параметра `ansible_winrm_server_cert_validation=ignore`.
*   **Kerberos Auth Failure:** Ошибки конфигурации `/etc/krb5.conf` или неверный регистр имени домена.

---

## 5. Методология и стандарты внедрения

### Сценарии настройки WinRM
*   **Официальный скрипт (`ConfigureRemotingForAnsible.ps1`):** Отраслевой стандарт для изолированных хостов, тестовых сред и подготовки эталонных образов. Обеспечивает конфигурацию `LocalAccountTokenFilterPolicy` для работы локальных администраторов.
*   **Групповые политики (GPO):** Корпоративный стандарт для сред Active Directory. Используется для централизованного управления настройками WinRM на уровне домена.
*   **Инфраструктура как код (IaC):** Интеграция скрипта в блоки `user_data` (Cloud-Init, Terraform).

### Техническое обоснование автоматизации
1.  **Безопасность:** Автоматическая генерация и привязка SSL-сертификатов (HTTPS).
2.  **Привилегии:** Настройка реестра для получения административных прав в удаленных сессиях.
3.  **Воспроизводимость:** Исключение ошибок ручного ввода в конфигурации брандмауэра.
