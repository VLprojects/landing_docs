## Содержание

[TOC]

# Введение

Для начала работы с API необходимо обратиться к разработчику и получить свой идентификатор `clientId` и сформировать токен доступа. С помощью этого токена происходит авторизация при выполнении запросов к API.

## Группы и комнаты для проведения трансляций 

В рамках работы с API livedigital используются понятия группы (`space`) и комнаты (`room`). Каждый клиент работает в рамках своей группы, в которой создает комнаты для проведения вебинаров и видеоконференций. Группы и комнаты могут быть публичные и непубличные. 

## Доступ пользователей

Доступ к публичным группам имеют все пользователи. Доступ к непубличным группам имеют только создатель\модераторы. Для того, чтобы предоставить доступ остальным пользователям используются специальные токены. Токены формируются с помощью метода API. Чтобы пригласить пользователя в группу\комнату ему передается токен пользователя в виде ссылки, перейдя по которой пользователь активирует свой доступ. Токен можно использовать только один раз, после активации токен становится невалидным.

## Работа с API

API предоставляет набор методов, которые представляют собой HTTP-запросы. Выполняя запрос с определенными параметрами, можно создать новую группу или комнату, сформировать токен пользователя для доступа в комнату при проведении трансляции и т.д. Поддерживается выполнение только авторизованных запросов. Для этого в заголовке запроса передается специальный токен доступа. Такой токен является уникальным для каждого клиента или пользователя. При этом для выполнения одних запросов нужно авторизоваться как клиент, а для выполнения других как пользователь.

**Примечание!** В данном руководстве нет полного описания методов API, которые используются для выполнения операций. Для каждого метода дана ссылка на *Swagger*, где приведена полная спецификация методов. Для доступа к странице *Swagger* необходимо указать логин и пароль, которые можно получить, обратившись к разработчикам livedigital.

## Встраивание на сайт клиента

После того, как с помощью API в системе созданы соответствующие объекты данных для группы, комнат, пользователей, необходимо встроить функционал для отображения видеоконференции на страницу сайта. Для этого используется технология `iFrame`. Объект iFrame добавляется в HTML-код страницы. Такой объект содержит атрибуты, в которых передаются идентификаторы группы, пользователей, которые имеют доступ, параметры внешнего отображения на странице, разрешения - например, для использования камеры, микрофона и т.д.

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

В результате выполнения запроса возвращается JSON, содержащий значение `access_token` - это и есть токен пользователя для авторизации. Также метод возвращает значение `refresh_token` - это токен, который может использоваться для фоновой авторизации пользователя (подробнее см. [Добавление комнаты на сайт c помощью iFrame](#добавление-комнаты-на-сайт-c-помощью-iframe)).


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

# Создание групп и комнат

Для каждого клиента - организатора трансляций создается отдельная группа для управления всеми трансляциями. В каждой такой группе создаются комнаты для каждой отдельной трансляции. Для создания каждого такого объекта: группы (space), комнаты (room) и пользователя (user) используются свои методы API.

**Важно!** При создании групп и комнат для авторизации используется [токен пользователя](#создание-клиентского-токена).

## Создание группы

Для создания новой группы необходимо выполнить запрос `createSpaces`. В результате выполнения будет создана новая группа и вернется ее идентификатор. В запросе необходимо передать параметр `name` - имя создаваемой группы. Следует указать значение `isPublic: true` - для создания публичной группы. Дополнительно можно указать описание - параметр `description` и ссылку на изображение с логотипом - параметр `logo`. Полная спецификация метода приведена [в Swagger](https://moodhood-api.livedigital.space/doc/#/Spaces/createSpace).

**Примечание!** В случае указания параметра `isPublic:false` доступ к группе имеет только создатель\модератор  

Пример выполнения запроса `createSpaces`

**POST  https://moodhood-api.livedigital.space/v1/spaces**

```json 
{
  "isPublic": true,
  "name": "Some test space",
  "description": "Space where my team will have all future calls",
  "logo": "https://server.com/images/png-clipart-myspace-logo.png"
}
```

Пример кода для cURL

```
curl --location --request POST 'https://moodhood-api.livedigital.space/v1/spaces' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmFiNWE4NGIwY2U2ZWUzMWIwOTk3YjMiLCJhdWQiOiJ1c2VyIiwidHlwZSI6ImFjY2Vzc1Rva2VuIiwiY0lkIjoiNjJhMDlmYzcwMmY4NmNlMzdhOTM4NmYxIiwianRpIjoiZXZ5ZlJWRWsyRGozVFFsYzF5UnBLIiwic2duIjoiYjgxMTBmOGZiNiIsImlhdCI6MTY1NTc1ODAwMiwiZXhwIjoxNjU2MzYyODAyfQ.hzXW-dHJD0TUtLY22yefmZogvSanEbb70zRo4Kku98A' \
--header 'Content-Type: application/json' \
--data-raw '{
 "isPublic": true,
 "name": "Some test space",
 "description": "Space where my team will have all future calls"
}'
```

В результате успешного выполнения запроса будет создана новая группа и вернется ее идентификатор для дальнейшего использования. Например, для добавления комнат в группу.

Результат:
```json
{
  "id": "60d55c0eb9ef88ab17b0aabb"
}
```
## Создание комнат 

Для создания в комнат в группе используется метод `createRooms`. В строке запроса как часть URL-адреса передается ID группы, в которую будет добавлена комната - `{spaceId}` - это значение, возвращаемое методом `createSpaces` при создании группы (см. описание выше). В теле запроса обязательно указывается имя создаваемой комнаты - параметр `name`. Дополнительно можно задать тип комнаты и др. параметры (см [Настройка комнаты](#настройка-комнаты)). Полная спецификация метода приведена [в Swagger](https://moodhood-api.livedigital.space/doc/#/SpaceRooms/createRoom).

В результате выполнения метода в группе с заданным идентификатором создается новая комната определенного типа.

Пример выполнения запроса createRooms

**POST  https://moodhood-api.livedigital.space/v1/spaces/60d55c0eb9ef88ab17b0aabb/rooms**

```json 
{
  "name": "string",
  "isPublic": true,
  "isScreensharingAllowed": true,
  "isChatAllowed": true,
  "type": "lesson"
}
```

Пример кода для cURL

```
curl --location --request POST 'https://moodhood-api.livedigital.space/v1/spaces/62b0e24a81ad6df4bb583c58/rooms' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmFiNWE4NGIwY2U2ZWUzMWIwOTk3YjMiLCJhdWQiOiJ1c2VyIiwidHlwZSI6ImFjY2Vzc1Rva2VuIiwiY0lkIjoiNjJhMDlmYzcwMmY4NmNlMzdhOTM4NmYxIiwianRpIjoiZXZ5ZlJWRWsyRGozVFFsYzF5UnBLIiwic2duIjoiYjgxMTBmOGZiNiIsImlhdCI6MTY1NTc1ODAwMiwiZXhwIjoxNjU2MzYyODAyfQ.hzXW-dHJD0TUtLY22yefmZogvSanEbb70zRo4Kku98A' \
--header 'Content-Type: application/json' \
--data-raw '{
 "name": "string",
 "isPublic": true,
 "isScreensharingAllowed": true,
 "isChatAllowed": true,
 "type": "lesson"
}'
```

В результате выполнения запроса создается новая комната в заданной группе и возвращается JSON с параметрами комнаты: 
* id - полный идентификатор комнаты,
* alias - короткая ссылка на комнату для удобства использования в запросах,
* channelId - служебная информация.    
 
Результат:

```json
{
  "alias": "EGBYWoMZJe",
  "id": "62b0e25e3dc082dc7502d273",
  "channelId": "62b0e25e7f20b37190ff5e0a"
}
```

# Конфигурирование настроек группы, комнаты

## Настройка группы

Для настройки группы используется запрос `updateSpace`. В качестве параметра в строке запроса указывается идентификатор группы (см. [Создание группы](#создание-группы)). Для группы можно настроить следующие атрибуты:
* **name** - имя группы,
* **description** - описание группы,
* **logo** - логотип,
* **isPublic** - доступ к группе (для всех или только для создателя).

В результате выполнения запроса будут изменены настройки группы в соответствии с указанными параметрами. 

В теле запроса обязательно передаются параметры `name` - имя редактируемой группы и  `isPublic: true\false`. В случае указания параметра `isPublic: false` доступ к просмотру комнат в группе имеет только создатель\модератор. Если установлено значение `isPublic: true`, то доступ имеют все пользователи. Полная спецификация метода приведена [в Swagger](https://moodhood-api.livedigital.space/doc/#/Spaces/updateSpace).

Пример выполнения запроса `updateSpace`

**PUT  https://moodhood-api.livedigital.space/v1/spaces/60d55c0eb9ef88ab17b0aabb**

где *60d55c0eb9ef88ab17b0aabb* - идентификатор группы.

```json
{
  "isPublic": true,
  "name": "Some test space",
  "description": "Space where my team will have all future calls",
  "logo": "https://server.com/images/someimage.png"
}
```

Пример кода для cURL

```
curl --location --request PUT 'https://moodhood-api.livedigital.space/v1/spaces/60d55c0eb9ef88ab17b0aabb' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmFiNWE4NGIwY2U2ZWUzMWIwOTk3YjMiLCJhdWQiOiJ1c2VyIiwidHlwZSI6ImFjY2Vzc1Rva2VuIiwiY0lkIjoiNjJhMDlmYzcwMmY4NmNlMzdhOTM4NmYxIiwianRpIjoiZXZ5ZlJWRWsyRGozVFFsYzF5UnBLIiwic2duIjoiYjgxMTBmOGZiNiIsImlhdCI6MTY1NTc1ODAwMiwiZXhwIjoxNjU2MzYyODAyfQ.hzXW-dHJD0TUtLY22yefmZogvSanEbb70zRo4Kku98A' \
--header 'Content-Type: application/json' \
--data-raw '{
 "isPublic": true,
 "name": "Some test space",
 "description": "Space where my team will have all future calls",
 "Logo": "https://server.com/images/png-clipart-myspace-logo.png"
}'
```

В результате успешного выполнения запроса для группы будут установлены новые параметры и сервер вернет код ответа 200. 

## Настройка комнаты

Для настройки комнаты используется запрос `updateRoom`. В качестве параметров в строке запроса указываются идентификаторы комнаты (см. [Создание комнат](#создание-комнат)) и группы, в которой создана эта комната (см. [Создание группы](#создание-группы)). 

Для комнаты можно настроить следующие атрибуты (параметры в теле запроса):
* **name** - имя комнаты,
* **isScreensharingAllowed** - разрешено ли расшаривать экран во время трансляции,
* **isChatAllowed** - доступен ли чат для участников,
* **isPublic** - доступ к комнате (для всех или только для создателя\модератора),
* **type** - тип комнаты, для уроков или для вебинаров. 
 
Возможны два типа комнат:
* **lesson** - для уроков, при этом участники могут включать камеру, микрофон, демонстрацию экрана,
* **webinar** - для вебинаров, когда вести аудио\видео вещание может только создатель или модератор, остальные участники могут только смотреть и слушать. 

Полная спецификация метода приведена [в Swagger](https://moodhood-api.livedigital.space/doc/#/SpaceRooms/updateRoom).

В результате выполнения запроса будут изменены настройки комнаты в соответствии с указанными параметрами.

Пример выполнения запроса `updateRoom`

**PUT  https://moodhood-api.livedigital.space/v1/spaces/60d55c0eb9ef88ab17b0aabb/rooms/62b0e25e3dc082dc7502d273**

где *60d55c0eb9ef88ab17b0aabb* - идентификатор группы, а *62b0e25e3dc082dc7502d273* - идентификатор комнаты. 

```json  
{
  "name": "string",
  "isPublic": true,
  "isScreensharingAllowed": true,
  "isChatAllowed": true,
  "type": "lesson"
}
```

Пример кода для cURL

```
curl --location --request PUT 'https://moodhood-api.livedigital.space/v1/spaces/62b0e24a81ad6df4bb583c58/rooms/62b0e25e3dc082dc7502d273' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmFiNWE4NGIwY2U2ZWUzMWIwOTk3YjMiLCJhdWQiOiJ1c2VyIiwidHlwZSI6ImFjY2Vzc1Rva2VuIiwiY0lkIjoiNjJhMDlmYzcwMmY4NmNlMzdhOTM4NmYxIiwianRpIjoiZXZ5ZlJWRWsyRGozVFFsYzF5UnBLIiwic2duIjoiYjgxMTBmOGZiNiIsImlhdCI6MTY1NTc1ODAwMiwiZXhwIjoxNjU2MzYyODAyfQ.hzXW-dHJD0TUtLY22yefmZogvSanEbb70zRo4Kku98A' \
--header 'Content-Type: application/json' \
--data-raw '{
 "name": "string",
 "isPublic": true,
 "isScreensharingAllowed": true,
 "isChatAllowed": true,
 "type": "lesson"
}'
```

В результате выполнения запроса параметры комнаты будут обновлены и от сервера вернется код ответа 200. 

# Управление пользователями

Прежде чем приглашать пользователя присоединиться к трансляции, управлять его правами доступа к комнатам и т.д. необходимо создать учетную запись пользователя в системе.

## Создание пользователя

Для создания в системе нового пользователя используется метод `createUser`. В теле запроса передаются данные пользователя - адрес email, имя пользователя в системе, его пароль, дополнительно можно указать телефон. В результате выполнения метода будет зарегистрирован новый пользователь с указанными данными. Полная спецификация метода приведена [в Swagger](https://moodhood-api.livedigital.space/doc/#/Users/createNewUser).

**Важно!** При создании нового пользователя используется [токен клиента](#создание-клиентского-токена)

Пример выполнения запроса `createUser`

**POST  https://moodhood-api.livedigital.space/v1/users**

```json 
{
  "email": "some-email@example.com",
  "password": "gmi28abc",
  "username": "Some Username Here",
  "phone": "+79671234567",
  "captchaToken": "string"
}
```

Пример кода для cURL

```
curl --location --request POST 'https://moodhood-api.livedigital.space/v1/users' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmFiNWE4NGIwY2U2ZWUzMWIwOTk3YjMiLCJhdWQiOiJ1c2VyIiwidHlwZSI6ImFjY2Vzc1Rva2VuIiwiY0lkIjoiNjJhMDlmYzcwMmY4NmNlMzdhOTM4NmYxIiwianRpIjoiSzZEX1ZNQWRrc25CQnpjaTJnRlFBIiwic2duIjoiYjgxMTBmOGZiNiIsImlhdCI6MTY1NTc0MTEyOSwiZXhwIjoxNjU2MzQ1OTI5fQ.Qy-zwBGwjyWzPF8uXYtla9IOvCfOCeQ9uzCaUo8Q7pM' \
--header 'Content-Type: application/json' \
--data-raw '{
 "email": "some-email@example.com",
 "password": "gmi28abc",
 "username": "Some Username Here",
 "phone": "+79671234567"
}'
```

В результате выполнения запроса будет зарегистрирован новый пользователь с указанными данными и вернется его идентификатор в системе.

Результат:

``` json
{
  "id": "60cb1fd41f46ac12ff0eec79"
}
```

## Управление доступом пользователей

### Создание инвайта для доступа к группе

Чтобы пользователь мог присоединиться к трансляции, ему необходимо предоставить доступ к группе и комнате, где будет проходить трансляция.

**Примечание**. Для публичных групп и комнат доступ по умолчанию есть у всех пользователей.  

Для предоставления доступа к группе используется метод `createInvite`. В строке запроса передается идентификатор группы, а в теле запроса указывается параметр `role`, который может иметь два значения:
* **role_space_moderator** - модератор,
* **role_space_user** - пользователь.

Полная спецификация метода приведена [в Swagger](https://moodhood-api.livedigital.space/doc/#/SpaceInvites/createInvite).

В результате выполнения запроса возвращается идентификатор, по которому далее следует активировать инвайт (см. ниже). 

Пример выполнения запроса `createInvite`

**POST  https://moodhood-api.livedigital.space/v1/spaces/60d55c0eb9ef88ab17b0aabb/invites**

```json 
{
  "role": "role_space_moderator"
}
```

Пример кода для cURL

```
curl --location --request POST 'https://moodhood-api.livedigital.space/v1/spaces/62bcc725721aeb718445daf7/invites' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmFiNWE4NGIwY2U2ZWUzMWIwOTk3YjMiLCJhdWQiOiJ1c2VyIiwidHlwZSI6ImFjY2Vzc1Rva2VuIiwiY0lkIjoiNjJhMDlmYzcwMmY4NmNlMzdhOTM4NmYxIiwianRpIjoic2YxOHFtdkV4Ymdzc2pNV0hRNWVyIiwic2duIjoiYjgxMTBmOGZiNiIsImlhdCI6MTY1NjUzODg2NCwiZXhwIjoxNjU3MTQzNjY0fQ.bNopnFmGc7bEX8Jy8GkcojCA1SGy1wm7xIhFbO9kzVY' \
--header 'Content-Type: application/json' \
--header 'Cookie: accessToken=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmFiNWE4NGIwY2U2ZWUzMWIwOTk3YjMiLCJhdWQiOiJ1c2VyIiwidHlwZSI6ImFjY2Vzc1Rva2VuIiwiY0lkIjoiNjJhMDlmYzcwMmY4NmNlMzdhOTM4NmYxIiwianRpIjoic2YxOHFtdkV4Ymdzc2pNV0hRNWVyIiwic2duIjoiYjgxMTBmOGZiNiIsImlhdCI6MTY1NjUzODg2NCwiZXhwIjoxNjU3MTQzNjY0fQ.bNopnFmGc7bEX8Jy8GkcojCA1SGy1wm7xIhFbO9kzVY; refreshToken=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmFiNWE4NGIwY2U2ZWUzMWIwOTk3YjMiLCJhdWQiOiJ1c2VyIiwidHlwZSI6InJlZnJlc2hUb2tlbiIsImNJZCI6IjYyYTA5ZmM3MDJmODZjZTM3YTkzODZmMSIsImp0aSI6ImRoUXhxM2ZyV1V0QktOZUhsd1o3cCIsInNnbiI6ImI4MTEwZjhmYjYiLCJpYXQiOjE2NTY1Mzg4NjQsImV4cCI6MTY1ODM1MzI2NH0.INhr4aj0cY-l-CbyFL0KkV4nz-2-vmRMpdVB2MWTg2o; trClId=LhAdDHgbA5gqFpklo8rw' \
--data-raw '{
 "role": "role_space_moderator"
}'
```

Результат:

```json
{
   "id": "62bccb101f211e947a00da47"
}
```

Сервер возвращает идентификатор инвайта для его активации.

### Активация инвайта

Созданный инвайт для группы необходимо активировать. Для этого следует выполнить метод `activateInvite`, авторизовавшись с помощью `access_token` того пользователя, которому должен быть предоставлен доступ. Методу в качестве параметров в строке запроса передаются идентификатор группы и идентификатор инвайта (см. предыдущий раздел). Тело запроса пустое. Полная спецификация метода приведена [в Swagger](https://moodhood-api.livedigital.space/doc/#/SpaceInvites/activateInvite).

Пример выполнения запроса `activateInvite`

**POST  https://moodhood-api.livedigital.space/v1/spaces/62bcc725721aeb718445daf7/invites/62bccb101f211e947a00da47/activate**

где *62bcc725721aeb718445daf7* - идентификатор группы, *62bccb101f211e947a00da47* - идентификтаор инвайта.
 
Пример кода для cURL

```
curl --location --request POST 'https://moodhood-api.livedigital.space/v1/spaces/62bcc725721aeb718445daf7/invites/62bccb101f211e947a00da47/activate' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmFiNWE4NGIwY2U2ZWUzMWIwOTk3YjMiLCJhdWQiOiJ1c2VyIiwidHlwZSI6ImFjY2Vzc1Rva2VuIiwiY0lkIjoiNjJhMDlmYzcwMmY4NmNlMzdhOTM4NmYxIiwianRpIjoic2YxOHFtdkV4Ymdzc2pNV0hRNWVyIiwic2duIjoiYjgxMTBmOGZiNiIsImlhdCI6MTY1NjUzODg2NCwiZXhwIjoxNjU3MTQzNjY0fQ.bNopnFmGc7bEX8Jy8GkcojCA1SGy1wm7xIhFbO9kzVY' \
--header 'Cookie: accessToken=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmFiNWE4NGIwY2U2ZWUzMWIwOTk3YjMiLCJhdWQiOiJ1c2VyIiwidHlwZSI6ImFjY2Vzc1Rva2VuIiwiY0lkIjoiNjJhMDlmYzcwMmY4NmNlMzdhOTM4NmYxIiwianRpIjoic2YxOHFtdkV4Ymdzc2pNV0hRNWVyIiwic2duIjoiYjgxMTBmOGZiNiIsImlhdCI6MTY1NjUzODg2NCwiZXhwIjoxNjU3MTQzNjY0fQ.bNopnFmGc7bEX8Jy8GkcojCA1SGy1wm7xIhFbO9kzVY; refreshToken=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmFiNWE4NGIwY2U2ZWUzMWIwOTk3YjMiLCJhdWQiOiJ1c2VyIiwidHlwZSI6InJlZnJlc2hUb2tlbiIsImNJZCI6IjYyYTA5ZmM3MDJmODZjZTM3YTkzODZmMSIsImp0aSI6ImRoUXhxM2ZyV1V0QktOZUhsd1o3cCIsInNnbiI6ImI4MTEwZjhmYjYiLCJpYXQiOjE2NTY1Mzg4NjQsImV4cCI6MTY1ODM1MzI2NH0.INhr4aj0cY-l-CbyFL0KkV4nz-2-vmRMpdVB2MWTg2o; trClId=LhAdDHgbA5gqFpklo8rw'
```

В результате выполнения запроса пользователю будет предоставлен доступ к группе.

По одному инвайту можно пригласить только одного пользователя. После того, как инвайт будет активирован, он становится недействительным.

### Предоставление прав доступа к комнате

Для предоставления доступа к комнате используется метод `grantRole`. В строке запроса передается идентификатор группы и комнаты в ней, а в теле запроса идентификатор пользователя и его роль:
* **role_room_user** - пользователь,
* **role_room_moderator** - модератор.

Полная спецификация метода приведена [в Swagger](https://moodhood-api.livedigital.space/doc/#/RoomRoles/grantRoleToUserAtRoom).

В результате выполнения запроса пользователю задается роль для указанной комнаты.

Пример выполнения запроса `grantRole`

**POST  https://moodhood-api.livedigital.space/v1/spaces/60d55c0eb9ef88ab17b0aabb/rooms/60d55c0eb9ef88ab17b0aabb/roles**

где *60d55c0eb9ef88ab17b0aabb* - идентификатор группы, *60d55c0eb9ef88ab17b0aabb* - идентификатор комнаты.

```json 
{
  "targetUserId": "60d55c0eb9ef88ab17b0aabb",
  "role": "role_room_user"
}
```

Пример кода для cURL

```
curl --location --request POST 'https://moodhood-api.livedigital.space/v1/spaces/62bcc725721aeb718445daf7/rooms/62bcc7341f211e444300da37/roles' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmFiNWE4NGIwY2U2ZWUzMWIwOTk3YjMiLCJhdWQiOiJ1c2VyIiwidHlwZSI6ImFjY2Vzc1Rva2VuIiwiY0lkIjoiNjJhMDlmYzcwMmY4NmNlMzdhOTM4NmYxIiwianRpIjoic2YxOHFtdkV4Ymdzc2pNV0hRNWVyIiwic2duIjoiYjgxMTBmOGZiNiIsImlhdCI6MTY1NjUzODg2NCwiZXhwIjoxNjU3MTQzNjY0fQ.bNopnFmGc7bEX8Jy8GkcojCA1SGy1wm7xIhFbO9kzVY' \
--header 'Content-Type: application/json' \
--header 'Cookie: refreshToken=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmFiNWE4NGIwY2U2ZWUzMWIwOTk3YjMiLCJhdWQiOiJ1c2VyIiwidHlwZSI6InJlZnJlc2hUb2tlbiIsImNJZCI6IjYyYTA5ZmM3MDJmODZjZTM3YTkzODZmMSIsImp0aSI6ImRoUXhxM2ZyV1V0QktOZUhsd1o3cCIsInNnbiI6ImI4MTEwZjhmYjYiLCJpYXQiOjE2NTY1Mzg4NjQsImV4cCI6MTY1ODM1MzI2NH0.INhr4aj0cY-l-CbyFL0KkV4nz-2-vmRMpdVB2MWTg2o; trClId=LhAdDHgbA5gqFpklo8rw' \
--data-raw ' {
 "targetUserId": "60d55c0eb9ef88ab17b0aabb",
 "role": "role_room_user"
}'
```

В результате выполнения запроса пользователю назначаются права доступа к комнате в соответстви с заданной ролью и сервер возвращает код ответа 200.

### Предоставление прав доступа к комнате незарегистрированному пользователю

Можно предоставить временный доступ к комнате пользователю, незарегистрированному в системе. Для этого используется метод 
`generateAccess`. В строке запроса передается идентификатор группы и комнаты в ней, а в теле запроса имя пользователя и его роль:
* **user** - пользователь,
* **moderator** - модератор.

**Примечание**. Метод может быть использован только пользователем, который создал комнату (не путать с правами создателя группы) или уже имеющим права модератора в комнате.

Дополнительно в теле запроса можно передать следующие параметры: 
* **externalUserId** - любой буквенно-циферный идентификатор, по которому интегратор может сопоставить пользователя системы с пользователем своего сервиса, например, в аналитическом отчёте, 
* **ttl** - время жизни данной ссылки в секундах. Дефолтное значение 86400 секунд (24 часа),
* **isPermanent** - булево значение (`True` или `False`), указывающее, будет ли ссылка однаразовой - т.е. инвалидируется сразу после первого открытия, или ей можно воспользоваться снова в течение всего указанного периода TTL,
* **email** - эл. почта пользователя,
* **phone** - телефон пользователя.

**Примечание**. При переходе в комнату по ссылке, которая будет сгенерирована в результате выполнения метода, у пользователя будут запрошены персональные данные, такие как его эл.почта, телефон и т.д. Эти данные потом используются в аналитических отчетах. Если какие-то данные известны заранее, то можно передать их в запросе, указав в теле запроса значения `email`, `phone` и другие поля, которые могут быть запрошены у пользователя на странице входа. Если эти данные были переданы в теле запроса, то они уже не запрашиваются у пользователя повторно на форме входа в вебинар.

Полная спецификация метода приведена [в Swagger](https://moodhood-api.livedigital.space/doc/#/RoomGenerateAccess/roomGenerateAccess).

Пример выполнения запроса `generateAccess`

**POST  https://moodhood-api.livedigital.space/v1/spaces/60d55c0eb9ef88ab17b0aabb/rooms/60d55c0eb9ef88ab17b0aabb/generate-access**

где *60d55c0eb9ef88ab17b0aabb* - идентификатор группы, *60d55c0eb9ef88ab17b0aabb* - идентификатор комнаты.

```json 
{
  "username": "JohnSmith",
  "role": "user",
  "externalUserId": "string",
  "ttl": 86400,
  "isPermanent": true,
  "email": "user@some.mail",
  "phone": "+93456580"
}
```

Пример кода для cURL

```
curl --location --request POST 'https://moodhood-api.livedigital.space/v1/spaces/62bcc725721aeb718445daf7/rooms/62bcc7341f211e444300da37/generate-access' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmFiNWE4NGIwY2U2ZWUzMWIwOTk3YjMiLCJhdWQiOiJ1c2VyIiwidHlwZSI6ImFjY2Vzc1Rva2VuIiwiY0lkIjoiNjJhMDlmYzcwMmY4NmNlMzdhOTM4NmYxIiwianRpIjoic2YxOHFtdkV4Ymdzc2pNV0hRNWVyIiwic2duIjoiYjgxMTBmOGZiNiIsImlhdCI6MTY1NjUzODg2NCwiZXhwIjoxNjU3MTQzNjY0fQ.bNopnFmGc7bEX8Jy8GkcojCA1SGy1wm7xIhFbO9kzVY' \
--header 'Content-Type: application/json' \
--header 'Cookie: refreshToken=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmFiNWE4NGIwY2U2ZWUzMWIwOTk3YjMiLCJhdWQiOiJ1c2VyIiwidHlwZSI6InJlZnJlc2hUb2tlbiIsImNJZCI6IjYyYTA5ZmM3MDJmODZjZTM3YTkzODZmMSIsImp0aSI6ImRoUXhxM2ZyV1V0QktOZUhsd1o3cCIsInNnbiI6ImI4MTEwZjhmYjYiLCJpYXQiOjE2NTY1Mzg4NjQsImV4cCI6MTY1ODM1MzI2NH0.INhr4aj0cY-l-CbyFL0KkV4nz-2-vmRMpdVB2MWTg2o; trClId=LhAdDHgbA5gqFpklo8rw' \
--data-raw ' {
  "username": "JohnSmith",
  "role": "user",
  "externalUserId": "string",
  "ttl": 86400,
  "isPermanent": true,
  "email": "user@some.mail",
  "phone": "+93456580"
}'
```

В результате выполнения запроса пользователю задается роль для указанной комнаты и в теле ответа возвращается параметр `url`, содержащий ссылку для доступа вида:  

**https://edu.livedigital.space/room/hMInKY6FoM?participantName=JohnSmith&accessToken=someAccessToken**

Ссылка содержит значения `participantName`, равное указанному в запросе параметру `username` и уже сгенерированный токен пользователя `accessToken`, таким образом нет необходимости отдельно его генерировать и подставлять в ссылку.

# Добавление комнаты на сайт c помощью iFrame

Для добавления на сайт комнаты с трансляцией следует вставить в код страницы элемент `iFrame` с нужными атрибутами.

**Примечание**. Для сайта должна быть настроена работа по протоколу HTTPS.

Пример страницы с кодом iFrame:

```html
<!DOCTYPE html>
<html>
 <head>
  <meta charset="utf-8" />
  <title>livedigital</title>
 </head>
 <body>
  <p>Пример вставки фрейма livedigital</p>
  <iframe
    src="https://edu.livedigital.space/room/<room_alias>?participantName=IvanIvanov&refreshToken=someTokenHere"
    id="userplayer"
    frameborder="0"
    width="880px"
    height="595px"
    allow="camera;microphone;fullscreen;accelerometer;autoplay;clipboard-write;encrypted-media;gyroscope;picture-in-picture;display-capture;" allowusermedia>
  </iframe>  
 </body>
</html>
```

Элемент `iFrame` имеет следующие атрибуты:

* **src** - URL для доступа в комнату, где указывается alias созданной комнаты (см. [Создание комнат](#создание-комнат)). Также в качестве параметров здесь после символа “?” можно указать имя пользователя (`participantName`) и его refresh_token (`refreshToken`) для фоновой авторизации в комнате.
* **id** - устаревший атрибут для iFrame, оставленный для совместимости с ранними версиями браузеров, значение может быть любым.
* **frameborder** - ширина рамки вокруг фрейма (следует оставить 0 ).
* **width/height** - ширина/высота для отображения фрейма на странице.
* **disableSupport** - отключает техподдержку livedigital.
* **allow** - список разрешений для комнаты, где через знак “;” перечисляются какие возможности будут разрешены для комнаты. Так, в примере кода выше указаны следующие возможности:
   * *camera* - разрешено использование камеры;
   * *microphone* - разрешено использование микрофона;
   * *fullscreen* - разрешено отображение во весь экран;
   * *accelerometer* - разрешено использование акселерометра (на мобильных устройствах); 
   * *autoplay* - разрешено автоматическое проигрывание; 
   * *clipboard-write* - разрешено копирование в буфер обмена; 
   * *encrypted-media* - разрешено использование так называемого *Encrypted Media Extension API*, который позволяет управлять проигрыванием медиа контента на странице;
   * *display-capture* - разрешено использовать захват содержимого экрана; 
   * *gyroscope* - разрешено использование гироскопа; 
   * *picture-in-picture* - разрешена использование режима “картинка-в-картинке”; 
   * *screen-wake-lock* - включена защита от гаснущего экрана; 
   * *allowusermedia* - отдельно указанное разрешение, указывающее на то, что использование камеры и микрофона пользователем разрешено.

Если для атрибута `allow` для какого-то разрешения добавить ключевое слово `none`, то это означает запрет на использование. Например, *allow = camera \`none\`* означает, что использование камеры будет запрещено.

# Отслеживание событий в группе

API livedigital предоставляет возможность отслеживать события при помощи механизма вебхуков. Это такой механизм отправки обратных запросов с уведомлением при наступлении в системе определенного особытия, благодаря чему на такие события можно подписаться. Пример события, для которого можно создать вебхук и подписаться, это, например, начало или окончание звонка. При наступлении такого события сервисом livedigital будет выполнен POST-запрос на заранее заданный URL. Интегратору на своей стороне следует реализовать обработку такого запроса. Например, можно реализовать отправку уведомлений администратору группы. При этом следует иметь в виду, что доставка уведомлений не гарантирована. Если POST-запрос не был обработан, он будет отправлен снова с определенным интервалом. Всего будет сделано 3 таких попытки. Если запрос трижды не был обработан на стороне интегратора, то он больше не отправляется.

**Примечание.** Механизм вебхуков доступен на любом платном тарифе и недоступен для тарифного плана Free.

При создании вебхука возвращаются данные следующего вида: 

```json
{
  "url": "https://some.url",
  "secret": "g7r0gOSKiQ59prCGXo1H50PACb5ca1mD",
  "isActive": true
}

```

Здесь `url` это адрес, по которому будет выполнен обратный POST-запрос сервисом livedigital. Значение `secret` - ключ для проверки валидности созданного вебхука (см. описание ниже). `isActive` - признак того, что вебхук активен.

В результате наступления события сервис livedigital отправит на указанный URL запрос, передав в теле запроса данные следующего вида:

```json
{
  "signature": "string",
  "body": {
    "eventName": "payload.eventName",
    "roomId": "payload.roomId",
    "spaceId": "payload.spaceId",
    "recordId": "payload.recordId"
  },
}    
```

В блоке `body` содержатся данные о событии: название события `eventName` и идентификаторы комнаты\группы, для которых наступило событие. 

Возможные значения `eventName`:

* **call_started** — звонок начался;
* **call_finished** — звонок закончился; 
* **record_started** — начало облачной записи; 
* **record_finished** — окончание облачной записи (успешно); 
* **record_failed** — во время облачной записи произошла ошибка.

Также возвращается строковое значение `signature`. Это сигнатура для данных, содержащихся в `body`, сформированная по алгоритму HMAC sha256, а в качестве ключа было использовано значение `secret` (см. выше). Пользователю, создавшему вебхук, известно значение `secret`. Поэтому можно вычислить сигнатуру для данных в `body` и сравнить со значением, вернувшимся в `signature`. При совпадении сигнатур данные считаются валидными. При несовпадении сигнатур следует отклонить запрос, так как данные были скомпроментированы и не соответствуют действительности.

Пример кода на NodeJS для вычисления сигнатуры: 
 
```
crypto.createHmac('sha256', secret).update(JSON.stringify(body), 'utf8').digest('hex');
```

Для работы с вебхуками API livedigital предоставляет указанные ниже методы.

**Важно!** При создании вебхуков для авторизации используется [токен пользователя](#создание-токена-пользователя) — владельца группы.

## Создание вебхука

Для создания вебхука используется запрос `createWebhook`. В качестве параметра в строке запроса указывается идентификатор группы (см. [Создание группы](#создание-группы)), а в теле запроса передается параметр `url` - адрес, по которому должен быть выполнен обратный запрос с уведомлением о наступившем событии. 

Полная спецификация метода приведена [в Swagger](https://moodhood-api.livedigital.space/doc/#/WebHook/CreateWebHook).

Пример выполнения запроса `createWebhook`

**POST https://moodhood-api.livedigital.space/v1/spaces/60d55c0eb9ef88ab17b0aabb/webhook**

где *60d55c0eb9ef88ab17b0aabb* - идентификатор группы.


```json
{
  "url": "https://some.url/"
}
```
Пример кода для cURL

```
curl --location --request POST 'https://moodhood-api.livedigital.space/v1/spaces/62bcc725721aeb718445daf7/webhook' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJhdWQiOiJjbGllbnQiLCJ0eXBlIjoiYWNjZXNzVG9rZW4iLCJjSWQiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJqdGkiOiI1ZGxDZ0pxRzZ1dFFycjNqSTFjSmMiLCJpYXQiOjE2NTUzMTYwNjgsImV4cCI6MTY1NTkyMDg2OH0.5vTOaqS77Yl-4cYT1WM5DdKbo8-I__bxB2DX5kyFaTA' \
--header 'Content-Type: text/plain' \
--data-raw '{
  "url": "https://some.url/"
}'
```

В результате выполнения запроса будет создан вебхук для указанной группы и вернется JSON с параметрами: 
* `url` - адрес, по которому будет выполнен обратный запрос,
* `seret` - ключ для проверки подлинности данных, возвращаемых с помощью созданного вебхука,
* `isActive` - признак активности вебхука (по умолчанию `true`). 

Результат:

```json
{
  "url": "https://some.url",
  "secret": "g7r0gOSKiQ59prCGXo1H50PACb5ca1mD",
  "isActive": true
}
```

Создать вебхук можно также в личном кабинете в разделе **Интеграция**. 


![webhook](https://i.imgur.com/IrBj77C.png)

Для этого следует ввести значение URL в поле **WebHook URL** и нажать кнопку **Сохранить**. Значение сгенерированного ключа будет показано в поле **WebHook secret**, откуда его можно скопировать. В случае компрометации ключа его можно поменять, нажав кнопку **Обновить**.

## Изменение параметров вебхука

Для созданного ранее вебхука можно изменить значение `isActive` - признак того, что вебхук активен. Т.е. можно заблокировать или активировать вебхук. Для этого используется запрос `updateWebhook`. Запрос похож на создание вебхука, только используется тип запроса `PUT`. В качестве параметра в строке запроса указывается идентификатор группы (см. [Создание группы](#создание-группы)), а в теле запроса передаются параметры:
* `url` - адрес, по которому выполняется обратный запрос с уведомлением о событии.
* `isActive` - признак активности вебхука, `true` - вебхук активен, или `false` - вебхук заблокирован. 

Полная спецификация метода приведена [в Swagger](https://moodhood-api.livedigital.space/doc/#/WebHook/UpdateWebHook).

Пример выполнения запроса `createWebhook`

**PUT https://moodhood-api.livedigital.space/v1/spaces/60d55c0eb9ef88ab17b0aabb/webhook**

где *60d55c0eb9ef88ab17b0aabb* - идентификатор группы.


```json
{
  "url": "https://some.url/",
  "isActive": true
}
```
Пример кода для cURL

```
curl --location --request PUT 'https://moodhood-api.livedigital.space/v1/spaces/62bcc725721aeb718445daf7/webhook' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJhdWQiOiJjbGllbnQiLCJ0eXBlIjoiYWNjZXNzVG9rZW4iLCJjSWQiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJqdGkiOiI1ZGxDZ0pxRzZ1dFFycjNqSTFjSmMiLCJpYXQiOjE2NTUzMTYwNjgsImV4cCI6MTY1NTkyMDg2OH0.5vTOaqS77Yl-4cYT1WM5DdKbo8-I__bxB2DX5kyFaTA' \
--header 'Content-Type: text/plain' \
--data-raw '{
  "url": "https://some.url/",
  "isActive": true
}'
```

В результате выполнения запроса будет обновлен статус активности вебхука для указанной группы и вернется JSON с параметрами: 
* `url` - адрес, по которому должен быть выполнен обратный запрос,
* `isActive` - установленное значение активности вебхука. 

Результат:

```json
{
  "url": "https://some.url",
  "isActive": true
}
```

## Получение данных созданного вебхука

Чтобы получить данные созданного ранее вебхука используется метод `getWebhook`. Запрос похож на создание вебхука, только используется тип запроса `GET`. В качестве параметра в строке запроса указывается идентификатор группы (см. [Создание группы](#создание-группы)). 

Полная спецификация метода приведена [в Swagger](https://moodhood-api.livedigital.space/doc/#/WebHook/GetWebHook).

Пример выполнения запроса `createWebhook`

**GET https://moodhood-api.livedigital.space/v1/spaces/60d55c0eb9ef88ab17b0aabb/webhook**

где *60d55c0eb9ef88ab17b0aabb* - идентификатор группы.

Пример кода для cURL

```
curl --location --request GET 'https://moodhood-api.livedigital.space/v1/spaces/62bcc725721aeb718445daf7/webhook' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJhdWQiOiJjbGllbnQiLCJ0eXBlIjoiYWNjZXNzVG9rZW4iLCJjSWQiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJqdGkiOiI1ZGxDZ0pxRzZ1dFFycjNqSTFjSmMiLCJpYXQiOjE2NTUzMTYwNjgsImV4cCI6MTY1NTkyMDg2OH0.5vTOaqS77Yl-4cYT1WM5DdKbo8-I__bxB2DX5kyFaTA' \
--header 'Content-Type: text/plain' \
--data-raw '{
  "url": "https://some.url/",
  "isActive": true
}'
```

В результате выполнения запроса возвращаются данные вебхука для указанной группы: 
* `url` - адрес, по которому должен быть выполнен обратный запрос,
* `isActive` - установленное значение активности вебхука. 

Результат:

```json
{
  "url": "https://some.url",
  "isActive": true
}
```

## Удаление вебхука

Если нужно удалить вебхук для группы, то используется метод `deleteWebhook`. Выполняется запрос типа `DELETE`. В качестве параметра в строке запроса указывается идентификатор группы (см. [Создание группы](#создание-группы)).

Полная спецификация метода приведена [в Swagger](https://moodhood-api.livedigital.space/doc/#/WebHook/DeleteWebHook).

Пример выполнения запроса `deleteWebhook`

**DELETE https://moodhood-api.livedigital.space/v1/spaces/60d55c0eb9ef88ab17b0aabb/webhook**

где *60d55c0eb9ef88ab17b0aabb* - идентификатор группы.

Пример кода для cURL

```
curl --location --request DELETE 'https://moodhood-api.livedigital.space/v1/spaces/62bcc725721aeb718445daf7/webhook' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJhdWQiOiJjbGllbnQiLCJ0eXBlIjoiYWNjZXNzVG9rZW4iLCJjSWQiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJqdGkiOiI1ZGxDZ0pxRzZ1dFFycjNqSTFjSmMiLCJpYXQiOjE2NTUzMTYwNjgsImV4cCI6MTY1NTkyMDg2OH0.5vTOaqS77Yl-4cYT1WM5DdKbo8-I__bxB2DX5kyFaTA' \
--header 'Content-Type: text/plain' \
```

В результате выполнения запроса вебхук для заданной группы будет удален.

## Обновление ключа для проверки валидности вебхука

Если в ходе проверки валидности вебхука выяснилось, что сигнатуры не совпадают и есть подозрение, что ключ скомпрометирован, то его можно обновить. Для этого используется метод `refreshSecret`. В качестве параметра в строке запроса указывается идентификатор группы (см. [Создание группы](#создание-группы)), для которого нужно обновить ключ. Запрос выполняется по методу `PUT`.  

Полная спецификация метода приведена [в Swagger](https://moodhood-api.livedigital.space/doc/#/WebHook/RefreshSecretWebHook).

Пример выполнения запроса `refreshSecret`

**PUT https://moodhood-api.livedigital.space/v1/spaces/60d55c0eb9ef88ab17b0aabb/webhook/refresh-secret**

где *60d55c0eb9ef88ab17b0aabb* - идентификатор группы.

Пример кода для cURL

```
curl --location --request PUT 'https://moodhood-api.livedigital.space/v1/spaces/62bcc725721aeb718445daf7/webhook/refresh-secret' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJhdWQiOiJjbGllbnQiLCJ0eXBlIjoiYWNjZXNzVG9rZW4iLCJjSWQiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJqdGkiOiI1ZGxDZ0pxRzZ1dFFycjNqSTFjSmMiLCJpYXQiOjE2NTUzMTYwNjgsImV4cCI6MTY1NTkyMDg2OH0.5vTOaqS77Yl-4cYT1WM5DdKbo8-I__bxB2DX5kyFaTA' \
--header 'Content-Type: text/plain' \
```

В результате выполнения запроса вернутся данные вебхука с новым значенеим `secret`:

```json
{
  "url": "https://some.url",
  "secret": "g7r0gOSKiQ59prCGXo1H50PACb5ca1mD",
  "isActive": true
}
```

# Выгрузка видеозаписи и аналитики проведенного вебинара\конференции

API позволяет выгружать запись проведенного вебинара или конференции и аналитический отчет по вебинару\конференции в формате JSON или XLSX.

## Выгрузка видео с записью вебинара\конференции

Метод `RedirectToDownloadRoomRecord` позволяет получить ссылку на загрузку видео с проведенным вебинаром или конференцией. Ссылка будет действительна в течении 24 часов.

В строке запроса `RedirectToDownloadRoomRecord` обязательно передаются идентификаторы группы, комнаты и записи - параметры `spaceId`, `roomId` и `recordId`. Идентификаторы группы и комнаты для заданной вебинарной комнаты должны быть известны (см. раздел [Создание групп и комнат](#создание-групп-и-комнат)). А получить идентификатор записи можно двумя способами: с помощью вебхуков или с помощью специального метода (см. описание обоих способов ниже).

Полная спецификация метода приведена [в Swagger](https://moodhood-api.livedigital.space/doc/#/RoomRecords/RedirectToDownloadRoomRecord).

Пример выполнения запроса `RedirectToDownloadRoomRecord`

**GET https://moodhood-api.livedigital.space/v1/spaces/{spaceId}/rooms/{roomId}/records/download/{recordId}**

В результате выполнения запроса возвращается текстовая строка, содержащая URL записи, вида https://some.url/record.mp4.

### Как получить идентификатор записи вебинара\конференции

Как было сказано выше, идентификатор записи можно получить, если настроен механизм отправки обратных запросов (вебхуков) с уведомлением о наступлении в системе определенного события (см. [Отслеживание событий в группе](#отслеживание-событий-в-группе). Так для событий `record_finished` или `record started` в теле обратного запроса возвращается идентификатор законченной или начатой видеозаписи (`recordId`).

Пример обратного запроса для отслеживания события `record_finished`:  

```json
{
  "signature": "ae6edba49d063002f2957d94e47f5ff5de3dda0a5c1ce98848d15c777b9df7ce",
  "body": {
    "eventName": "record_finished",
    "roomId": "649c18e086b915cae33041a2",
    "spaceId": "64217150224cd9e040e114d1",
    "recordId": "64b6544f7b3b4f29d70afec2" 
  }
}
```

В теле обратного запроса передается значение `recordId`, которое и нужно использовать в качестве идентификатора записи в строке запроса `RedirectToDownloadRoomRecord`.

Если вебхуки для отслеживания событий не настроены, то можно использовать метод `GetRecords` для получения списка идентификаторов всех записей. После чего выбрать нужный идентификатор и использовать его в методе `RedirectToDownloadRoomRecord`. 

В строке запроса для метода `GetRecords` передаются обязательные параметры `spaceId` и `roomId` - идентификаторы группы и комнаты, записи для которых нужно получить, а также дополнительные параметры для фильтрации записей, например, `dateFrom` и `dateTo` - ограничения по датам начала и конца периода, за который нужны записи. Полная спецификация метода приведена [в Swagger](https://moodhood-api.livedigital.space/doc/#/RoomRecords/get_spaces__spaceId__rooms__roomId__records).

Пример выполнения запроса `GetRecords`

**GET https://moodhood-api.livedigital.space/v1/spaces/{spaceId}/rooms/{roomId}/records**

В результате выполнения запроса возвращается JSON, содержащий список `items`, где каждый элемент списка это объект типа `RoomRecord`, а поле `id` этого объекта это идентификатор записи. 

```json
{
  "items": [
    {
      "id": "60d55c0eb9ef88ab17b0aabb",
      "name": "string",
      "fileSize": 0,
      "roomId": "60d55c0eb9ef88ab17b0aabb",
      "spaceId": "60d55c0eb9ef88ab17b0aabb",
      "callId": "60d55c0eb9ef88ab17b0aabb",
      "state": "new",
      "createdAt": "2023-08-08T07:05:59.369Z",
      "startedAt": "2023-08-08T07:05:59.369Z",
      "finishedAt": "2023-08-08T07:05:59.369Z"
    }
  ],
  "total": 0
}
```

Для каждой записи также возвращаются различные параметры, например, `callId` - идентификатор события, `startedAt`, `finishedAt` - время начала и конца записи; по которым можно идентифицировать нужную запись.

Значение `id` для нужной записи необходимо передать в качестве параметра `recordId` в вызове метода `RedirectToDownloadRoomRecord` для получения ссылки на запись.

**Примечание!** Помимо получения списка записей и загрузки записи с видео API позволяет проводить другие операции с видео, например, стартовать и останавливать запись для комнаты, удалять видеозаписи и др. Полный набор методов для работы с видео можно посмотреть [в Swagger](https://moodhood-api.livedigital.space/doc/#/RoomRecords). 

## Выгрузка аналитики по проведенному вебинару или конференции

Для выгрузки аналитики по вебинару\конференции используется метод `getRoomAnalyticsReport`, который возвращает данные аналитики в формате JSON или XLSX. В параметрах метода указывается идентификатор вызова `callId`, который можно получить с помощью метода `analyticsCalls`. В отличие от записи видео идентификатор `callId` для аналитики нельзя получить с помощью вебхука для события `call_finished`. Но обратный вызов для этого события означает формирование аналитики после мероприятия (т.е. нет смысла запрашивать аналитику, пока не получено уведомление о событии `call_finished`).

В строке запроса `getRoomAnalyticsReport` как часть URL обязательно передается идентификатор группы `spaceId`, а также в качестве обязательных параметров запроса передается идентификатор комнаты и идентификатор вызова - параметры `roomId` и `callId`. Также указывается параметр `format`, который определяет формат выгрузки отчета и может принимать значение `xlsx` или `json`.

Идентификаторы группы и комнаты для заданной вебинарной комнаты должны быть известны (см. раздел [Создание групп и комнат](#создание-групп-и-комнат)). А получить идентификатор вызова `callId` можно с помощью метода `analyticsCalls` (см. описание ниже).

Полная спецификация метода `getRoomAnalyticsReport` приведена [в Swagger](https://moodhood-api.livedigital.space/doc/#/SpaceAnalytics/getRoomAnalyticsReport).

Пример выполнения запроса `getRoomAnalyticsReport`:

**GET https://moodhood-api.livedigital.space/v1/spaces/60d55c0eb9ef88ab17b0aabb/analytics/report?roomId=60d55c0eb9ef88ab17b0aabb&callId=60d55c0eb9ef88ab17b0aabb&format=xlsx**

В результате выполнения запроса возвращается аналитика по событию в формате JSON или XLSX.

Для получения `callId` нужно использовать метод `analyticsCalls`. В строке запроса как часть URL обязательно передается идентификатор группы `spaceId`, а также в качестве обязательных параметров запроса передается идентификатор комнаты и дата, за которую надо получить аналитику - параметры `roomId` и `date`. Дополнительно можно указать, например, фильтр по минимальному количеству участников события. 

Полная спецификация метода приведена [в Swagger](https://moodhood-api.livedigital.space/doc/#/SpaceAnalytics/analyticsCalls).

Пример выполнения запроса `analyticsCalls`:

**GET https://moodhood-api.livedigital.space/v1/spaces/60d55c0eb9ef88ab17b0aabb/analytics/calls?roomId=60d55c0eb9ef88ab17b0aabb&date=2023-08-01T12%3A25%3A40.253Z**

В результате выполнения запроса возвращается JSON со списком завершенных вызовов за указанную дату. 

```json
{
  "items": [
    {
      "id": "60d55c0eb9ef88ab17b0aabb",
      "active": true,
      "roomId": "60d55c0eb9ef88ab17b0aabb",
      "participantsCount": 0,
      "isBreakoutRoom": true,
      "planAlias": "string",
      "roomType": "lesson",
      "totalParticipantsCount": 0,
      "uniqueParticipantsCount": 0,
      "startedAt": "2023-08-12T12:26:51.553Z",
      "finishedAt": "2023-08-12T12:26:51.553Z"
    }
  ]
}
```

Значение `id` - это и есть идентификатор вызова, который необходимо передать в качестве параметра `callId` в методе `getRoomAnalyticsReport` для получения аналитики.

# Часто задаваемые вопросы

## Авторизация и доступ пользователей

**Можно ли предоставить доступ к записям напрямую с платформы для всех пользователей через API?**

Мы не предоставляем общий доступ к облачной записи для обычных пользователей, только для администратора.

**При тестировании столкнулась с проблемой: при попытке учеником присоединиться к вебинару приходит ответ Unauthorized.**

Предположительно, браузер не сохраняет файлы cookie, что приводит к тому, что аутентификация не работает. Попробуйте в настройках браузера включить разрешение браузеру к хранилищу. Например, для браузера Firefox см. в настройках раздел *Приватность и защита > Куки и данные сайтов*. Для браузера Chrome - раздел настроек *Конфиденциальность и безопасность > Файлы cookie и другие данные сайтов*. 

**Ошибка при получении токена пользователя. Использовали учетные данные клиента и `grant_type: password`, а так же учетные данные пользователя. Попробовали вариант с передачей `access token` в заголовке и без него, в обоих случаях приходит ответ `{"error":"invalid_grant","error_description":"Invalid credentials"}`**

Тут есть определённая путанница в авторизации. В соответствии с требованиями стандарта RFC oauth2 при получении токена с `grant_type: password`, поле с идентификатором пользователя обязано именоваться username. Но идентификатором пользователя у нас выступает email, так как username не уникален. Таким образом, при получении токена пользователя в поле username надо указывать email пользователя.

**Запрос GET `https://moodhood-api.livedigital.space/v1/spaces/` возвращает ошибку авторизации *403 Данный метод запрещен для пользователя*. Токен запрашиваю: `"grant_type": "client_credentials"` - возможно, тут надо использовать другой тип? Какой тип следует указать для получения отчетов по работе в комнате, информации по вебинарам?**

Создавать спэйсы\комнаты и получать данные по ним можно только под токеном пользователя - `grant_type:password`. Подробнее [см. здесь](#создание-токена-пользователя).

**Как сделать конференцию только для опредленных людей (чтобы никто случайно не вошел, даже зная ссылку)?**

У комнаты есть признак приватности и в неё смогут зайти только те люди, которым выдан доступ. Т.е. даже владелец спейса в приватную комнату зайти не сможет, если ему создатель этой комнаты не добавил права на её посещение.

**Можно ли пригшашать в комнату пользователя по предварительной авторизации (например, используя refresh hash), чтобы ему ничего не пришлось вводить при входе в комнату, и он сразу заходил в нее?**

Есть метод `roomGenerateAccess` полностью автоматизирующий этот процесс, подробная [инструкция здесь](#предоставление-прав-доступа-к-комнате-незарегистрированному-пользователю). Спецификация метода [в Swagger](https://moodhood-api.livedigital.space/doc/#/RoomGenerateAccess/roomGenerateAccess).

**А по пользователям — доступны только эти переменные? Или можно передавать еще и ID с платформы?
Актуально, если, например, сменил почту и/или имя, а ID на платформе постоянный.**

Смотря о каком методе мы говорим. Если `generate-acccess`, то да. Там можно указать `externalUserId` - это идентификатор пользователя из вашей системы и по нему можно в аналитике соотнести ваших и наших пользователей.

## Группы и комнаты

**В инструкции указан пример URL
`https://moodhood-api.livedigital.space/v1/spaces/60d55c0eb9ef88ab17b0aabb/rooms/60d55c0eb9ef88ab17b0aabb/generate-access` - там всегда дважны используется идентфикатор комнаты (<room_alias>)? Получается, что запрос пойдет такой `https://moodhood-api.livedigital.space/v1/spaces/<room_alias>/rooms/<room_alias>/generate-access`**

Не совсем, вот корректная структура запроса:
`https://moodhood-api.livedigital.space/v1/spaces/<space_id>/rooms/<room_id>/generate-access`

Более подробное описание всех параметров запроса можно посмотреть [в Swagger](https://moodhood-api.livedigital.space/doc/#/RoomGenerateAccess/roomGenerateAccess).

**Обратил внимание, что ссылка формируется в примере только с room_alias.**

Ссылки на открытие комнаты всегда формируются по alias, верно. А в работе с методами самого API всегда используются идентификаторы.

**Насколько вообще нужена группа, если у нас просто отдельный iFrame с уроком? Создание группы это опционально и ее может не быть? Или группа есть всегда?**

Группа всегда есть. К группе можно относиться просто как к директории для комнат. Можно создать их несколько, а можно все комнаты хранить в одной группе. Это родительский компонент для комнаты и всех имеющихся сущностей в конференции.

**В ссылке-приглашении на комнату `https://edu.livedigital.space/room/0mrw9W62rC` не указан spaceId, значит группа опцинальна?**

В ссылке используется alias, это сделано просто для сокращения длины ссылки, так как alias сам по себе уникален.

**Могу ли я сделать запрос без указания spaceId? Чтобы создателю было достаточно лишь указать просто ссылку на комнату**

Нет, если посмотреть [в Swagger](https://moodhood-api.livedigital.space/doc/#/SpaceRooms), то все запросы в API, касающиеся комнат содержат в URL и идентификатор spaceId тоже.

**Чем отличается группы от комнат и зачем они нужны (группы) на одной учетой записи? Не до конца понятна логика.**

Группы - это просто иерархическая единица, включающая в себя комнаты. Группа может быть одна. А может быть несколько, для удобства, так как каждое из них может иметь свои настройки приватности.

**Может ли быть несколько админов у вебинарной комнаты?**

Может быть неограниченное количество админов в вебинаре, но на экране отображается всего трое. Их можно чередовать, но чем больше админов участвуют именно в вещании (находятся на экране с включенными камерами\микрофонами), тем более высокая нагрузка.

## Получение статистики

**Нам нужна возможность в начале сессии передать Email пользователя, чтобы в конце сессии получить данные о посещаемости. В идеале нам надо каким-то методом отправлять запрос и получать список посещений, в виде адреса емейл и времени, проведённого на занятии, а также общее время встречи.**

В [этом разделе](https://moodhood-api.livedigital.space/doc/#/RoomGenerateAccess/roomGenerateAccess) описано, как сгенерировать готовую ссылку для iFrame для вашего пользователя с нужными правами и идентификатором пользователя из вашей системы, по которому вы сможете найти в аналитическом отчёте информацию о его активности в звонке, сообщениях в чате и др.

**Какие методы в Swagger отвечают за возможность показа статистики пользователям? Например, вебинар был обучающий. Getcourse, например, предоставляет доступ к шкале достижения оценок, статистике прохождения уроков, посещаемости, расписанию уроков.**

Вы можете собирать статистику по участникам мероприятий и результаты голосований по API в формате JSON. И из этих данных на своей стороне формировать любые типы отчетов. Вам могут пригодиться следующие методы:
* Метод для получения статистики: [getRoomAnalyticsReport](https://moodhood-api.livedigital.space/doc/#/SpaceAnalytics/getRoomAnalyticsReport)
* Метод для работы с участниками: [ManagementAPI](https://moodhood-api.livedigital.space/doc/#/RoomGenerateAccess/roomGenerateAccess)
* Метод для работы с опросами: [RoomPolls](https://moodhood-api.livedigital.space/doc/#/RoomPolls)   

**Нужно получить статистику по вебинару, проведенному в комнате с заданными spaceId > roomId в определенное время. Какой метод для этого используется?**

Первым делом необходимо запросить звонки, совершённые в комнате за интересующую дату с помощью метода [analyticsCalls](https://moodhood-api.livedigital.space/doc/#/SpaceAnalytics/analyticsCalls). Затем, используя необходимый `callId`, получить аналитический отчёт в необходимом вам формате с помощью метода [getRoomAnalyticsReport](https://moodhood-api.livedigital.space/doc/#/SpaceAnalytics/getRoomAnalyticsReport).

**Статистика доступна для всех? Статистика пользователей и их действий (реакций, опросов, чатов). Говорят, у беcплатных групп нету статистики?**

Статистика доступна только модераторам и администраторам на спэйсах, имеющих тариф с доступом к статистике.

## Интеграция

**Зачем тестировать интеграцию? Вообще на нашей платформе это лишь iFrame, который не дает никакой нагрузки на саму платформу, это чисто клиентская история (фронтэнд). Это равносильно тому, что просто будет открыта страница с сервисом и никакой трафик непосредственно через наши сервера в данном случае не идет.**

Все боты почти одновременно пройдут процедуру авторизации в вашем сервисе. Этот момент мы и тестируем, так как в некоторых случаях даже такой небольшой одновременный вход оказывался критичным.

**В хуках, которые вы отправляете, есть событие: *record_failed - Во время облачной записи произошла ошибка*. Стоит ли мне при этом событии заново стартовать запись? Она автоматические не стартует при ошибках?**

Да, в случае ошибки лучше начать заново, мы автоматически этого не делаем.

## Разное

**Будет ли приходить нашим пользователям ваша рассылка?**

При создании пользователей через API они не попадают в наши рассылки. Более того, мы пока этой опцией (рассылками) не пользуемся. Отправляем письма только для восстановления паролей. Но это не ваш случай, т.к. вы создаете на нашей стороне временных пользователей.

**После оканчания оплаченного тарифа он сменился на тариф Free. Я ожидала, что будет удаление аккаунта. Это баг?**

Это не баг. На нашей стороне просто отключается платный тарифный план, все правильно.

**Клиент, который будет использовать сервис, должен предоставить нам не только clientId и clientSecret, но еще и учетные данные своей учетки?**

clientId и clientSecret - это только ваши данные. К данному вопросу можно подойти с нескольких сторон. Нужно знать вашу схему интеграции. Вы можете зарегистрировать себе одного главного пользователя, который будет создавать все спэйсы\комнаты, генерировать ссылки и доступы для других пользователей на мероприятия. И этому же пользователю будут доступны все данные по мероприятиям - аналитика, отчёты и т.д.

**Каждому новому мероприятию присваиваеться ID? То есть, если вебинар был 21.12 в 10.00 и потом в 13.00, обоим мероприятиям будет назначен разный ID? Или следует по времени сортировать?**

Да, у каждого звонка свой идентификатор. Отдельная сущность для каждого звонка создаётся в момент входа первого пользователя в комнату и заканчивается в момент выхода последнего.

**Какой метод используется для получения списка записей?**

Документацию по работе с записью вебинаров можно посмотреть по [этой ссылке](https://moodhood-api.staging.livedigital.space/doc/#/RoomRecords/get_spaces__spaceId__rooms__roomId__records).

**Cмена типа с вебинара на конференцию возможна после проведения? Это на что-то влияет, на представление комнаты, например?**

Это deprecated функционал, скоро будет удалён. Настоятельно рекомендуем не изменять тип комнаты.
