# Ansible Домашнее задание 5 — Тестирование ролей с помощью Molecule и Tox
## Автор: Шаров Олег

Репозиторий содержит Ansible-роли с автоматизированным тестированием через Molecule (с драйвером Docker) и Tox (с драйвером Podman).

## 🚀 Быстрый старт

```bash
# Клонировать репозиторий
git clone <repository-url>
cd ansible-homework-5

# Часть 1: Запуск Molecule-тестов
cd ansible/roles/vector-role
source ../../.venv/bin/activate
molecule test

# Часть 2: Запуск Tox-тестов (в Docker-контейнере)
docker run --privileged=true \
  -v $(pwd)/ansible/roles/vector-role:/opt/vector-role \
  -w /opt/vector-role \
  -it aragast/netology:latest /bin/bash
# Внутри контейнера: tox -e py37-ansible210
```

## 📦 Роли

### vector-role
Устанавливает и настраивает [Vector](https://vector.dev/) (инструмент сбора данных) на нескольких дистрибутивах Linux.

**Поддерживаемые дистрибутивы:**
- RockyLinux 9
- Ubuntu 22.04

**Возможности:**
- Скачивание бинарника Vector 0.34.1 из официального источника
- Создание конфигурации из шаблона (Jinja2)
- Настройка systemd-сервиса
- Создание необходимых директорий (`/etc/vector`, `/var/lib/vector`)
- Включает комплексные тесты Molecule с assert-проверками

---

## 🧪 Часть 1: Molecule + Docker (тег v1.0.0)

### Запуск тестов

```bash
cd ansible/roles/vector-role
source ../../.venv/bin/activate
molecule test
```

### Что проверяется в тестах

- ✅ Наличие и исполняемость бинарника Vector (`/usr/local/bin/vector`)
- ✅ Корректная версия Vector (0.34.1)
- ✅ Наличие конфигурационного файла `/etc/vector/vector.toml`
- ✅ Валидность конфигурации (через `vector validate --no-environment`)
- ✅ Активное состояние сервиса (`systemctl status vector`)
- ✅ Сервис включён в автозагрузку (`systemctl is-enabled vector`)

### Структура сценариев Molecule

- `molecule/default/` — основной сценарий тестирования с драйвером Docker
- `molecule/podman/` — облегчённый сценарий для Tox с драйвером Podman

---

## 🐍 Часть 2: Tox + Podman (тег v1.1.0)

### Запуск тестов через Tox

Тестирование выполняется внутри специального Docker-контейнера `aragast/netology:latest`:

```bash
# Из корня репозитория:
docker run --privileged=true \
  -v $(pwd)/ansible/roles/vector-role:/opt/vector-role \
  -w /opt/vector-role \
  -it aragast/netology:latest /bin/bash

# Внутри контейнера:
tox -e py37-ansible210
```

### Результат выполнения Tox

```
__________________________________________________________________________________________ summary __________________________________________________________________________________________
  py37-ansible210: commands succeeded
  congratulations :)
```

### ⚠️ Важное примечание о среде Docker-in-Docker

При запуске Tox внутри Docker-контейнера `aragast/netology:latest` (среда Docker-in-Docker) инициализация `systemd` внутри тестового контейнера Podman занимает **2–4 минуты**.

В логе это проявляется в виде многократных строк:
```
FAILED - RETRYING: Wait for instance(s) creation to complete (300 retries left).
FAILED - RETRYING: Wait for instance(s) creation to complete (299 retries left).
...
```

**Это НЕ является ошибкой!** Это штатный механизм ожидания (retry) со стороны Molecule. Тест успешно дожидается запуска контейнера и в итоге завершается со статусом `congratulations :)`.

---

## 📋 Требования

- Python 3.x
- Docker
- Molecule с драйвером docker
- Ansible 2.9+
- Tox (для запуска в контейнере `aragast/netology:latest`)

## 📝 История версий

- **v1.0.0** — Molecule-тесты с драйвером Docker (RockyLinux 9 + Ubuntu 22.04)
- **v1.1.0** — Tox-тесты с драйвером Podman (py37-ansible210)

## 📂 Структура проекта

```
ansible-homework-5/
├── README.md
├── .gitignore
└── ansible/
    └── roles/
        └── vector-role/
            ├── defaults/
            ├── handlers/
            │   └── main.yml
            ├── meta/
            │   └── main.yml
            ├── molecule/
            │   ├── default/          # Сценарий с Docker
            │   │   ├── molecule.yml
            │   │   ├── converge.yml
            │   │   ├── verify.yml
            │   │   ├── create.yml
            │   │   └── destroy.yml
            │   └── podman/           # Сценарий с Podman
            │       ├── molecule.yml
            │       ├── converge.yml
            │       └── verify.yml
            ├── tasks/
            │   └── main.yml
            ├── templates/
            │   └── vector.toml.j2
            ├── tox.ini
            ├── tox-requirements.txt
            └── requirements.yml
```
