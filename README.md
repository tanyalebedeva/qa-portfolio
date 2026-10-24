# QA Portfolio - ручное тестирование Web UI + REST API
Полный цикл ручного тестирования

| Объект | Тип | Ссылка |
|---|---|---|
| **Swag Labs** | Web UI, интернет-магазин | https://www.saucedemo.com |
| **Restful Booker** | REST API бронирования | https://restful-booker.herokuapp.com |

---

| Артефакт | Описание | Файл |
|---|---|---|
| Тест-план | Скоуп, подход, окружения, критерии входа/выхода, риски | [docs/test-plan.md](docs/test-plan.md) |
| Тест-дизайн | Классы эквивалентности, граничные значения, таблица решений, диаграмма состояний, pairwise | [docs/test-design.md](docs/test-design.md) |
| Чек-лист | 77 проверок UI, кроссбраузерность, адаптив, базовая безопасность | [checklists/ui-checklist.md](checklists/ui-checklist.md) |
| Тест-кейсы | 30 тест-кейсов (позитивные + негативные) с приоритетами | [test-cases/test-cases.md](test-cases/test-cases.md) |
| Баг-репорты | 11 дефектов в формате Jira с severity/priority | [bug-reports/bug-reports.md](bug-reports/bug-reports.md) |
| API-тестирование | Postman-коллекция: 14 запросов, 41 автопроверка, цепочки переменных | [api/](api/) |
| Отчёт | Test Summary Report с метриками | [reports/test-summary-report.md](reports/test-summary-report.md) |

---

## Инструменты

- **Chrome DevTools** - Network, Console, Elements, Device Toolbar (адаптив), Application (cookies, localStorage)
- **Postman** - коллекции, окружения, pre-request/test-скрипты, переменные
- **Newman** - запуск коллекции из консоли
- **Markdown + Mermaid** - документация и диаграммы
- **Git / GitHub** - версионирование, Issue Template для баг-репортов

---

## Структура репозитория

```
qa-portfolio/
├── README.md
├── docs/
│   ├── test-plan.md
│   └── test-design.md
├── checklists/
│   └── ui-checklist.md
├── test-cases/
│   └── test-cases.md
├── bug-reports/
│   ├── bug-reports.md
│   └── screenshots/
├── api/
│   ├── restful-booker.postman_collection.json
│   ├── restful-booker.postman_environment.json
│   └── README.md
├── reports/
│   └── test-summary-report.md
└── .github/ISSUE_TEMPLATE/bug_report.md
```

## Как запустить API-тесты

**Postman:** File → Import → выбрать оба файла из папки `api/` → выбрать окружение `Restful Booker` → Run collection.

**Newman (консоль):**
```bash
npm install -g newman newman-reporter-htmlextra
newman run api/restful-booker.postman_collection.json \
  -e api/restful-booker.postman_environment.json \
  -r cli,htmlextra
```

> Часть тестов **падает намеренно** (папка `Negative` и запрос `CRUD / 07 Delete booking`) - каждый красный тест соответствует найденному багу API (см. BUG-009…BUG-011).

---

## Итоги

| Метрика | Значение |
|---|---|
| Тест-кейсов выполнено | 30 |
| Passed / Failed / Blocked | 21 / 7 / 2 |
| Найдено дефектов | 11 (2 Critical, 4 Major, 4 Minor, 1 Trivial) |
| Покрытие модулей UI | Login, Products, Cart, Checkout, Menu, Footer |
| Покрытие API | Auth, CRUD /booking, Health check |

