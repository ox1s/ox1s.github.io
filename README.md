

[git hub репозитория (ветка feature/identity-api)](https://github.com/ox1s/dotnet-inno-clinic/tree/feature/identity-api)

Можно использовать Swagger, но я люблю файлы `*.http` в папке `requests` (в solution не добавляла, лежат в файловой структуре)

докер не настраивала, если будешь не просто смотреть - `aspire run` в корне проекта, но в этом надобности нет

# 1. `Register.http`
Осуществляет регистрацию, правила для email  и password я брала с таблиц requirements, что были на платформе, но так же добавила более лучшую проверку для пароля. Ошибки обрабатываются с помощью библиотеки Fluentблабла, выдаются массивом или сразу
### запрос
```json
{
  "email": "string",
  "password": "*1test.com!D"
}
```
#### ответ
```json
{
  "type": "https://tools.ietf.org/html/rfc9110#section-15.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "errors": {
    "Email": [
      "'Email' is not a valid email address."
    ]
  },
  "traceId": "00-c8bc525624203d0951f67e3e14c9ff14-1844243af4415739-01"
}
```

### запрос
```json
{
  "email": "string@test.com",
  "password": "string@test.com"
}
```
#### ответ
```json
{
  "type": "https://tools.ietf.org/html/rfc9110#section-15.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "errors": {
    "General.Validation": [
      "Password too weak"
    ]
  },
  "traceId": "00-949eaead213db6bb8080d8c9b3fe9d4c-c185871e8b2118ac-01"
}
```

### запрос
```json
{
  "email": "string1@test.com",
  "password": "*1test.com!D"
}
```
#### ответ
```json
{
  "id": "26840801-f81f-43c8-a277-acb395d0acf7",
  "email": "string1@test.com",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6InN0cmluZzFAdGVzdC5jb20iLCJpZCI6IjI2ODQwODAxLWY4MWYtNDNjOC1hMjc3LWFjYjM5NWQwYWNmNyIsImV4cCI6MTc2ODU3NDY4NiwiaXNzIjoiSW5ub0NsaW5pYyIsImF1ZCI6Iklubm9DbGluaWMifQ.C8KZkAS6TylbTPtg5vkvA52NrOz3A4vsIXvgfHonfP8"
}
```


# 2. Верификация
Заходим на mailpit в aspire дашборде и нажимаем ссылку с верификацией

# 3. `Login.http`
Заходим в аккаунт

### Запрос
```json
{
  "email": "string1@test.com",
  "password": "*1test.com!D"
}
```
#### Ответ
```json
{
  "id": "26840801-f81f-43c8-a277-acb395d0acf7",
  "email": "string1@test.com",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6InN0cmluZzFAdGVzdC5jb20iLCJpZCI6IjI2ODQwODAxLWY4MWYtNDNjOC1hMjc3LWFjYjM5NWQwYWNmNyIsImV4cCI6MTc2ODU3NDg0MywiaXNzIjoiSW5ub0NsaW5pYyIsImF1ZCI6Iklubm9DbGluaWMifQ.JchXNSc7DCvHuTTaeI3z7AQ9cXeG_RaaVie8qQFvDo4"
}
```

# Вход для доктора
Для этого пока стоит заглушка в виде `FakeProfileService.cs`, чтобы проверить, нужно расскоменировать соответствующие строки. После, когда буду реализовывать 
`ProfilesService` сделаю синхронное взаимодействие для проверки роли (доктор или обычный чел), чтобы чекнуть статус, так как 

|      |                                                                                                                                                                                                                                                                                                                         |
| ---- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| AC-4 | Given the account exists in the system   <br>And the profile linked to the account has status “At work” or “On vacation”, “Sick Day”, “Sick Leave”, “Self-isolation”, “Leave without pay”  <br>  <br>Then the system should display a notification "You've signed in successfully"  <br>  <br>And display the home page |
| AC-5 | Given the account doesn’t exist in the system   <br>  <br>Or the profile linked to the account has status “Inactive”  <br>  <br>Then the system should display a notification “Either an email or a password is incorrect”                                                                                              |


### Запрос
```json
{
  "email": "string1@test.com",
  "password": "*1test.com!D"
}
```

#### Ответ
```json
{
  "type": "https://tools.ietf.org/html/rfc9110#section-15.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "errors": {
    "Account.Inactive": [
      "Your account is inactive or suspended."
    ]
  },
  "traceId": "00-3dd474180e631a74c42c56ee93b7a93f-b5ef7355417149a1-01"
}
```


