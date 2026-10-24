# Баг-репорты

**Окружение (если не указано иное):** Windows 11, Google Chrome (последняя стабильная), 1920×1080
**Стенды:** https://www.saucedemo.com · https://restful-booker.herokuapp.com

| ID | Краткое описание | Severity | Priority | Модуль | Статус |
|---|---|---|---|---|---|
| BUG-001 | [problem_user] Ввод в Last Name перезаписывает First Name - оформление заказа невозможно | Critical | High | Checkout | Open |
| BUG-002 | [error_user] Кнопка Finish не завершает заказ | Critical | High | Checkout | Open |
| BUG-003 | [problem_user] Сортировка товаров не работает | Major | Medium | Products | Open |
| BUG-004 | [problem_user] У всех товаров одинаковое изображение | Major | Medium | Products | Open |
| BUG-005 | Можно оформить заказ с пустой корзиной | Major | High | Cart / Checkout | Open |
| BUG-006 | [performance_glitch_user] Вход занимает ~5 секунд | Minor | Low | Login | Open |
| BUG-007 | Поле Zip/Postal Code принимает буквы и спецсимволы | Minor | Low | Checkout | Open |
| BUG-008 | Reset App State не сбрасывает кнопки «Remove» в каталоге | Minor | Low | Menu | Open |
| BUG-009 | API: DELETE /booking/{id} возвращает 201 Created вместо 200/204 | Trivial | Low | API | Open |
| BUG-010 | API: POST /auth с неверными данными возвращает 200 OK вместо 401 | Minor | Medium | API | Open |
| BUG-011 | API: POST /booking без обязательных полей возвращает 500 вместо 400 | Major | Medium | API | Open |

---

## BUG-001 · [problem_user] Ввод в Last Name перезаписывает First Name - оформление заказа невозможно

| Поле | Значение |
|---|---|
| Severity | **Critical** - ключевой сценарий (покупка) заблокирован, обходного пути нет |
| Priority | High |
| Модуль | Checkout: Your Information |
| Воспроизводимость | 10/10 |

**Предусловия:**
1. Выполнен вход под `problem_user` / `secret_sauce`.
2. В корзину добавлен любой товар.

**Шаги:**
1. Открыть корзину, нажать **Checkout**.
2. В поле **First Name** ввести `Ivan`.
3. Кликнуть в поле **Last Name** и ввести `Petrov`.
4. В поле **Zip/Postal Code** ввести `123456`.
5. Нажать **Continue**.

**Ожидаемый результат:** в поле Last Name значение `Petrov`, выполнен переход на «Checkout: Overview».

**Фактический результат:** при вводе в Last Name символы попадают в поле First Name (в нём остаётся последний введённый символ - `v`), поле Last Name остаётся пустым. После Continue - ошибка «Error: Last Name is required».

**Вложения:** `screenshots/bug-001.png`, видео `screenshots/bug-001.mp4`

**Примечание:** у `standard_user` не воспроизводится → дефект фронтенда, привязанный к пользователю.

---

## BUG-002 · [error_user] Кнопка Finish не завершает заказ

| Поле | Значение |
|---|---|
| Severity | **Critical** |
| Priority | High |
| Модуль | Checkout: Overview |
| Воспроизводимость | 10/10 |

**Предусловия:** вход под `error_user`, в корзине 1 товар, заполнена форма Checkout (Last Name может не сохраняться - заполняем заново при необходимости), открыта страница «Checkout: Overview».

**Шаги:**
1. Открыть DevTools → Console.
2. Нажать **Finish**.

**Ожидаемый результат:** открыта страница «Checkout: Complete!», корзина очищена.

**Фактический результат:** страница не меняется, заказ не оформлен. В Console появляется ошибка JavaScript при клике.

**Вложения:** `screenshots/bug-002-console.png`

---

## BUG-003 · [problem_user] Сортировка товаров не работает

| Поле | Значение |
|---|---|
| Severity | **Major** - функция не работает, но покупка возможна |
| Priority | Medium |
| Модуль | Products |
| Воспроизводимость | 10/10 |

**Предусловия:** вход под `problem_user`.

**Шаги:**
1. В выпадающем списке сортировки выбрать **Price (low to high)**.

**Ожидаемый результат:** товары отсортированы по возрастанию цены ($7.99 … $49.99).

**Фактический результат:** порядок товаров не меняется, проверено для всех 4 вариантов сортировки. У `standard_user` сортировка работает → дефект привязан к пользователю.

**Вложения:** `screenshots/bug-003.png`

---

## BUG-004 · [problem_user] У всех товаров одинаковое изображение

| Поле | Значение |
|---|---|
| Severity | **Major** - пользователь не видит, какой товар покупает |
| Priority | Medium |
| Модуль | Products |
| Воспроизводимость | 10/10 |

**Предусловия:** вход под `problem_user`.

**Шаги:**
1. Осмотреть изображения карточек в каталоге.
2. DevTools → Elements → проверить атрибут `src` у изображений.

**Ожидаемый результат:** у каждого товара своё изображение.

**Фактический результат:** у всех 6 товаров одно и то же изображение (собака). В DevTools у всех `<img>` одинаковый `src` → проблема в данных/фронтенде, не в кэше браузера.

**Вложения:** `screenshots/bug-004.png`

---

## BUG-005 · Можно оформить заказ с пустой корзиной

| Поле | Значение |
|---|---|
| Severity | **Major** - нарушена бизнес-логика, создаются «пустые» заказы |
| Priority | High |
| Модуль | Cart / Checkout |
| Пользователь | `standard_user` (эталонный) |
| Воспроизводимость | 10/10 |

**Предусловия:** вход под `standard_user`, корзина пуста.

**Шаги:**
1. Нажать на иконку корзины.
2. Нажать **Checkout**.
3. Заполнить форму: `Ivan` / `Petrov` / `123456`, нажать **Continue**.
4. Нажать **Finish**.

**Ожидаемый результат:** кнопка Checkout неактивна при пустой корзине или показано сообщение «Your cart is empty».

**Фактический результат:** открывается Overview с Item total $0.00, Total $0.00; после Finish - «Thank you for your order!».

**Вложения:** `screenshots/bug-005.png`

---

## BUG-006 · [performance_glitch_user] Вход занимает ~5 секунд

| Поле | Значение |
|---|---|
| Severity | **Minor** |
| Priority | Low |
| Модуль | Login |
| Воспроизводимость | 5/5 |

**Шаги:**
1. Открыть DevTools → Network, включить «Disable cache».
2. Ввести `performance_glitch_user` / `secret_sauce`, нажать **Login**.
3. Засечь время до отображения каталога.

**Ожидаемый результат:** каталог открывается не более чем за 3 с (ориентир UX).

**Фактический результат:** среднее время по 5 замерам - ~5 с. Для сравнения: `standard_user` - < 1 с при тех же условиях. Задержка на стороне клиента (сетевые запросы выполняются быстро).

**Вложения:** `screenshots/bug-006-network.png`

---

## BUG-007 · Поле Zip/Postal Code принимает буквы и спецсимволы

| Поле | Значение |
|---|---|
| Severity | **Minor** |
| Priority | Low |
| Модуль | Checkout: Your Information |
| Воспроизводимость | 10/10 |

**Шаги:**
1. Войти под `standard_user`, добавить товар, нажать **Checkout**.
2. Заполнить First Name `Ivan`, Last Name `Petrov`, Zip `abc!@#`.
3. Нажать **Continue**.

**Ожидаемый результат:** ошибка валидации «Invalid postal code», переход не выполнен.

**Фактический результат:** значение принято, выполнен переход на Overview.

**Дополнительно:** принимаются также значения из одних пробелов и строки длиной 1000+ символов.

---

## BUG-008 · Reset App State не сбрасывает кнопки «Remove» в каталоге

| Поле | Значение |
|---|---|
| Severity | **Minor** |
| Priority | Low |
| Модуль | Burger menu |
| Воспроизводимость | 10/10 |
| Найден | Исследовательское тестирование |

**Шаги:**
1. Войти под `standard_user`.
2. Добавить в корзину 2 товара.
3. Открыть бургер-меню → **Reset App State**.

**Ожидаемый результат:** бейдж корзины исчез, кнопки товаров вернулись в состояние «Add to cart».

**Фактический результат:** бейдж исчез (корзина очищена), но у товаров остаются кнопки «Remove» до перезагрузки страницы. Нажатие на «Remove» ничего не делает → рассинхрон UI и состояния.

---

## BUG-009 · API: DELETE /booking/{id} возвращает 201 Created вместо 200/204

| Поле | Значение |
|---|---|
| Severity | **Trivial** - удаление работает, неверен только статус-код |
| Priority | Low |
| Эндпоинт | `DELETE /booking/{id}` |

**Запрос:**
```http
DELETE /booking/{id} HTTP/1.1
Host: restful-booker.herokuapp.com
Cookie: token={{token}}
```

**Ожидаемый результат:** `200 OK` или `204 No Content` (RFC 9110: 201 означает создание ресурса).

**Фактический результат:** `201 Created`, тело `Created`. Последующий `GET /booking/{id}` → `404` (бронь удалена).

**Postman:** запрос `CRUD / 07 Delete booking`, тест «Status code is 200 or 204» - красный.

---

## BUG-010 · API: POST /auth с неверными данными возвращает 200 OK вместо 401

| Поле | Значение |
|---|---|
| Severity | **Minor** |
| Priority | Medium |
| Эндпоинт | `POST /auth` |

**Запрос:**
```json
POST /auth
Content-Type: application/json

{ "username": "admin", "password": "wrong" }
```

**Ожидаемый результат:** `401 Unauthorized`.

**Фактический результат:** `200 OK`, тело `{"reason": "Bad credentials"}`. Клиент, проверяющий только статус-код, посчитает авторизацию успешной.

**Postman:** `Negative / Auth with wrong password` - красный.

---

## BUG-011 · API: POST /booking без обязательных полей возвращает 500 вместо 400

| Поле | Значение |
|---|---|
| Severity | **Major** - сервер падает на невалидном вводе, клиент не получает причину ошибки |
| Priority | Medium |
| Эндпоинт | `POST /booking` |

**Запрос:**
```json
POST /booking
Content-Type: application/json
Accept: application/json

{ "firstname": "Ivan" }
```

**Ожидаемый результат:** `400 Bad Request` с описанием недостающих полей.

**Фактический результат:** `500 Internal Server Error`, тело `Internal Server Error`.

**Postman:** `Negative / Create booking without required fields` - красный.
