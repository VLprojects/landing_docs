---
layout: default
title: "Конфигурирование настроек группы, комнаты"
nav_order: 1400
parent: Интеграция
---
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
