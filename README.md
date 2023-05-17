# sign-server

## 概要
＠SaKu2110まで  

## 実装した機能
- [GET] /ping
  - Request: なし
  - Response: {"message": "ping"} / HttpStatusCode(200)
- [POST] /signin
  - Request: Header: UserId `string`, Password `string`
  - Response: {"token": 任意の文字列} / HttpStatusCode(200)

- [POST] /signup
  - Request: Header: UserId `string`, Password `string`
  - Response: {"token": 任意の文字列} / HttpStatusCode(201)

## 実行手順
1. git clone "URL about this repo"
1. cd backend_tutorial
1. cp .env.sample .env
1. docker compose up
1. curl -c cookie.txt -X POST --url http://localhost:8080/signup -d 'UserId=rintaro' -d 'Password=pass'
1. curl -b cookie.txt -X POST --url http://localhost:8080/signin -d 'UserId=rintaro' -d 'Password=pass'