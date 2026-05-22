# Zabbix Templates & Grafana Dashboards: Terminal Infrastructure Monitoring

Комплект решений для мониторинга и планирования ресурсов терминальных серверов (RDS) и ферм (RDSH Collections). Включает шаблоны для **Zabbix 7.4** и специализированные дашборды-калькуляторы для **Grafana**.

Решение разделено на два уровня абстракции:
1.  **Terminal Servers** — мониторинг отдельных хостов.
2.  **Terminal Farms** — мониторинг коллекций/ферм (агрегированные данные).

<img width="1579" height="776" alt="image" src="https://github.com/user-attachments/assets/e0009fb8-c5d8-4788-807e-8858020f9bb5" />

## 📂 Состав репозитория

### Шаблоны Zabbix
1.  **`template_terminal_servers.yaml`** — Шаблон для сбора метрик с отдельных терминальных серверов.
2.  **`template_terminal_farms.yaml`** — Шаблон для сбора метрик с терминальных ферм (коллекций).

### Дашборды Grafana (Калькуляторы ресурсов)
1.  **`Grafana_dashboard_servers.json`** — Калькулятор ресурсов **Терминальных Серверов (ТС)**.
    *   Позволяет оценить текущую утилизацию каждого сервера.
    *   Помогает выявить перегруженные или недогруженные хосты.
2.  **`Grafana_dashboard_farms.json`** — Калькулятор ресурсов **Терминальных Ферм (ТФ)**.
    *   Агрегирует данные по фермам.
    *   Помогает планировать емкость фермы на основе количества пользователей в AD и текущей нагрузки.

---

## ⚙️ Архитектура сбора данных

Шаблоны используют тип элемента данных `system.run` для чтения JSON-файлов, генерируемых внешними скриптами.

> **Важно:** Сами шаблоны **не собирают** данные напрямую из Windows API. Они ожидают, что на хосте запущен скрипт (PowerShell/Python), который опрашивает систему и сохраняет результат в JSON.

### Требования к агенту
*   Zabbix Agent должен иметь разрешение на выполнение команд `system.run`.
*   Путь к файлам данных по умолчанию: `C:\Program Files\Zabbix Agent\scripts\`

---

## 📝 Формат входных данных (JSON)

Для корректной работы Low-Level Discovery (LLD) ваши скрипты должны генерировать JSON-файлы следующей структуры.

### 1. Для шаблона "Terminal Servers" (`TerminalServer.json`)

Скрипт должен создавать файл `C:\Program Files\Zabbix Agent\scripts\TerminalServer.json`.

**Структура JSON:**
Массив объектов, где каждый объект описывает один терминальный сервер.

```json
[
  {
    "name_tc": "TS-MOSCOW-01",
    "active_session": 15,
    "disconnected_session": 2,
    "all_session": 17,
    "count_users_member": 50
  },
  {
    "name_tc": "TS-SPB-02",
    "active_session": 8,
    "disconnected_session": 0,
    "all_session": 8,
    "count_users_member": 30
  }
]
