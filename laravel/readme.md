# メモ

## 実行環境について

laravel環境（nginx+php+mysql）が含まれる
* 参考：https://qiita.com/ucan-lab/items/56c9dc3cf2e6762672f4

空の`backend`ディレクトリを作っておくこと（あとで，ここにLaravelプロジェクトを作る）

### Laravelプロジェクトの作成

`docker-compose up`が済んだら，`app-bash.cmd`を叩いてphpコンテナに入り以下のコマンドを実行する（pwdは`/work`）。

```
composer create-project --prefer-dist "laravel/laravel=8.*" .
```

できあがったLaravelプロジェクトはホスト側の `./backend` から編集できる。

### MySQLについて

* docker-compose.yml の db 部分をmysqlにしておく
* jdbc:mysql://localhost:3306/laravel_local
  * phper / secret

Laravelから見えるように `backend/.env` を以下のように修正する
```
DB_CONNECTION=mysql
DB_HOST=db
DB_PORT=3306
DB_DATABASE=laravel_local
DB_USERNAME=phper
DB_PASSWORD=secret
```

### PostgreSQLについて

* docker-compose.yml の db 部分をpostgresにしておく
* jdbc:postgresql://localhost:5432/laravel_local
  * postgres / password

Laravelから見えるように `backend/.env` を以下のように修正する
```
DB_CONNECTION=pgsql
DB_HOST=db
DB_PORT=5432
DB_DATABASE=laravel_local
DB_USERNAME=postgres
DB_PASSWORD=password
```

## プロジェクトをIntelliJで開く

* `backend`をWebモジュールとして登録する
* 