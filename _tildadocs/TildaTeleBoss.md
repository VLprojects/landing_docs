## Содержание

[TOC]

# **Введение**

Документ описывает порядок действий для добавления на сайт видеоконференции teleboss с помощью предоставляемого API. Методы API позволяют создать пространство и комнаты для видеоконференций, настроить параметры их работы, добавить пользователей и управлять правами доступа для них к созданным комнатам.

# **Этапы интеграции**
Для начала работы с API необходимо зарегистрировать мастер-аккаунт на платформе teleboss и пройти в личный кабинет, затем в личном кабинете создать и сохранить персональный токен \- **Personal Token**. С помощью этого токена происходит авторизация при выполнении запросов к API.

**Очень важно использовать только тот персональный токен (`personal token`), который был сгенерирован владельцем аккаунта! Токены, которые были созданы с помощью других учетных записей - невалидны.**

***Этап 1:*** Регистрация мастер-аккаунта: перейдите на сайт teleboss и зарегистрируйте мастер-аккаунт, который будет использоваться для выполнения запросов к API.

***Этап 2:*** Получение персонального токена: создайте токен и сохраните его в безопасном месте. Этот токен будет использоваться для авторизации при выполнении запросов к API.

***Этап 3:*** Создание группы: используя полученный токен, выполните API-запрос для создания группы. Убедитесь, что передаете все необходимые параметры (например, название группы, описание и т.д.).

***Этап 4:*** Создание комнаты для мероприятия: выполните API-запрос для создания комнаты, указывая идентификатор ранее созданной группы. Убедитесь, что указаны все необходимые параметры (например, название комнаты и т.д.).

***Этап 5:*** Авторизация участников: выполните API-запросы для добавления участников в комнату, используя их учетные данные и роли (например, администратор, ведущий).

***Этап 6:*** Проведение мероприятия: мероприятие начнётся после того, как первый участник или администратор подключится к комнате.

***Этап 7:*** Передача аналитики и видеозаписи: после завершения мероприятия выполните запрос для получения аналитики и видеозаписи. Сохраните полученные данные в нужном формате (например, JSON/XLSX для аналитики, MP4 для видеозаписи).

## **Группы и комнаты для проведения мероприятий**

В рамках работы с API teleboss используются понятия группы (`space`) и комнаты (`room`). Каждый клиент работает в рамках своей группы, в которой создает комнаты для проведения вебинаров и видеоконференций. Комнаты могут быть публичные и приватные (непубличные).

## **Доступ пользователей**

Доступ к группам имеют создатель группы и все пользователи, которые были туда приглашены. Группа \- это родительский компонент для комнаты и всех имеющихся сущностей в конференции.

Доступ к комнате осуществляет через специальные пригласительные ссылки, которые создаются при помощи метода [`roomGenerateAccess`](https://moodhood-api.teleboss.ru/doc/#/RoomGenerateAccess/roomGenerateAccess). 

## **Работа с API**

API предоставляет набор методов, которые представляют собой HTTP-запросы. Выполняя запрос с определенными параметрами, можно создать новую группу или комнату, сформировать токен пользователя для доступа в группу или комнату при проведении трансляции и т.д. Поддерживается выполнение только авторизованных запросов. Для этого в заголовке запроса передается специальный токен доступа \- персональный токен (personal-token). Такой токен является уникальным для каждого клиента или пользователя. При этом для выполнения одних запросов нужно авторизоваться как клиент, а для выполнения других как пользователь.

**Примечание\!** В данном руководстве нет полного описания методов API, которые используются для выполнения операций. Для каждого метода дана ссылка на [`Swagger`](https://moodhood-api.teleboss.ru/doc/#/), где приведена полная спецификация методов.

## **Встраивание на сайт**

После того, как с помощью API в системе созданы соответствующие объекты данных для группы, комнат, пользователей, необходимо встроить функционал для отображения видеоконференции на страницу сайта. Для этого используется технология `iFrame`. Объект `iFrame` добавляется в HTML-код страницы. Такой объект содержит атрибуты, в которых передаются идентификаторы группы, пользователей, которые имеют доступ, параметры внешнего отображения на странице, разрешения \- например, для использования камеры, микрофона и т.д.

# **Авторизация**

Для работы с API teleboss необходима авторизация. Авторизация осуществляется с помощью специального токена доступа \- персонального токена (personal-token). Мы рекомендуем использовать один мастер-аккаунт, на котором будет создан этот токен и только один мастер-аккаунт будет использоваться в настройке интеграции. Администраторам/организаторам встреч не требуется создание личного аккаунта.

## **Создание персонального токена**

Персональные токены не имеют срока жизни и используются для авторизованного доступа к API в своих приложениях. Для разных проектов лучше использовать разные токены, чтобы можно было при необходимости удалить токен и запретить доступ только для конкретного проекта.

Список персональных токенов отображается в личном кабинете, в разделе **«Интеграция»**:

![menu](https://imgur.com/YtSDvFc.png)

**Примечание\!** Показан только список токенов и время создания каждого токена. Посмотреть или скопировать созданный ранее токен нельзя. Токен отображается только один раз при его создании, потом увидеть его снова не получится. Поэтому, если вы забыли или потеряли токен, то можно только удалить его и создать новый.

Чтобы создать новый токен следует нажать кнопку **«Создать»** ниже под списком токенов. Новый токен будет сгенерирован и откроется форма для его сохранения:

![save_personal_token](https://i.imgur.com/doNRPAo.png)

Скопируйте токен в надежное место. Убедитесь, что значение сохранено \- после закрытия формы увидеть токен снова будет невозможно. Когда токен будет скопирован, нажмите кнопку **«Подтвердить сохранение токена»**. Форма будет закрыта, а в список токенов будет добавлена новая запись со временем создания токена.

Чтобы удалить токен и запретить по нему доступ следует удалить токен из списка, нажав на ссылку Удалить рядом с ним:

![delete_personal_token](https://i.imgur.com/HFDxjoQ.png)

# **Cоздание групп и комнат**

Для каждого проекта создается отдельная группа для управления всеми мероприятиями. В каждой такой группе создаются комнаты для каждого отдельного мероприятия. Для создания каждого такого объекта: группы (`space`) и комнаты (`room`) используются свои методы API.

**Важно\!** Для всех операций с API должен использоваться персональный токен доступа пользователя, который был получен выше.

## **Создание группы**

Для создания новой группы необходимо выполнить запрос `createSpace`. В результате выполнения будет создана новая группа и вернется ее идентификатор. В запросе необходимо передать параметр `name` \- имя создаваемой группы. Следует указать значение `usPublic: true` \- для корректного создания группы. Дополнительно можно указать описание \- параметр `description` ссылку на изображение с логотипом \- параметр `logo.`

Пример выполнения запроса `createSpace:`   
**`POST`** `https://moodhood-api.teleboss.ru/v1/spaces`

```
{
  "isPublic": true,
  "name": "Имя моей группы",
  "descriptio
n": "Для meetings",
  "logo": "string"
}
```

Пример кода для cURL:

```
curl -X 'POST' \
  'https://moodhood-api.teleboss.ru/v1/spaces/' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "isPublic": true,
  "name": "Some space name",
  "description": "Space where my team will have all future calls",
  "logo": "string"
}'
```

#### В результате успешного выполнения запроса будет создана новая группа и вернется ее идентификатор для дальнейшего использования. Например, для добавления комнат в группу.

#### Результат: 

#### 

```
{
  "id": "60d55c0eb9ef88ab17b0aabb"
}
```

## **Создание комнаты**

Для создания в комнат в группе используется метод `createRoom`. В строке запроса как часть URL-адреса передается ID группы, в которую будет добавлена комната \- `{spaceId}` \- это значение, возвращаемое методом `createSpace` при создании группы (см. описание выше). В теле запроса обязательно указывается имя создаваемой комнаты \- параметр `name`. Дополнительно можно задать тип комнаты, имя, настройки автозаписи и др. параметры (см. [Настройка комнаты](#Настройка-комнаты-(room))). Полная спецификация метода приведена в [Swagger](https://moodhood-api.teleboss.ru/doc/#/SpaceRooms/createRoom).

В результате выполнения метода в группе с заданным идентификатором создается новая комната определенного типа.

Пример выполнения запроса `createRoom`:

**`POST`** `https://moodhood-api.teleboss.ru/v1/spaces/60d55c0eb9ef88ab17b0aabb/rooms`

```
{
  "name": "string",
  "isPublic": true,
  "isChatAllowed": true,
  "isRecordAllowed": true,
  "isAutoRecordingAllowed": false,
  "isMicrophonePublishingAllowed": true,
  "isScreenMediaPublishingAllowed": true,
  "isCameraPublishingAllowed": true,
  "isAvatarsAllowed": true,
  "isSelfRenamingAllowed": true,
  "isHandRaisingAllowed": true,
  "isScreensharingAllowed": true,
  "isRemoteDrawingAllowed": true,
  "type": "lesson",
  "waitingRoomAudience": "nobody",
  "redirectUrl": "https://developer.mozilla.org/docs/Web/HTTP/Overview"
}
```

Пример кода для cURL:

```
curl --location --request POST 'https://moodhood-api.teleboss.ru/v1/spaces/62b0e24a81ad6df4bb583c58/rooms' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmFiNWE4NGIwY2U2ZWUzMWIwOTk3YjMiLCJhdWQiOiJ1c2VyIiwidHlwZSI6ImFjY2Vzc1Rva2VuIiwiY0lkIjoiNjJhMDlmYzcwMmY4NmNlMzdhOTM4NmYxIiwianRpIjoiZXZ5ZlJWRWsyRGozVFFsYzF5UnBLIiwic2duIjoiYjgxMTBmOGZiNiIsImlhdCI6MTY1NTc1ODAwMiwiZXhwIjoxNjU2MzYyODAyfQ.hzXW-dHJD0TUtLY22yefmZogvSanEbb70zRo4Kku98A' \
--header 'Content-Type: application/json' \
--data-raw '{
  "id": "60d55c0eb9ef88ab17b0aabb",
  "appId": "60d55c0eb9ef88ab17b0aabb",
  "channelId": "60d55c0eb9ef88ab17b0aabb",
  "alias": "3h5Bd7h5bg1aX",
  "name": "string",
  "isPublic": true,
  "spaceId": "60d55c0eb9ef88ab17b0aabb",
  "templateId": "60d55c0eb9ef88ab17b0aabb",
  "parentRoomId": "60d55c0eb9ef88ab17b0aabb",
  "isChatAllowed": true,
  "isFavorite": true,
  "isRecordAllowed": true,
  "isScreensharingAllowed": true,
  "isAutoRecordingAllowed": false,
  "isMicrophonePublishingAllowed": true,
  "isScreenMediaPublishingAllowed": true,
  "isCameraPublishingAllowed": true,
  "isAvatarsAllowed": true,
  "isSelfRenamingAllowed": true,
  "isHandRaisingAllowed": true,
  "isRemoteDrawingAllowed": true,
  "type": "lesson",
  "waitingRoomAudience": "nobody",
  "breakoutRooms": {
    "status": "enabled",
    "session": {
      "status": "ready",
      "startedAt": null
    }
  },
  "joinSettings": {
    "fields": [
      {
        "slug": "email",
        "type": "email",
        "title": "Mobile Phone",
        "enabled": true,
        "required": true,
        "description": "Used as a helper in an input elements"
      }
    ],
    "customFields": [
      {
        "id": "f81d4fae-7dec-11d0-a765-00a0c91eabcd",
        "type": "string",
        "title": "Favorite Color",
        "enabled": true,
        "required": true,
        "description": "Used as a helper in an input elements",
        "order": 0
      }
    ]
  },
  "lastCallAt": "2025-01-20T11:11:23.289Z",
  "createdAt": "2025-01-20T11:11:23.289Z",
  "roleInRoom": "role_room_owner",
  "redirectUrl": "https://developer.mozilla.org/docs/Web/HTTP/Overview"
}
```

В результате выполнения запроса создается новая комната в заданной группе и возвращается JSON с параметрами комнаты:

* `id` \- полный идентификатор комнаты,  
* `alias` \- короткая ссылка на комнату для удобства использования в запросах,  
* `channelId` \- служебная информация.

Результат:

```
{
  "alias": "EGBYWoMZJe",
  "id": "62b0e25e3dc082dc7502d273",
  "channelId": "62b0e25e7f20b37190ff5e0a"
}
```

**Примечание\!** Если аналитика и запись полностью забираются в LMS, то комнаты прошедших мероприятий рекомендуем удалить. Если записи и аналитика продолжают храниться в облачном хранилище teleboss, то комнаты удалять не нужно.

# **Конфигурирование групп и комнат**

Для каждого проекта создается отдельная группа для управления всеми мероприятиями. 

## **Настройка группы (space)**

Настройка группы производится при создании самой группы, либо задается методом `updateSpace`. В качестве параметра в строке запроса указывается идентификатор группы (см. Создание группы). Для группы можно настроить следующие атрибуты:

* `name` \- имя группы;  
* `description` \- описание группы;  
* `logo` \- логотип;  
* `isPublic` \- deprecated функционал, в скоров времени этот атрибут будет удален.

В результате выполнения запроса будут изменены настройки группы в соответствии с указанными параметрами.

В теле запроса обязательно передаются параметры `name` \- имя редактируемой группы и `isPublic: true`. Полная спецификация метода приведена в [Swagger](https://moodhood-api.teleboss.ru/doc/#/Spaces/updateSpace).

Пример выполнения запроса `updateSpace`:

**`PUT`** `https://moodhood-api.teleboss.ru/v1/spaces/60d55c0eb9ef88ab17b0aabb`, где 60d55c0eb9ef88ab17b0aabb \- ID группы.

```
{
  "isPublic": true,
  "name": "Some test space",
  "description": "Space where my team will have all future calls",
  "logo": "https://server.com/images/someimage.png"
}
```

Пример кода для cURL:

```
{
curl --location --request PUT 'https://moodhood-api.teleboss.ru/v1/spaces/60d55c0eb9ef88ab17b0aabb' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmFiNWE4NGIwY2U2ZWUzMWIwOTk3YjMiLCJhdWQiOiJ1c2VyIiwidHlwZSI6ImFjY2Vzc1Rva2VuIiwiY0lkIjoiNjJhMDlmYzcwMmY4NmNlMzdhOTM4NmYxIiwianRpIjoiZXZ5ZlJWRWsyRGozVFFsYzF5UnBLIiwic2duIjoiYjgxMTBmOGZiNiIsImlhdCI6MTY1NTc1ODAwMiwiZXhwIjoxNjU2MzYyODAyfQ.hzXW-dHJD0TUtLY22yefmZogvSanEbb70zRo4Kku98A' \
--header 'Content-Type: application/json' \
--data-raw '{
 "isPublic": true,
 "name": "Some test space",
 "description": "Space where my team will have all future calls",
 "Logo": "https://server.com/images/png-clipart-myspace-logo.png"
}'
```

В результате успешного выполнения запроса для группы будут установлены новые параметры и сервер вернет код ответа 200\.

## **Настройка комнаты (room)**

Настройка комнаты производится при создании самой комнаты, либо задается методом [`updateRoom`](https://moodhood-api.teleboss.ru/doc/#/SpaceRooms/updateRoom). В качестве параметров в строке запроса указываются идентификаторы комнаты (см. Создание комнат) и группы, в которой создана эта комната (см. Создание группы).

Для комнаты можно настроить следующие атрибуты (параметры в теле запроса):

* `name` \- имя комнаты;  
* `isPublic` \- доступ к комнате (любой участник с ссылкой на комнату, может присоединиться к мероприятию или к комнате могут подключаться только пользователи из LMS, у которых есть специальная пригласительная ссылка, или зарегистрированные в teleboss и имеющие доступ к группе);  
* `isScreensharingAllowed` \- разрешено ли демонстрировать экран во время мероприятия участникам встречи;  
* `isChatAllowed` \- включен/выключен чат у всех пользователей;  
* `isAutoRecordingAllowed` \- включена/выключена автоматическая запись при входе администратора в комнату;  
* `isMicrophonePublishingAllowed` \- возможность включать/выключать микрофон участникам встречи (не администраторам);  
* `isCameraPublishingAllowed` \- возможность включать/выключать веб\-камеру участникам встречи (не администраторам);  
* `isRemoteDrawingAllowed` \- доступна ли функция удаленного рисования на экране пользователя;  
* `waitingRoomAudience` \- включен/выключен зал ожидания;  
* `type` \- тип комнаты: конференция(lesson) или вебинар(webinar);  
* `redirectUrl` \- включена/выключена переадресация после завершения встречи на заданный URL.

Возможны два типа комнат:

* `lesson` \- для уроков, при этом участники могут включать камеру, микрофон, демонстрацию экрана,  
* `webinar` \- для вебинаров, когда вести аудио\\видео вещание может только создатель или модератор, остальные участники могут только смотреть и слушать.

Полная спецификация метода приведена в [Swagger](https://moodhood-api.teleboss.ru/doc/#/SpaceRooms/updateRoom).

**Важно\!** Мы не рекомендуем изменять тип уже созданной комнаты. Также параметры `templateId` и `parentRoomId` не используются, их необходимо удалить при создании комнаты.

В результате выполнения запроса будут изменены настройки комнаты в соответствии с указанными параметрами.

Пример выполнения запроса `updateRoom`:

**`PUT`** `https://moodhood-api.teleboss.ru/v1/spaces/60d55c0eb9ef88ab17b0aabb/rooms/62b0e25e3dc082dc7502d273,` где 60d55c0eb9ef88ab17b0aabb \- ID группы (spaceId), а 62b0e25e3dc082dc7502d273 \- ID комнаты (roomId).

```
{
  "name": "string",
  "isPublic": true,
  "isChatAllowed": true,
  "isRecordAllowed": true,
  "isScreensharingAllowed": true,
  "isAutoRecordingAllowed": false,
  "isMicrophonePublishingAllowed": true,
  "isScreenMediaPublishingAllowed": true,
  "isCameraPublishingAllowed": true,
  "isRemoteDrawingAllowed": true,
  "waitingRoomAudience": "nobody",
  "redirectUrl": "https://developer.mozilla.org/docs/Web/HTTP/Overview"
}
```

Пример кода для cURL

```
curl --location --request PUT 'https://moodhood-api.teleboss.ru/v1/spaces/62b0e24a81ad6df4bb583c58/rooms/62b0e25e3dc082dc7502d273' \
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

В результате выполнения запроса параметры комнаты будут обновлены и от сервера вернется код ответа 200\.

# **Создание приглашений в комнату**

Для каждой комнаты создается ссылка, в которую будут вложены различные параметры: роль пользователя, его имя и т.д.

**Важно\!** Используйте только метод [`roomGenerateAccess`](https://moodhood-api.teleboss.ru/doc/#/RoomGenerateAccess/roomGenerateAccess), тогда аналитический отчёт после встречи сформируется корректно.

## **Приглашение участника с правами администратора или гостя в комнату**

При помощи метода [`roomGenerateAccess`](https://moodhood-api.teleboss.ru/doc/#/RoomGenerateAccess/roomGenerateAccess), который позволяет создать временного пользователя без необходимости регистрации на платформе teleboss, вы можете пригласить на встречу участника, наделенного правами администратора или не имеющего таковых. В строке запроса передается идентификатор группы и комнаты в ней, а в теле запроса имя пользователя и его роль:

* `user` \- гость,  
* `moderator` \- администратор встречи.

Дополнительно в теле запроса можно передать следующие параметры:

* `externalUserId` \- любой буквенно-циферный идентификатор пользователя, по которому интегратор может сопоставить пользователя системы с пользователем своего сервиса, например, в аналитическом отчёте. Рекомендуем создавать его уникальным для каждого пользователя, чтобы облегчить процесс идентификации в аналитическом отчёте;  
* `externalMeetingId` \- любой буквенно-циферный идентификатор для конкретной встречи. Необязательный параметр, который позволяет LMS системе передавать уникальный для неё идентификатор каждой конкретной встречи, на которую приходит участник. Рекомендуем создавать его уникальным для каждого пользователя, чтобы облегчить процесс идентификации в аналитическом отчёте;  
* `ttl` \- время жизни ссылки в секундах (по умолчанию 86400 секунд). Максимальное значение \- 432000 секунд. Рекомендуем передавать длительность на 2 часа больше, чем ожидаемая длительность мероприятия;  
* `isPermanent` \- позволяет перейти по пригласительной ссылке несколько раз, т.е. ссылка не будет аннулирована при повторном переходе. Если этот параметр отсутствует, то ссылка будет являться одноразовой;
* `email` \- эл. почта пользователя;
* `phone` \- телефон пользователя.

При переходе в комнату по ссылке, которая будет сгенерирована в результате выполнения метода, у пользователя будут запрошены персональные данные, такие как его эл.почта, телефон и т.д. Эти данные потом используются в аналитических отчетах. Если какие-то данные известны заранее, то можно передать их в запросе, указав в теле запроса значения email, phone и другие поля, которые могут быть запрошены у пользователя на странице входа. Если эти данные были переданы в теле запроса, то они уже не запрашиваются у пользователя повторно на форме входа в вебинар.

Полная спецификация метода приведена в [Swagger](https://moodhood-api.teleboss.ru/doc/#/RoomGenerateAccess/roomGenerateAccess).

Пример выполнения запроса `generateAccess`

**`POST`** `https://moodhood-api.teleboss.ru/v1/spaces/60d55c0eb9ef88ab17b0aabb/rooms/60d55c0eb9ef88ab17b0aabb/generate-access`, где 60d55c0eb9ef88ab17b0aabb \- ID группы, 60d55c0eb9ef88ab17b0aabb \- ID комнаты.

```
{
  "username": "JohnDoe",
  "role": "moderator",
  "externalUserId": "string",
  "externalMeetingId": "string",
  "ttl": 86400,
  "isPermanent": true,
  "email": "john.doe@mail.server",
  "phone": "+9606646464"
}
```

Пример кода cURL:

```
curl --location --request POST 'https://moodhood-api.teleboss.ru/v1/spaces/62bcc725721aeb718445daf7/rooms/62bcc7341f211e444300da37/generate-access' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmFiNWE4NGIwY2U2ZWUzMWIwOTk3YjMiLCJhdWQiOiJ1c2VyIiwidHlwZSI6ImFjY2Vzc1Rva2VuIiwiY0lkIjoiNjJhMDlmYzcwMmY4NmNlMzdhOTM4NmYxIiwianRpIjoic2YxOHFtdkV4Ymdzc2pNV0hRNWVyIiwic2duIjoiYjgxMTBmOGZiNiIsImlhdCI6MTY1NjUzODg2NCwiZXhwIjoxNjU3MTQzNjY0fQ.bNopnFmGc7bEX8Jy8GkcojCA1SGy1wm7xIhFbO9kzVY' \
--header 'Content-Type: application/json' \
--header 'Cookie: refreshToken=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmFiNWE4NGIwY2U2ZWUzMWIwOTk3YjMiLCJhdWQiOiJ1c2VyIiwidHlwZSI6InJlZnJlc2hUb2tlbiIsImNJZCI6IjYyYTA5ZmM3MDJmODZjZTM3YTkzODZmMSIsImp0aSI6ImRoUXhxM2ZyV1V0QktOZUhsd1o3cCIsInNnbiI6ImI4MTEwZjhmYjYiLCJpYXQiOjE2NTY1Mzg4NjQsImV4cCI6MTY1ODM1MzI2NH0.INhr4aj0cY-l-CbyFL0KkV4nz-2-vmRMpdVB2MWTg2o; trClId=LhAdDHgbA5gqFpklo8rw' \
--data-raw ' {
  "username": "JohnDoe",
  "role": "user",
  "externalUserId": "string",
  "ttl": 86400,
  "isPermanent": true,
  "email": "john.doe@mail.server",
  "phone": "+9606646464"
}'
```

В результате выполнения запроса пользователю задается роль для указанной комнаты и в теле ответа возвращается параметр `url`, содержащий ссылку для доступа вида:

`https://a.teleboss.ru/room/hMInKY6FoM?participantName=JohnDoe&accessToken=someAccessToken`

Ссылка содержит значения `participantName`, равное указанному в запросе параметру `username` и уже сгенерированный токен пользователя `accessToken`, таким образом нет необходимости отдельно его генерировать и подставлять в ссылку.

# 

# **Добавление комнаты на сайт с помощью iFrame**

Для добавления на сайт комнаты с трансляцией следует вставить в код страницы элемент `iFrame` с нужными атрибутами.

**Важно\!** Для сайта должна быть настроена работа по протоколу HTTPS.

Пример страницы с кодом `iFrame`:

```
<!DOCTYPE html>
<html>
 <head>
  <meta charset="utf-8" />
  <title>teleboss</title>
 </head>
 <body>
  <p>Пример вставки iFrame teleboss</p>
  <iframe
 src="https://a.teleboss.ru/room/<room_alias>?participantName=IvanIvanov&refreshToken=someTokenHere"
    id="userplayer"
    frameborder="0"
    width="100%"
    height="100%"
    allow="camera;microphone;fullscreen;accelerometer;autoplay;clipboard-write;encrypted-media;gyroscope;picture-in-picture;display-capture;" allowusermedia>
  </iframe>  
 </body>
</html>
```

Элемент `iFrame` имеет следующие атрибуты:

`src` \- URL для доступа в комнату, где указывается alias созданной комнаты (см. Создание комнат). Также в качестве параметров здесь после символа “?” можно указать имя пользователя (participantName) и его refresh\_token (refreshToken) для фоновой авторизации в комнате.

`id` \- устаревший атрибут для iFrame, оставленный для совместимости с ранними версиями браузеров, значение может быть любым.

`frameborder` \- ширина рамки вокруг фрейма (следует оставить `0` ).

`width/height` \- ширина/высота для отображения фрейма на странице.

`disableSupport` \- отключает техподдержку teleboss.

`allow` \- список разрешений для комнаты, где через знак “`;`” перечисляются какие возможности будут разрешены для комнаты. Так, в примере кода выше указаны следующие возможности:

`camera` \- разрешено использование камеры;

`microphone` \- разрешено использование микрофона;

`fullscreen` \- разрешено отображение во весь экран;

`accelerometer` \- разрешено использование акселерометра (на мобильных устройствах);

`autoplay` \- разрешено автоматическое проигрывание;

`clipboard-write` \- разрешено копирование в буфер обмена;

`encrypted-media` \- разрешено использование так называемого Encrypted Media Extension API, который позволяет управлять проигрыванием медиа контента на странице;

`display-capture` \- разрешено использовать захват содержимого экрана;

`gyroscope` \- разрешено использование гироскопа;

`picture-in-picture` \- разрешено использование режима “картинка-в-картинке”;

`screen-wake-lock` \- заблокировано отключение экрана со временем, если открыта страница с включенным элементом `iFrame`;

`allowusermedia` \- отдельно указанное разрешение, указывающее на то, что использование камеры и микрофона пользователем разрешено.

Если для атрибута `allow` для какого-то разрешения добавить ключевое слово `none`, то это означает запрет на использование. Например, `` allow = camera `none` `` означает, что использование камеры будет запрещено.

**Переадресация пользователя из iFrame по окончанию звонка**

После завершения сущности звонка (все пользователи покинули комнату/администратор завершил звонок), teleboss отправит в родительское окно `iFrame` сообщение:

```
window.parent.postMessage('ld_finish_call');
```

Чтобы подписаться на это событие, необходимо добавить слушателя `message` к объекту `window`, как указано в [спецификации](https://developer.mozilla.org/ru/docs/Web/API/Window/postMessage):

```
window.addEventListener("message", receiveMessage, false);
```

# 

# **Отслеживание событий в группе**

API teleboss предоставляет возможность отслеживать события при помощи механизма вебхуков (webhook). Это такой механизм отправки обратных запросов с уведомлением при наступлении в системе определенного особытия, благодаря чему на такие события можно подписаться. Пример события, для которого можно создать вебхук и подписаться, это, например, начало или окончание звонка. При наступлении такого события сервисом teleboss будет выполнен POST-запрос на заранее заданный URL. Интегратору на своей стороне следует реализовать обработку такого запроса. Например, можно реализовать отправку уведомлений администратору группы. При этом следует иметь в виду, что доставка уведомлений не гарантирована. Если POST-запрос не был обработан, он будет отправлен снова с определенным интервалом. Всего будет сделано 3 таких попытки. Если запрос трижды не был обработан на стороне интегратора, то он больше не отправляется.

**Примечание\!** Механизм вебхуков доступен на любом платном тарифе и недоступен для бесплатного тарифного плана **«**Basic**»**.

При создании вебхука возвращаются данные следующего вида:

```
{
  "url": "https://some.url",
  "secret": "g7r0gOSKiQ59prCGXo1H50PACb5ca1mD",
  "isActive": true
}
```

Здесь `url` это адрес, по которому будет выполнен обратный POST-запрос сервисом teleboss. Значение `secret` \- ключ для проверки валидности созданного вебхука (см. описание ниже). `isActive` \- признак того, что вебхук активен.

В результате наступления события сервис teleboss отправит на указанный URL запрос, передав в теле запроса данные следующего вида:

```
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

В блоке `body` содержатся данные о событии: название события `eventName` и идентификаторы комнаты\\группы, для которых наступило событие.

Возможные значения `eventName`:

`call_started` — звонок начался;

`call_finished` — звонок закончился;

`record_started` — начало облачной записи;

`record_finished` — окончание облачной записи (успешно);

`record_aborted` — запись была остановлена пользователем в течении 3-х секунд после старта;

`record_failed` — во время облачной записи произошла ошибка.

Также возвращается строковое значение signature. Это сигнатура для данных, содержащихся в `body`, сформированная по алгоритму `HMAC sha256`, а в качестве ключа было использовано значение secret (см. выше). Пользователю, создавшему вебхук, известно значение secret. Поэтому можно вычислить сигнатуру для данных в `body` и сравнить со значением, вернувшимся в `signature`. При совпадении сигнатур данные считаются валидными. При несовпадении сигнатур следует отклонить запрос, так как данные были скомпроментированы и не соответствуют действительности.

Пример кода на NodeJS для вычисления сигнатуры:

```
crypto.createHmac('sha256', secret).update(JSON.stringify(body), 'utf8').digest('hex');
```

Для работы с вебхуками API teleboss предоставляет указанные ниже методы.

**Важно\!** При создании вебхуков для авторизации используется персональный токен пользователя — владельца группы.

## **Создание вебхука**

Для создания вебхука используется запрос [`createWebhook`](https://moodhood-api.teleboss.ru/doc/#/WebHook/CreateWebHook). В качестве параметра в строке запроса указывается идентификатор группы (см. Создание группы), а в теле запроса передается параметр `url` \- адрес, по которому должен быть выполнен обратный запрос с уведомлением о наступившем событии.

Полная спецификация метода приведена в [Swagger](https://moodhood-api.teleboss.ru/doc/#/WebHook/CreateWebHook).

Пример выполнения запроса `createWebhook`

**`POST`** `https://moodhood-api.teleboss.ru/v1/spaces/60d55c0eb9ef88ab17b0aabb/webhook`

где 60d55c0eb9ef88ab17b0aabb \- идентификатор группы.

 

```
{
  "url": "https://some.url/"
}
```

Пример кода для cURL:

```
{
curl --location --request POST 'https://moodhood-api.teleboss.ru/v1/spaces/62bcc725721aeb718445daf7/webhook' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJhdWQiOiJjbGllbnQiLCJ0eXBlIjoiYWNjZXNzVG9rZW4iLCJjSWQiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJqdGkiOiI1ZGxDZ0pxRzZ1dFFycjNqSTFjSmMiLCJpYXQiOjE2NTUzMTYwNjgsImV4cCI6MTY1NTkyMDg2OH0.5vTOaqS77Yl-4cYT1WM5DdKbo8-I__bxB2DX5kyFaTA' \
--header 'Content-Type: text/plain' \
--data-raw '{
  "url": "https://some.url/"
}'
```

В результате выполнения запроса будет создан вебхук для указанной группы и вернется JSON с параметрами:

* `url` \- адрес, по которому будет выполнен обратный запрос,  
* `seret` \- ключ для проверки подлинности данных, возвращаемых с помощью созданного вебхука,  
* `isActive` \- признак активности вебхука (по умолчанию true).

Результат:

```
{
  "url": "https://some.url",
  "secret": "g7r0gOSKiQ59prCGXo1H50PACb5ca1mD",
  "isActive": true
}
```

Создать вебхук можно также в личном кабинете в разделе «Интеграция»: для этого следует ввести значение URL в поле «WebHook URL» и нажать кнопку «Сохранить». Значение сгенерированного ключа будет показано в поле «WebHook secret», откуда его можно скопировать. В случае компрометации ключа его можно поменять, нажав кнопку «Обновить».

![update_webhook_url](https://imgur.com/4wCXSxF.png)

## **Изменение параметров вебхука**

Для созданного ранее вебхука можно изменить значение `isActive` \- признак того, что вебхук активен. То есть, можно заблокировать или активировать вебхук. Для этого используется запрос `updateWebhook`. Запрос похож на создание вебхука, только используется тип запроса **`PUT`**. В качестве параметра в строке запроса указывается идентификатор группы (см. Создание группы), а в теле запроса передаются параметры:

`url` \- адрес, по которому выполняется обратный запрос с уведомлением о событии.

`isActive` \- признак активности вебхука, `true` \- вебхук активен, или `false` \- вебхук заблокирован.

Полная спецификация метода приведена в [Swagger](https://moodhood-api.ru.space/doc/#/WebHook/UpdateWebHook).

Пример выполнения запроса `updateWebhook`

**`PUT`** `https://moodhood-api.teleboss.ru/v1/spaces/60d55c0eb9ef88ab17b0aabb/webhook`

где 60d55c0eb9ef88ab17b0aabb \- идентификатор группы.

```
{
  "url": "https://some.url/",
  "isActive": true
}
```

Пример кода для cURL:

```
{
curl --location --request PUT 'https://moodhood-api.teleboss.ru/v1/spaces/62bcc725721aeb718445daf7/webhook' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJhdWQiOiJjbGllbnQiLCJ0eXBlIjoiYWNjZXNzVG9rZW4iLCJjSWQiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJqdGkiOiI1ZGxDZ0pxRzZ1dFFycjNqSTFjSmMiLCJpYXQiOjE2NTUzMTYwNjgsImV4cCI6MTY1NTkyMDg2OH0.5vTOaqS77Yl-4cYT1WM5DdKbo8-I__bxB2DX5kyFaTA' \
--header 'Content-Type: text/plain' \
--data-raw '{
  "url": "https://some.url/",
  "isActive": true
}'
```

В результате выполнения запроса будет обновлен статус активности вебхука для указанной группы и вернется JSON с параметрами:

`url` \- адрес, по которому должен быть выполнен обратный запрос,

`isActive` \- установленное значение активности вебхука.

Результат:

```
{
  "url": "https://some.url",
  "isActive": true
}
```

## **Получение данных созданного вебхука**

Чтобы получить данные созданного ранее вебхука используется метод `getWebhook`. Запрос похож на создание вебхука, только используется тип запроса **`GET`**. В качестве параметра в строке запроса указывается идентификатор группы (см. Создание группы).

Полная спецификация метода приведена в [Swagger](https://moodhood-api.teleboss.ru/doc/#/WebHook/GetWebHook).

Пример выполнения запроса `getWebhook:`

**`GET`** `https://moodhood-api.teleboss.ru/v1/spaces/60d55c0eb9ef88ab17b0aabb/webhook`

где 60d55c0eb9ef88ab17b0aabb \- идентификатор группы.

Пример кода для cURL:

```
{
curl --location --request GET 'https://moodhood-api.teleboss.ru/v1/spaces/62bcc725721aeb718445daf7/webhook' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJhdWQiOiJjbGllbnQiLCJ0eXBlIjoiYWNjZXNzVG9rZW4iLCJjSWQiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJqdGkiOiI1ZGxDZ0pxRzZ1dFFycjNqSTFjSmMiLCJpYXQiOjE2NTUzMTYwNjgsImV4cCI6MTY1NTkyMDg2OH0.5vTOaqS77Yl-4cYT1WM5DdKbo8-I__bxB2DX5kyFaTA' \
--header 'Content-Type: text/plain' \
--data-raw '{
  "url": "https://some.url/",
  "isActive": true
}'
```

В результате выполнения запроса возвращаются данные вебхука для указанной группы:

`url` \- адрес, по которому должен быть выполнен обратный запрос,

`isActive` \- установленное значение активности вебхука.

Результат:

```
{
  "url": "https://some.url",
  "isActive": true
}
```

## **Удаление вебхука**

Если нужно удалить вебхук для группы, то используется метод [`deleteWebhook`](https://moodhood-api.teleboss.ru/doc/#/WebHook/DeleteWebHook). Выполняется запрос типа **`DELETE`**. В качестве параметра в строке запроса указывается идентификатор группы (см. Создание группы).

Полная спецификация метода приведена в [Swagger](https://moodhood-api.teleboss.ru/doc/#/WebHook/DeleteWebHook).

Пример выполнения запроса `deleteWebhook`:

**`DELETE`** `https://moodhood-api.teleboss.ru/v1/spaces/60d55c0eb9ef88ab17b0aabb/webhook`

где 60d55c0eb9ef88ab17b0aabb \- идентификатор группы.

Пример кода для cURL:

```
{
curl --location --request DELETE 'https://moodhood-api.teleboss.ru/v1/spaces/62bcc725721aeb718445daf7/webhook' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJhdWQiOiJjbGllbnQiLCJ0eXBlIjoiYWNjZXNzVG9rZW4iLCJjSWQiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJqdGkiOiI1ZGxDZ0pxRzZ1dFFycjNqSTFjSmMiLCJpYXQiOjE2NTUzMTYwNjgsImV4cCI6MTY1NTkyMDg2OH0.5vTOaqS77Yl-4cYT1WM5DdKbo8-I__bxB2DX5kyFaTA' \
--header 'Content-Type: text/plain' \
}
```

В результате выполнения запроса вебхук для заданной группы будет удален.

## **Обновление ключа для проверки валидности вебхука**

Если в ходе проверки валидности вебхука выяснилось, что сигнатуры не совпадают и есть подозрение, что ключ скомпрометирован, то его можно обновить. Для этого используется метод [`refreshSecretWebhook`](https://moodhood-api.teleboss.ru/doc/#/WebHook/RefreshSecretWebHook). В качестве параметра в строке запроса указывается идентификатор группы (см. Создание группы), для которого нужно обновить ключ. Запрос выполняется по методу **`PUT`**.

Полная спецификация метода приведена в [Swagger](https://moodhood-api.teleboss.ru/doc/#/WebHook/RefreshSecretWebHook).

Пример выполнения запроса `refreshSecretWebhook`:

**`PUT`** `https://moodhood-api.teleboss.ru/v1/spaces/60d55c0eb9ef88ab17b0aabb/webhook/refresh-secret`

где 60d55c0eb9ef88ab17b0aabb \- идентификатор группы.

Пример кода для cURL

```
{
curl --location --request PUT 'https://moodhood-api.teleboss.ru/v1/spaces/62bcc725721aeb718445daf7/webhook/refresh-secret' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJhdWQiOiJjbGllbnQiLCJ0eXBlIjoiYWNjZXNzVG9rZW4iLCJjSWQiOiI2MmEwOWZjNzAyZjg2Y2UzN2E5Mzg2ZjEiLCJqdGkiOiI1ZGxDZ0pxRzZ1dFFycjNqSTFjSmMiLCJpYXQiOjE2NTUzMTYwNjgsImV4cCI6MTY1NTkyMDg2OH0.5vTOaqS77Yl-4cYT1WM5DdKbo8-I__bxB2DX5kyFaTA' \
--header 'Content-Type: text/plain' \
}
```

В результате выполнения запроса вернутся данные вебхука с новым значением `secret`:

```
{
  "url": "https://some.url",
  "secret": "g7r0gOSKiQ59prCGXo1H50PACb5ca1mD",
  "isActive": true
}
```

# **Выгрузка видеозаписи и аналитики проведенного вебинара или конференции**

API teleboss предоставляет возможность LMS автоматически выгружать аналитику в формате JSON/XLSX и получать доступ к видеозаписи мероприятия после доставки webhook о завершении конференции или вебинара.

## **Выгрузка видео с записью вебинара или конференции** 

Метод [`RedirectToDownloadRoomRecord`](https://moodhood-api.teleboss.ru/doc/#/RoomRecords/RedirectToDownloadRoomRecord) позволяет получить ссылку на загрузку видео с проведенным вебинаром или конференцией. Ссылка будет действительна в течении 24 часов.

В строке запроса `RedirectToDownloadRoomRecord` обязательно передаются идентификаторы группы, комнаты и записи \- параметры `spaceId`, `roomId` и `recordId`. Идентификаторы группы и комнаты для заданной вебинарной комнаты должны быть известны (см. раздел Создание групп и комнат). А получить идентификатор записи можно двумя способами: с помощью вебхуков или с помощью специального метода (см. описание обоих способов ниже).

Полная спецификация метода приведена в [Swagger](https://moodhood-api.teleboss.ru/doc/#/RoomRecords/RedirectToDownloadRoomRecord).

Пример выполнения запроса `RedirectToDownloadRoomRecord`

**`GET`** `https://moodhood-api.teleboss.ru/v1/spaces/{spaceId}/rooms/{roomId}/records/download/{recordId}`

В результате выполнения запроса возвращается текстовая строка, содержащая URL записи, вида [`https://some.url/record.mp4`](https://some.url/record.mp4).

## **Как получить идентификатор записи вебинара/конференции**

Как было сказано выше, идентификатор записи можно получить, если настроен механизм отправки обратных запросов (вебхуков) с уведомлением о наступлении в системе определенного события (см. Отслеживание событий в группе). Так для событий `record_finished` или `record_started` в теле обратного запроса возвращается идентификатор законченной или начатой видеозаписи (`recordId`).

Пример обратного запроса для отслеживания события `record_finished`:

```
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

Если вебхуки для отслеживания событий не настроены, то можно использовать метод [`getRecords`](https://moodhood-api.teleboss.ru/doc/#/RoomRecords/get_spaces__spaceId__rooms__roomId__records) для получения списка идентификаторов всех записей. После чего выбрать нужный идентификатор и использовать его в методе `RedirectToDownloadRoomRecord`.

В строке запроса для метода `getRecords` передаются обязательные параметры `spaceId` и `roomId` \- идентификаторы группы и комнаты, записи для которых нужно получить, а также дополнительные параметры для фильтрации записей, например, `dateFrom` и `dateTo` \- ограничения по датам начала и конца периода, за который нужны записи. Полная спецификация метода приведена в [Swagger](https://moodhood-api.teleboss.ru/doc/#/RoomRecords/get_spaces__spaceId__rooms__roomId__records).

Пример выполнения запроса `getRecords`

**`GET`** `https://moodhood-api.teleboss.ru/v1/spaces/{spaceId}/rooms/{roomId}/records`

В результате выполнения запроса возвращается JSON, содержащий список `items`, где каждый элемент списка это объект типа `RoomRecord`, а поле id этого объекта это идентификатор записи.

```
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

Для каждой записи также возвращаются различные параметры, например, `callId` \- идентификатор события, `startedAt`, `finishedAt` \- время начала и конца записи; по которым можно идентифицировать нужную запись.

Значение `id` для нужной записи необходимо передать в качестве параметра `recordId` в вызове метода `RedirectToDownloadRoomRecord` для получения ссылки на запись.

**Примечание\!** Помимо получения списка записей и загрузки записи с видео API позволяет проводить другие операции с видео: стартовать и останавливать запись для комнаты, удалять видеозаписи и др. Полный набор методов для работы с видео можно посмотреть в [Swagger](https://moodhood-api.teleboss.ru/doc/#/RoomRecords).

## **Выгрузка аналитики по проведенному вебинару или конференции**

Для выгрузки аналитики по вебинару\\конференции используется метод [`getRoomAnalyticsReport`](https://moodhood-api.teleboss.ru/doc/#/SpaceAnalytics/getRoomAnalyticsReport), который возвращает данные аналитики в формате JSON или XLSX. В параметрах метода указывается идентификатор вызова `callId`, который можно получить с помощью метода `analyticsCalls`. В отличие от записи видео идентификатор `callId` для аналитики нельзя получить с помощью вебхука для события `call_finished`. Но обратный вызов для этого события означает формирование аналитики после мероприятия (т.е. нет смысла запрашивать аналитику, пока не получено уведомление о событии `call_finished`).

В строке запроса `getRoomAnalyticsReport` как часть URL обязательно передается идентификатор группы `spaceId`, а также в качестве обязательных параметров запроса передается идентификатор комнаты и идентификатор вызова \- параметры `roomId` и `callId`. Также указывается параметр `format`, который определяет формат выгрузки отчета и может принимать значение xlsx или json.

Идентификаторы группы и комнаты для заданной вебинарной комнаты должны быть известны (см. раздел Создание групп и комнат). А получить идентификатор вызова `callId` можно с помощью метода `analyticsCalls` (см. описание ниже).

Полная спецификация метода `getRoomAnalyticsReport` приведена в [Swagger](https://moodhood-api.teleboss.ru/doc/#/SpaceAnalytics/getRoomAnalyticsReport).

Пример выполнения запроса `getRoomAnalyticsReport`:

**`GET`** `https://moodhood-api.teleboss.ru/v1/spaces/60d55c0eb9ef88ab17b0aabb/analytics/report?roomId=60d55c0eb9ef88ab17b0aabb&callId=60d55c0eb9ef88ab17b0aabb&format=xlsx`

В результате выполнения запроса возвращается аналитика по событию в формате JSON или XLSX.

Для получения `callId` нужно использовать метод [`analyticsCalls`](https://moodhood-api.teleboss.ru/doc/#/SpaceAnalytics/analyticsCalls). В строке запроса как часть URL обязательно передается идентификатор группы `spaceId`, а также в качестве обязательных параметров запроса передается идентификатор комнаты и дата, за которую надо получить аналитику \- параметры `roomId` и `date`. Дополнительно можно указать, например, фильтр по минимальному количеству участников события.

Полная спецификация метода приведена в [Swagger](https://moodhood-api.teleboss.ru/doc/#/SpaceAnalytics/analyticsCalls).

Пример выполнения запроса `analyticsCalls`:

**`GET`** `https://moodhood-api.teleboss.ru/v1/spaces/60d55c0eb9ef88ab17b0aabb/analytics/calls?roomId=60d55c0eb9ef88ab17b0aabb&date=2023-08-01T12%3A25%3A40.253Z`

В результате выполнения запроса возвращается JSON со списком завершенных вызовов за указанную дату.

```
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

Значение `id` \- это и есть идентификатор вызова, который необходимо передать в качестве параметра `callId`, в методе `getRoomAnalyticsReport` для получения аналитики.

**Важно**\! Мы рекомендуем собирать данные аналитики после окончания мероприятия, добавив небольшой дополнительный промежуток времени. Также можно делать это после получения вебхука (webhook) о завершении, которое приходит только после начала мероприятия. Это поможет избежать ситуации, когда вы получаете пустые данные, например, если администратор случайно зашел в комнату до начала мероприятия и вышел, а затем вернулся к старту встречи. В этом случае звонок завершится, и уведомление придет, но данные будут не по самому мероприятию.

# **Дополнительная информация**

## **Построение дашбордов по успеваемости прохождения курсов пользователями в различных разрезах**

При вызове метода generateAccess, которым генерируется ссылка на вход в комнату: [`https://moodhood-api.teleboss.ru/v1/spaces/{spaceId}/rooms/{roomId}/generate-access`](https://moodhood-api.teleboss.space/v1/spaces/60d55c0eb9ef88ab17b0aabb/rooms/60d55c0eb9ef88ab17b0aabb/generate-access)
необходимо указать два дополнительных параметра:

`"externalUserId": "string"` \- Уникальный идентификатор пользователя в вашей LMS системе.  Пользователь может быть учеником, преподавателем или иметь какую-то другую роль. Он должен быть одинаковым для одного и того же ученика на всем времени его обучения. Для преподавателя \- аналогично.
`"externalMeetingId": "string"` \- Уникальный идентификатор конкретного занятия. Должен быть одинаковым для всех пользователей посещающих это занятие.  Может генерироваться как угодно И используется только для связи данных о поведении ученика во время конкретного занятия с метаданными о самом занятии.

Далее необходимо настроить передача метаданных о занятии и ученике. Метод storeExternalUserMeta [`https://moodhood-api.teleboss.ru/doc/#/Users/storeExternalUserMeta`](https://moodhood-api.teleboss.ru/doc/#/Users/storeExternalUserMeta) нужно вызывать для каждой ссылки сгенерированной через generateAccess.

`"externalUserId": "string"` \- те же самые значения, что указывались выше в generateAccess ссылке.
`"externalMeetingId": "string"` \- те же самые значения, что указывались выше в generateAccess ссылке.
`"meta": {}` \- передается дополнительная информация о пользователей и о занятии. Формат данных (JSON) должен быть следующим:

```
{
"lesson_scheduled_start_dt" : "2024-12-12 09:00"  //плановое дата время начала урока по UTC
  "lesson_scheduled_end_dt" : "2024-12-12 10:00"   //плановое дата время окончания урока по UTC, опцинально
  "lesson_name" : "Эмоции: радость"  // Можно также указывать, другие параметры урока, которые было бы полезно видеть в аналитике или использовать в дальнейшем для интеграции
 
  "user_fullname" : "Иван Петров" //имя фамилия юзера
  "user_role" : "student" //роль пользователя. Допустимые значения:
                  //"slesson_scheduled_start_dttudent" - учитель,
                  //"teacher" - преподаватель,
                  //"parent" - родитель,
                  //"assistant" - ассистент или другой представитель школы.
  "student_group" // информация о принадлежности студента к группе
  {
  "student_group_id" : "ddfa1488" // уникальный идентификатор группы учеников
  "student_group_name" : "Группа 228"  // название группы
  }
  "lesson_course" // информация о принадлежности занятия (или студента) к курсу
  {
  "lesson_course_id" : "dsfad333" // уникальный идентификатор курса
  "lesson_course_name" : "Эмоциональный интеллект"  // название курса
  }
```
Подобным образом можно задавать любые кастомные иерархии для агрегации, в разрезах которых может быть полезна аналитика.

## **Часто задаваемые вопросы**

### Авторизация и доступ пользователей

**Можно ли предоставить доступ к записям напрямую с платформы для всех пользователей через API?**

Мы не предоставляем общий доступ к облачной записи для обычных пользователей, только для администратора.

**При тестировании столкнулась с проблемой: при попытке учеником присоединиться к вебинару приходит ответ Unauthorized.**

Предположительно, браузер не сохраняет файлы cookie, что приводит к тому, что аутентификация не работает. Попробуйте в настройках браузера включить разрешение браузеру к хранилищу. Для браузера Chrome - раздел настроек *Конфиденциальность и безопасность > Файлы cookie и другие данные сайтов*. 

**Ошибка при получении токена пользователя. Использовали учетные данные клиента и `grant_type: password`, а так же учетные данные пользователя. Попробовали вариант с передачей `access token` в заголовке и без него, в обоих случаях приходит ответ `{"error":"invalid_grant","error_description":"Invalid credentials"}`**

Тут есть определённая путанница в авторизации. В соответствии с требованиями стандарта RFC oauth2 при получении токена с `grant_type: password`, поле с идентификатором пользователя обязано именоваться username. Но идентификатором пользователя у нас выступает email, так как username не уникален. Таким образом, при получении токена пользователя в поле username надо указывать email пользователя.

**Запрос GET `https://moodhood-api.teleboss.ru/v1/spaces/` возвращает ошибку авторизации *403 Данный метод запрещен для пользователя*. Токен запрашиваю: `"grant_type": "client_credentials"` - возможно, тут надо использовать другой тип? Какой тип следует указать для получения отчетов по работе в комнате, информации по вебинарам?**

Создавать спэйсы\комнаты и получать данные по ним можно только под токеном пользователя - `grant_type:password`. Подробнее [см. здесь](#создание-токена-пользователя).

**Как сделать конференцию только для опредленных людей (чтобы никто случайно не вошел, даже зная ссылку)?**

У комнаты есть признак приватности и в неё смогут зайти только те люди, которым выдан доступ. Т.е. даже владелец спейса в приватную комнату зайти не сможет, если ему создатель этой комнаты не добавил права на её посещение.

**Можно ли пригшашать в комнату пользователя по предварительной авторизации (например, используя refresh hash), чтобы ему ничего не пришлось вводить при входе в комнату, и он сразу заходил в нее?**

Есть метод `roomGenerateAccess` полностью автоматизирующий этот процесс, подробная [инструкция здесь](#предоставление-прав-доступа-к-комнате-незарегистрированному-пользователю). Спецификация метода [в Swagger](https://moodhood-api.teleboss.ru/doc/#/RoomGenerateAccess/roomGenerateAccess).

**А по пользователям — доступны только эти переменные? Или можно передавать еще и ID с платформы?
Актуально, если, например, сменил почту и/или имя, а ID на платформе постоянный.**

Смотря о каком методе мы говорим. Если `generate-acccess`, то да. Там можно указать `externalUserId` - это идентификатор пользователя из вашей системы и по нему можно в аналитике соотнести ваших и наших пользователей.

### Группы и комнаты

**В инструкции указан пример URL
`https://moodhood-api.teleboss.ru/v1/spaces/60d55c0eb9ef88ab17b0aabb/rooms/60d55c0eb9ef88ab17b0aabb/generate-access` - там всегда дважны используется идентфикатор комнаты (<room_alias>)? Получается, что запрос пойдет такой `https://moodhood-api.teleboss.ru/v1/spaces/<room_alias>/rooms/<room_alias>/generate-access`**

Не совсем, вот корректная структура запроса:
`https://moodhood-api.teleboss.ru/v1/spaces/<space_id>/rooms/<room_id>/generate-access`

Более подробное описание всех параметров запроса можно посмотреть [в Swagger](https://moodhood-api.teleboss.ru/doc/#/RoomGenerateAccess/roomGenerateAccess).

**Обратил внимание, что ссылка формируется в примере только с room_alias.**

Ссылки на открытие комнаты всегда формируются по alias, верно. А в работе с методами самого API всегда используются идентификаторы.

**Насколько вообще нужена группа, если у нас просто отдельный iFrame с уроком? Создание группы это опционально и ее может не быть? Или группа есть всегда?**

Группа всегда есть. К группе можно относиться просто как к директории для комнат. Можно создать их несколько, а можно все комнаты хранить в одной группе. Это родительский компонент для комнаты и всех имеющихся сущностей в конференции.

**В ссылке-приглашении на комнату `https://a.teleboss.ru/room/0mrw9W62rC` не указан spaceId, значит группа опцинальна?**

В ссылке используется alias, это сделано просто для сокращения длины ссылки, так как alias сам по себе уникален.

**Могу ли я сделать запрос без указания spaceId? Чтобы создателю было достаточно лишь указать просто ссылку на комнату**

Нет, если посмотреть [в Swagger](https://moodhood-api.teleboss.ru/doc/#/SpaceRooms), то все запросы в API, касающиеся комнат содержат в URL и идентификатор spaceId тоже.

**Чем отличается группы от комнат и зачем они нужны (группы) на одной учетой записи? Не до конца понятна логика.**

Группы - это просто иерархическая единица, включающая в себя комнаты. Группа может быть одна. А может быть несколько, для удобства, так как каждое из них может иметь свои настройки приватности.

**Может ли быть несколько админов у вебинарной комнаты?**

Может быть неограниченное количество админов в вебинаре, но на экране отображается всего трое. Их можно чередовать, но чем больше админов участвуют именно в вещании (находятся на экране с включенными камерами\микрофонами), тем более высокая нагрузка.

### Получение статистики

**Нам нужна возможность в начале сессии передать Email пользователя, чтобы в конце сессии получить данные о посещаемости. В идеале нам надо каким-то методом отправлять запрос и получать список посещений, в виде адреса емейл и времени, проведённого на занятии, а также общее время встречи.**

В [этом разделе](https://moodhood-api.teleboss.ru/doc/#/RoomGenerateAccess/roomGenerateAccess) описано, как сгенерировать готовую ссылку для iFrame для вашего пользователя с нужными правами и идентификатором пользователя из вашей системы, по которому вы сможете найти в аналитическом отчёте информацию о его активности в звонке, сообщениях в чате и др.

**Какие методы в Swagger отвечают за возможность показа статистики пользователям? Например, вебинар был обучающий. Getcourse, например, предоставляет доступ к шкале достижения оценок, статистике прохождения уроков, посещаемости, расписанию уроков.**

Вы можете собирать статистику по участникам мероприятий и результаты голосований по API в формате JSON. И из этих данных на своей стороне формировать любые типы отчетов. Вам могут пригодиться следующие методы:
* Метод для получения статистики: [getRoomAnalyticsReport](https://moodhood-api.teleboss.ru/doc/#/SpaceAnalytics/getRoomAnalyticsReport)
* Метод для работы с участниками: [ManagementAPI](https://moodhood-api.teleboss.ru/doc/#/RoomGenerateAccess/roomGenerateAccess)
* Метод для работы с опросами: [RoomPolls](https://moodhood-api.teleboss.ru/doc/#/RoomPolls)   

**Нужно получить статистику по вебинару, проведенному в комнате с заданными spaceId > roomId в определенное время. Какой метод для этого используется?**

Первым делом необходимо запросить звонки, совершённые в комнате за интересующую дату с помощью метода [analyticsCalls](https://moodhood-api.teleboss.ru/doc/#/SpaceAnalytics/analyticsCalls). Затем, используя необходимый `callId`, получить аналитический отчёт в необходимом вам формате с помощью метода [getRoomAnalyticsReport](https://moodhood-api.teleboss.ru/doc/#/SpaceAnalytics/getRoomAnalyticsReport).

**Статистика доступна для всех? Статистика пользователей и их действий (реакций, опросов, чатов). Говорят, у беcплатных групп нету статистики?**

Статистика доступна только модераторам и администраторам на спэйсах, имеющих тариф с доступом к статистике. На всех тарифах.

### Интеграция

**Зачем тестировать интеграцию? Вообще на нашей платформе это лишь iFrame, который не дает никакой нагрузки на саму платформу, это чисто клиентская история (фронтэнд). Это равносильно тому, что просто будет открыта страница с сервисом и никакой трафик непосредственно через наши сервера в данном случае не идет.**

Все боты почти одновременно пройдут процедуру авторизации в вашем сервисе. Этот момент мы и тестируем, так как в некоторых случаях даже такой небольшой одновременный вход оказывался критичным.

**В хуках, которые вы отправляете, есть событие: *record_failed - Во время облачной записи произошла ошибка*. Стоит ли мне при этом событии заново стартовать запись? Она автоматические не стартует при ошибках?**

Да, в случае ошибки лучше начать заново, мы автоматически этого не делаем.

### Разное

**Будет ли приходить нашим пользователям ваша рассылка?**

При создании пользователей через API они не попадают в наши рассылки. Более того, мы пока этой опцией (рассылками) не пользуемся. Отправляем письма только для восстановления паролей. Но это не ваш случай, т.к. вы создаете на нашей стороне временных пользователей.

**После оканчания оплаченного тарифа он сменился на тариф Basic. Я ожидала, что будет удаление аккаунта. Это баг?**

Это не баг. На нашей стороне просто отключается платный тарифный план, все правильно.

**Каждому новому мероприятию присваиваеться ID? То есть, если вебинар был 21.12 в 10.00 и потом в 13.00, обоим мероприятиям будет назначен разный ID? Или следует по времени сортировать?**

Да, у каждого звонка свой идентификатор. Отдельная сущность для каждого звонка создаётся в момент входа первого пользователя в комнату и заканчивается в момент выхода последнего.

**Какой метод используется для получения списка записей?**

Документацию по работе с записью вебинаров можно посмотреть по [этой ссылке](https://moodhood-api.teleboss.ru/doc/#/RoomRecords/get_spaces__spaceId__rooms__roomId__records).

**Cмена типа с вебинара на конференцию возможна после проведения? Это на что-то влияет, на представление комнаты, например?**

Это deprecated функционал, скоро будет удалён. Настоятельно рекомендуем не изменять тип комнаты.

**Могу ли я изменить владельца группы (space/спейса)?**

Мы настоятельно не рекомендуем использовать функционал замены владельца, иначе комнаты могут работать некорректно.