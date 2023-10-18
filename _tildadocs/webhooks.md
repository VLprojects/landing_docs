---
layout: default
title: "Отслеживание событий в группе"
nav_order: 1700
parent: Интеграция
---
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
