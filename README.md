# Общая информация 

[Resful-Booker](https://github.com/mwinteringham/restful-booker) - это веб-API, который оснащен функциями аутентификации и содержит множество багов, которые можно найти и изучить. Restful-Booker также включает в себя подробную документацию по API. 

**Основные функции Resful-Booker:**  
- Просмотр бронирований  
- Создание бронирование
- Редактирование бронирование 
- Частичное редактирование бронирования  
- Удаление бронирования  

# Тест-план

## Получение бронирования по id  
Получение списка бронирования по id работает.  

В некоторых бронированиях нет additional needs (id: 1,3,6,7,8,10).

## Получение списка бронирования
Получение списка бронирований работает согласно спецификации.

## Получение списка бронирования с различными поисковыми фильтрами
По «firstname» и «lastname» фильтрует успешно.

## Создание бронирования
Работает создание бронирований согласно спецификации.

## Редактирование бронирования
Редактирование бронирование через все три вида авторизации работает.

Все поля можно редактировать.

## Удаление бронирования
Удаление бронирований работает успешно.

- Ищем удаленное бронирование по айди. Не находим.
- Ищем удаленное бронирование по списку. Не находим.
- Удаляем удаленное бронирование. Ошибка.
- Пытаемся редактировать данные через UpdateBooking и PartialUpdateBooking в удаленное бронирование. Ошибка.

## Поддержка форматов данных xml и json 
Формат данных в ответе (content-type) совпадает c форматом данных в запросе (accept).

Сервер принимает и отдает только xml и json форматы данных.

# Баг репорт

| Ожидаемый результат  | Фактический результат  | Шаги к воспроизведению |
|---|---|---|
|Endpoint -GetBooking <br> При фильтрации «checkin» получаем брони с датами позже или равными запрашиваемому в «checkin».| При введении даты въезда 2017-05-07 не получаем бронь с датой въезда 2017-05-07.| Отправить HTTP-запрос с query параметром checkin=2017-05-07|
|Endpoint-CreateBooking <br> При создании бронирования параметр «totalprice» принимает значение типа «number».| Параметр «totalprice» принимает значение типа «string». |Отправить HTTP-запрос методом POST. Body в формате JSON: <br> <code>{ <br> "firstname": "Keke",<br>"lastname": "Mur",<br> "totalprice": "456",<br>"depositpaid": "false",<br> "bookingdates":{<br>"checkin": "2023-10-05",<br>"checkout": "2023-12-30"<br>},<br> "additionalneeds": "breakfast"<br>}<code>|
|Endpoint-CreateBooking<br>При вводе даты въезда в 2025 году и даты выезда в 2019 году, выдает ошибку. |При вводе даты въезда в 2025 году и даты выезда в 2019 году, ошибку не выдает.<br> Логическая ошибка (въезд позже выезда).|Отправить HTTP-запрос методом PUT. Body в формате JSON:<code><br>{ <br>"firstname": "Roma", <br>"lastname": "Amor"<br>"totalprice": 410,<br>"depositpaid": true<br>"bookingdates": <br>{<br>"checkin": "2025-01-01",<br>"checkout": "2019-01-01"<br>},<br>"additionalneeds": "Breakfast"<br>}<code>|
|Endpoint-UpdateBooking<br>При вводе даты въезда в 2026 году и даты выезда в 2023 году, выдает ошибку.| При вводе даты въезда в 2026 году и даты выезда в 2023 году, ошибку не выдает.<br> Логическая ошибка (въезд позже выезда).|Отправить HTTP-запрос методом PUT. Body в формате JSON:<code><br> {<br>"firstname": "Susan",<br> "lastname": "Jones",<br>"totalprice": 464,<br> "depositpaid": false,<br> "bookingdates": <br>{<br>"checkin": "2026-10-14",<br>"checkout": "2023-10-20"<br>}<br>}<code>|
| Endpoint-PartialUpdateBooking <br>При патче даты въезда в «bookingdates» изменяется только дата въезда.|Изменяется не только дата въезда, но и дата выезда на "0NaN-aN-aN". |Отправить HTTP-запрос методом PATCH. Body в формате JSON:<br> <code> {<br>"bookingdates":{<br>"checkin": "2013-10-05"<br>}<br>} <code>|
|Endpoint-PartialUpdateBooking <br>При патче даты выезда в «bookingdates» изменяется только дата выезда.| Изменяется не только дата выезда, но и дата въезда на "0NaN-aN-aN".| Отправить HTTP-запрос методом PATCH. Body в формате JSON: <br><code>{<br>"bookingdates":{<br>"checkout": "2020-01-10"<br>}<br>} <code>|
|Endpoint-PartialUpdateBooking <br>При вводе даты въезда в 2030 году и даты выезда в 2020 году, выдает ошибку.| При вводе даты въезда в 2030 году и даты выезда в 2020 году ошибку не выдает. <br>Логическая ошибка (въезд позже выезда).| Отправить HTTP-запрос методом PATCH. Body в формате JSON:<br> <code> {<br>"bookingdates": {<br>"checkin": "2030-5-4",<br>"checkout": "2020-01-10"<br>}<br>}|