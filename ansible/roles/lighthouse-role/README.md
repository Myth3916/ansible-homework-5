# Lighthouse Role

Роль для установки и настройки [Lighthouse](https://github.com/VKCOM/lighthouse) — веб-интерфейса для ClickHouse.

## Требования

- OS: Debian/Ubuntu
- Ansible >= 2.9

## Зависимости

- Nginx (устанавливается автоматически)

## Параметры роли

| Переменная | Описание | Значение по умолчанию |
|---|---|---|
| `lighthouse_repo` | URL репозитория Lighthouse | `https://github.com/VKCOM/lighthouse` |
| `lighthouse_dir` | Директория для размещения файлов | `/var/www/lighthouse` |

## Пример использования

```yaml
- hosts: lighthouse
  roles:
    - role: lighthouse-role
      lighthouse_dir: "/opt/lighthouse"
```

## Лицензия

MIT
