# Vector Role

Роль для установки и настройки [Vector](https://vector.dev/) — высокопроизводительного сборщика логов.

## Требования

- OS: Debian/Ubuntu
- Ansible >= 2.9

## Зависимости

Нет.

## Параметры роли

| Переменная | Описание | Значение по умолчанию |
|---|---|---|
| `vector_clickhouse_host` | Хост ClickHouse для отправки логов | `clickhouse` |
| `vector_clickhouse_port` | Порт ClickHouse | `8123` |
| `vector_clickhouse_database` | Имя базы данных | `logs` |
| `vector_clickhouse_table` | Имя таблицы | `vector_logs` |

## Пример использования

```yaml
- hosts: vector
  roles:
    - role: vector-role
      vector_clickhouse_host: "192.168.1.10"
```

## Лицензия

MIT
