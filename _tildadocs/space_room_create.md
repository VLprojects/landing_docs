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
