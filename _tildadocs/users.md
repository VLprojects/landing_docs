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
