# メモ

## 実行環境について

laravel環境（nginx+php+postgresが含まれる
* 参考：
  * https://qiita.com/ucan-lab/items/56c9dc3cf2e6762672f4
  * https://qiita.com/cyclon2joker/items/39e620d3d16fa1f6edf0

## 初期設定

### Laravelプロジェクトの作成

1. 空の`backend`ディレクトリを作っておくこと（あとで，ここにLaravelプロジェクトを作る）
1. `.env.example`をコピーして`.env`を作り，プロキシサーバの設定を記述する
1. `docker-compose up`を実行して，phpコンテナにログインする（`app-bash.cmd`を実行するとログインできる）
1. phpコンテナ内で以下のコマンドを実行する（pwdは`/work`）。
```
composer create-project --prefer-dist "laravel/laravel=8.*" .
```

できあがったLaravelプロジェクトについて
* ホスト側から `http://localhost:10080/` でアクセスできる
* ホスト側の `./backend` から編集できる
* `artisan` などLaravelのコマンドはphpコンテナ内で行う

### PostgreSQLの設定

Laravelから見えるように `backend/.env` を以下のように修正する

```
DB_CONNECTION=pgsql
DB_HOST=postgres
DB_PORT=5432
DB_DATABASE=laravel_local
DB_USERNAME=postgres
DB_PASSWORD=password
```

`php artisan migrate`を実行してみる

## IntelliJ（PhpStorm）から開発する

* `backend`をWebモジュールとして登録する
* docker-compose.yml の db 部分をpostgresにしておく
* jdbc:postgresql://localhost:5432/laravel_local
  * postgres / password
 