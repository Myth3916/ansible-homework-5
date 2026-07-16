# Ansible Домашнее задание 5 — Тестирование ролей с помощью Molecule и Tox
## Автор: Шаров Олег

Репозиторий содержит Ansible-роли с автоматизированным тестированием через Molecule (с драйвером Docker) и Tox (с драйвером Podman).

## Роли

### vector-role
Устанавливает и настраивает Vector (инструмент сбора данных) на нескольких дистрибутивах Linux.

**Поддерживаемые дистрибутивы:**
- RockyLinux 9
- Ubuntu 22.04

**Возможности:**
- Скачивание бинарника Vector из официального источника
- Создание конфигурации из шаблона
- Настройка systemd-сервиса
- Включает комплексные тесты Molecule с assert-проверками

## Часть 1: Molecule + Docker (тег v1.0.0)

### Запуск тестов

```bash
cd ansible/roles/vector-role
source ../../.venv/bin/activate
molecule test
```

### Что проверяется в тестах

- ✅ Наличие и исполняемость бинарника Vector
- ✅ Корректная версия Vector (0.34.1)
- ✅ Наличие конфигурационного файла `/etc/vector/vector.toml`
- ✅ Валидность конфигурации (через `vector validate --no-environment`)
- ✅ Активное состояние сервиса
- ✅ Сервис включён в автозагрузку

### Структура сценариев Molecule

- `molecule/default/` — основной сценарий тестирования с драйвером Docker
- `molecule/podman/` — облегчённый сценарий для Tox с драйвером Podman

## Часть 2: Tox + Podman (тег v1.1.0)

### Запуск тестов через Tox

Тестирование выполняется внутри специального Docker-контейнера `aragast/netology:latest`:

```bash
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

### Примечание о среде Docker-in-Docker

При запуске Tox внутри Docker-контейнера (Docker-in-Docker) инициализация systemd в тестовом контейнере занимает 2–4 минуты. Это проявляется в виде строк `FAILED - RETRYING: Wait for instance(s) creation to complete` в логе, но это **не ошибка**, а нормальный механизм ожидания Molecule. Тест успешно завершается с `congratulations :)`.

## Требования

- Python 3.x
- Docker
- Molecule с драйвером docker
- Ansible 2.9+
- Tox (для запуска в контейнере `aragast/netology:latest`)

## История версий

- **v1.0.0** — Molecule-тесты с драйвером Docker (RockyLinux 9 + Ubuntu 22.04)
- **v1.1.0** — Tox-тесты с драйвером Podman (py37-ansible210)
