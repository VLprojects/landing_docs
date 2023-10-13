# Авторизация

Для работы с API livedigital необходима авторизация. Авторизация осуществляется с помощью специальных токенов доступа. Предусмотрены разные типы допуска и разные токены для выполнения различных операций. 

Клиентский токен (`access_token`) позволяет просматривать публичные сущности, например, комнаты, а также создать пользовательский токен, который можно использовать для создания новых сущностей. Далее при описании методов API указано с помощью какого токена следует авторизовываться для выполнения того или иного запроса. 

**Примечание.** Клиентский токен представляет собой OAuth2 токен доступа и имеет ограниченный срок действия. Необходимо его периодически обновлять. 

Также используются персональные токены для авторизации в API из приложений от вашего имени. Можно создать несколько таких токенов. Рекомендуется для разных приложений использовать разные токены, чтобы можно было при необходимости удалить токен и запретить доступ только для конкретного приложения. Создание персональных токенов происходит в личном кабинете в разделе **Интеграция** и [описано ниже](#создание-токена-пользователя).


## Идентификатор клиента и секретный ключ
Прежде чем создавать и использовать токены доступа необходимо запросить у разработчика свой идентификатор клиента `clientID` и секретный ключ `client_secret`. На основе этих данных формируется клиентский токен.

Пример `clientID`, `client_secret`, полученных от разработчика:

```
{
 “clientId” : “john-smith-livedigital”,
 “clientSecret” : “VTytkTR7ZHH5nLhoWCLjymrUJjKBzfs4”
}

```

Идентификатор клиента и секретный ключ также можно сгенерировать в личном кабинете пользователя. Для этого нужно в меню выбрать пункт **Интеграция**: 

![menu](https://i.imgur.com/mtWcQE2.png)

А во всплывающем окне нажать кнопки **Получить** в поле **ClientId** и **Обновить** в поле **Client secret**. 

![integration](https://i.imgur.com/hwoKU4p.png)

В результате в данные поля будут записаны сгенеренные значения идентифкатора и секретного ключа.

Используя полученный идентификатор клиента и секретный ключ необходимо создать клиентский токен.

## Создание клиентского токена

Для получения клиентского токена следует выполнить запрос `getAccessToken`, передав в параметрах запроса свои `clientId` и `client_secret`, полученные ранее. Также в теле запроса передается параметр `grant_type` со значением `client_credentials`. Полная спецификация метода приведена [в Swagger](https://moodhood-api.livedigital.space/doc/#/Auth/getAccessToken).

Пример выполнения запроса `getAccessToken`

**POST https://moodhood-api.livedigital.space/v1/auth/token**

```json
{
 "client_id": "john-smith-livedigital",
 "client_secret": "VTytkTR7ZHH5nLhoWCLjymrUJjKBzfs4",
 "grant_type": "client_credentials"
}
```

Результат:

```json
{
  "token_type": "Bearer",
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MGM4YzZmMGE2MGY1Yzc0OGQxZWYyN2UiLCJhdWQiOiJ1c2VyIiwidHlwZSI6ImFjY2Vzc1Rva2VuIiwiY0lkIjoiNjBjOGM2ZjBhNjBmNWM3NDhkMWVmMjdlIiwianRpIjoiNzlhZjgwMDM4YTdmNSIsInNnbiI6IjA5OGY2YmNkNDYiLCJpYXQiOjE2MjM4NDU0MTIsImV4cCI6MTYyNDQ1MDIxMn0.jfzFAs_XM",
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MGM4YzZmMGE2MGY1Yzc0OGQxZWYyN2UiLCJhdWQiOiJ1c2VyIiwidHlwZSI6InJlZnJlc2hUb2tlbiIsImNJZCI6IjYwYzhjNmYwYTYwZjVjNzQ4ZDFlZjI3ZSIsImp0aSI6IjU0ZjM4NDQxZmU1MmQiLCJzZ24iOiIwOThmNmJjZDQ2IiwiaWF0IjoxNjIzODQ1NDEyLCJleHAiOjE2MjY0Mzc0MTJ9.Gj8fLaDAX4"
}
```

В теле ответа возвращается параметр `access_token`, который содержит значение клиентского токена.

## Использование токена

Токен используется при выполнении запросов. Для этого необходимо в заголовки запроса добавить заголовок следующего вида:

```
Authorization: Bearer <access_token>
```

Здесь `Bearer` - это тип используемой авторизации.

Пример кода для cURL с передачей токена в заголовке Authorization:

```
curl --location --request POST 'https://moodhood-api.livedigital.space/v1/auth/token' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmFiNWE4NGIwY2U2ZWUzMWIwOTk3YjMiLCJhdWQiOiJ1c2VyIiwidHlwZSI6ImFjY2Vzc1Rva2VuIiwiY0lkIjoiNjJhMDlmYzcwMmY4NmNlMzdhOTM4NmYxIiwianRpIjoiSzZEX1ZNQWRrc25CQnpjaTJnRlFBIiwic2duIjoiYjgxMTBmOGZiNiIsImlhdCI6MTY1NTc0MTEyOSwiZXhwIjoxNjU2MzQ1OTI5fQ.Qy-zwBGwjyWzPF8uXYtla9IOvCfOCeQ9uzCaUo8Q7pM' \
--header 'Content-Type: application/json' \
--data-raw '{
"client_id": "john-smith-livedigital",
"client_secret": "VTytkTR7ZHH5nLhoWCLjymrUJjKBzfs4",
"grant_type": "password",
"username": "test_user@test.test",
"password": "123456"
}'

```

## Создание токена пользователя

Для получения токена пользователя следует выполнить запрос `getAccessToken`, авторизовавшись с помощью клиентского токена и передав в параметрах запроса свои `clientId` и `client_secret`, полученные ранее.
Также в теле запроса передаются параметры: 
* **grant_type** - тип доступа (следует указать `password`)
* **username** - имя пользователя
* **password** - пароль пользователя

Полная спецификация метода приведена [в Swagger](https://moodhood-api.livedigital.space/doc/#/Auth/getAccessToken).

Пример выполнения запроса `getAccessToken`

**POST  https://moodhood-api.livedigital.space/v1/auth/token**
 
```json
{
 "client_id": "john-smith-livedigital",
 "client_secret": "VTytkTR7ZHH5nLhoWCLjymrUJjKBzfs4",
 "grant_type": "password",
 "username": "test_user@test.test",
 "password": "123456"
}
```

Пример кода для cURL с передачей клиентского токена в заголовке Authorization:
 
```
curl --location --request POST 'https://moodhood-api.livedigital.space/v1/auth/token' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmFiNWE4NGIwY2U2ZWUzMWIwOTk3YjMiLCJhdWQiOiJ1c2VyIiwidHlwZSI6ImFjY2Vzc1Rva2VuIiwiY0lkIjoiNjJhMDlmYzcwMmY4NmNlMzdhOTM4NmYxIiwianRpIjoiSzZEX1ZNQWRrc25CQnpjaTJnRlFBIiwic2duIjoiYjgxMTBmOGZiNiIsImlhdCI6MTY1NTc0MTEyOSwiZXhwIjoxNjU2MzQ1OTI5fQ.Qy-zwBGwjyWzPF8uXYtla9IOvCfOCeQ9uzCaUo8Q7pM' \
--header 'Content-Type: application/json' \
--data-raw '{
"client_id": "john-smith-livedigital",
"client_secret": "VTytkTR7ZHH5nLhoWCLjymrUJjKBzfs4",
"grant_type": "password",
"username": "test_user@test.test",
"password": "123456"
}'
```

Результат:

```json
{
   "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJhdWQiOiJjbGllbnQiLCJ0eXBlIjoiYWNjZXNzVG9rZW4iLCJjSWQiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJqdGkiOiI1ZGxDZ0pxRzZ1dFFycjNqSTFjSmMiLCJpYXQiOjE2NTUzMTYwNjgsImV4cCI6MTY1NTkyMDg2OH0.5vTOaqS77Yl-4cYT1WM5DdKbo8-I__bxB2DX5kyFaTA",
   "token_type": "Bearer",
   "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJhdWQiOiJjbGllbnQiLCJ0eXBlIjoicmVmcmVzaFRva2VuIiwiY0lkIjoiNjJhMDlmYzcwMmY4NmNlMzdhOTM4NmYxIiwianRpIjoiYXNhZnJfOUJpM1diUnZlTXFYVFhUIiwiaWF0IjoxNjU1MzE2MDY4LCJleHAiOjE2NTcxMzA0Njh9.tjZrOlt35FliWSNPlWX-piFfIyiYAym72h2Yk-eAsxQ"
}
```

В результате выполнения запроса возвращается JSON, содержащий значение `access_token` - это и есть токен пользователя для авторизации. Также метод возвращает значение `refresh_token` - это токен, который может использоваться для фоновой авторизации пользователя (подробнее см. [Добавление комнаты на сайт c помощью iFrame](#добавление-комнаты-на-сайт-c-помощью-iFrame)).


## Создание персонального токена

Персональные токены не имеют срока жизни и используются для авторизованного доступа к API в своих приложениях. Для разных приложений лучше использовать разные токены, чтобы можно было при необходимости удалить токен и запретить доступ только для конкретного приложения.

Список персональных токенов отображается в личном кабинете в разделе **Интеграция**:

![personal_tokens](https://i.imgur.com/1M5aMP9.png)

**Важно.** Показан только список токенов и время создания каждого токена. Посмотреть или скопировать созданный ранее токен нельзя. Токен отображается только один раз при его создании, потом увидеть его снова не получится. Поэтому если вы забыли или потеряли токен, то можно только удалить его и создать новый.

Чтобы создать новый токен следует нажать кнопку **Создать** ниже под списком токенов. Новый токен будет сгенерирован и откроется форма для его сохраения:

![personal_tokens](https://i.imgur.com/QehrNT0.png)

Скопируйте токен в надежное место, убедитесь, что значение сохранено - после закрытия формы увидеть токен снова будет невозможно. Когда токен будет скопирован, нажмите кнопку **Подтвердить сохранение токена**. Форма будет закрыта, а в список токенов будет добавлена новая запись со временем содания токена.

Чтобы удалить токен и запретить по нему доступ следует удалить токен из списка, нажав на ссылку **Удалить** рядом с ним:

![personal_tokens](https://i.imgur.com/cNUpdho.png)