# basic-auth-api

## 概要
Basic認証をGolangで実装した．  
フレームワークにGin，データベースはMySQL，ORMにGormを使用している．  
アーキテクチャはMVCに準じている．  
swaggerはまだ編集していないので，後ほど気が向けば編集する．  

機能の概要は以下の通りである．
- POST /signup ユーザアカウント作成
- GET /users/{user_id} ユーザ情報の取得
- PATCH /users/{user_id} ユーザ情報の更新
- POST /close ユーザアカウントの削除

## 実装した機能
- [POST] /signup
  - Request: 
    - {"user_id", "password"} 
    - ユーザIDは6\~20文字かつ半角英数字のみ
    - パスワードは8\~20文字かつ半角英数字記号(空白と制御文字除くASCII文字)
  - Response: 
  - 成功
    - {"message": "Account successfully created", "user": {"user_id": "hogehoge", "nickname": "fugafuga"}}  /HttpStatusCode(200)　
  - 失敗
    - {"message": "Account creation failed", "cause": "(原因)"} /HttpStatuseCode(400)　
- [GET] /users/{user_id}
  - Request: 
    - Authorizationヘッダ: Basic <Base64エンコードされた {user_id} + ":" + {password}>
  - Response:
  - 成功
    - {"message": "User detail by user_id", "user": {"user_id": "hogehoge", "nickname": "fugafuga", "comment": "piyopiyo"}} /HttpStatusCode(200)
    - ニックネームの初期値はユーザID
  - 失敗
    - {"message": "No user found"} /HttpStatusCode(404)
    - {"message": "Authorization failed"} /HttpStatusCode(400)
- [PATCH] /users/{user_id}
  - Request:
    - Authorizationヘッダ: Basic <Base64エンコードされた {user_id} + ":" + {password}>
    - {"nickname", "comment"}
    - ニックネームは30文字以内，制御文字除く任意の文字，空文字列で更新された場合はユーザIDに戻る
    - コメントは100文字以内，制御文字除く任意の文字，空文字列で更新された場合は空文字列となる
  - Response:
  - 成功
    - {"message": "User successfully updated", "recipe": {"nickname": "hogehoge", "comment": "fugafuga"}} /HttpStatusCode(200)　
  - 失敗
    - {"message": "No user found"} /HttpStatusCode(404)
    - {"message": "User updation failed", "cause": "(原因)"} /HttpStatusCode(400)
    - {"message": "Authorization failed"} /HttpStatusCode(401)
- [POST] /close
  - Request:
    - Authorizationヘッダ: Basic <Base64エンコードされた {user_id} + ":" + {password}>
  - Response:
  - 成功
    - {"message": "Account and User successfully removed"} /HttpStatusCode(200)　
  - 失敗
    - {"message": "Authorization failed"} /HttpStatusCode(401)

## 動作確認
1. cp .env.sample .env
1. docker compose up -d
1. 「サインアップ」 curl -X POST -H "Content-Type: application/json" -d '{"user_id":"rintaro", "password":"password"}' localhost:8080/signup
2. 「ユーザ要素の取得」 curl -H "Authorization: Basic cmludGFybzpwYXNzd29yZA=="  localhost:8080/users/rintaro
3. 「ユーザ要素の更新」 curl -X PATCH -H "Content-Type: application/json" -d '{"nickname":"rinchan", "comment":"saikou"}' -H "Authorization: Basic cmludGFybzpwYXNzd29yZA=="  localhost:8080/users/rintaro
4. 「ユーザの削除」 curl -X POST -H "Authorization: Basic cmludGFybzpwYXNzd29yZA=="  localhost:8080/close/
