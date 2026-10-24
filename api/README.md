# API-тестирование - Restful Booker

**Стенд:** https://restful-booker.herokuapp.com
**Документация:** https://restful-booker.herokuapp.com/apidoc/index.html

## Состав коллекции

| Папка | Запросы | Что проверяется |
|---|---|---|
| Health | `GET /ping` | Доступность стенда, время ответа |
| Auth | `POST /auth` | Получение токена → сохраняется в `{{token}}` |
| CRUD | 8 запросов | Полный жизненный цикл брони: список → создание → чтение → PUT → PATCH → проверка изменений → удаление → 404 |
| Negative | 4 запроса | Неверный пароль, тело без обязательных полей, PUT без токена, несуществующий id |

**Итого:** 14 запросов, 41 автопроверка.

## Что умеет коллекция

- **Цепочка переменных:** токен из `/auth` и `bookingid` из `POST /booking` автоматически подставляются в следующие запросы.
- **Pre-request script:** перед созданием брони генерируется уникальное имя и цена - тесты не зависят от чужих данных на общем стенде.
- **Проверки:** статус-код, время ответа, заголовки, значения полей, JSON Schema, сохранение изменений после PUT/PATCH, отсутствие ресурса после DELETE.

## Запуск

### Postman
1. File → Import → `restful-booker.postman_collection.json` и `restful-booker.postman_environment.json`.
2. Справа вверху выбрать окружение **Restful Booker**.
3. Правый клик по коллекции → **Run collection** → Run. Порядок запросов важен.

### Newman
```bash
npm install -g newman newman-reporter-htmlextra
newman run restful-booker.postman_collection.json \
  -e restful-booker.postman_environment.json \
  -r cli,htmlextra --reporter-htmlextra-export report.html
```

## Ожидаемо красные тесты (найденные баги)

| Запрос | Тест | Баг |
|---|---|---|
| CRUD / 07 Delete booking | Status code is 200 or 204 | BUG-009 - возвращается 201 |
| Negative / Auth with wrong password | Status code is 401 | BUG-010 - возвращается 200 |
| Negative / Create booking without required fields | Status code is 400, Not a server error | BUG-011 - возвращается 500 |

Тесты специально написаны по **ожидаемому** поведению REST, а не подогнаны под фактическое: красный тест = задокументированный дефект.
