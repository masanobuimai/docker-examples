# メモ

## 実行環境について

laravel環境として，apache/php+postgresが含まれるdocker-composeを提供する。

* 参考：
  * https://qiita.com/ucan-lab/items/56c9dc3cf2e6762672f4
  * https://qiita.com/cyclon2joker/items/39e620d3d16fa1f6edf0
  * https://github.com/maip0902/DockerApachePHP

## 初期設定

### Laravelプロジェクトの作成

1. 空の`backend`ディレクトリを作っておくこと（あとで，ここにLaravelプロジェクトを作る）
1. `.env.example`をコピーして`.env`を作り，プロキシサーバの設定を記述する
1. `docker-compose up`を実行して，phpコンテナにログインする（`app-bash.cmd`を実行するとログインできる）
1. phpコンテナ内で以下のコマンドを実行する（pwdは`/var/www/html`）。
```
composer create-project --prefer-dist "laravel/laravel=8.*" .
composer require --dev barryvdh/laravel-ide-helper
composer require --dev nunomaduro/larastan
```

※このタイミングでide-handlerをインストールしないとartisanにコマンドが追加されなかった（なんでだろ？）

できあがったLaravelプロジェクトについて
* ホスト側から `http://localhost:10080/` でアクセスできる
* ホスト側の `./backend` から編集できる
* `artisan` などLaravelのコマンドはphpコンテナ内で行う

### デバッガを一時的に無効にする

`/usr/local/etc/php/php.ini`の`zend_extension=～`をコメントアウト（先頭に`;`）する

### PostgreSQLの設定

Laravelから見えるように `backend/.env` を以下のように修正する

```properties
DB_CONNECTION=pgsql
DB_HOST=postgres
DB_PORT=5432
DB_DATABASE=laravel_local
DB_USERNAME=postgres
DB_PASSWORD=password
```

`php artisan migrate`を実行するとDBにテーブルができあがる

### コードインスペクション

#### PHP_CodeSniffer

PhpStormで上手く動かないので入れるの止めた。

* https://github.com/squizlabs/PHP_CodeSniffer

インストール

```
composer require --dev squizlabs/php_codesniffer
```

使用方法

 * https://www.ritolab.com/entry/188

こんな感じの `phpcs.xml` をルートディレクトリに作る
```yml
<?xml version="1.0"?>
<ruleset name="PSR12/Laravel">
  <description>PSR12 compliant rules and settings for Laravel</description>
  <arg name="extensions" value="php" />
  <rule ref="PSR12" />
  <arg name="colors" />
  <arg value="ps" />
  <exclude-pattern>/bootstrap/</exclude-pattern>
  <exclude-pattern>/config/</exclude-pattern>
  <exclude-pattern>/database/</exclude-pattern>
  <exclude-pattern>/node_modules/</exclude-pattern>
  <exclude-pattern>/public/</exclude-pattern>
  <exclude-pattern>/resources/</exclude-pattern>
  <exclude-pattern>/routes/</exclude-pattern>
  <exclude-pattern>/storage/</exclude-pattern>
  <exclude-pattern>/vendor/</exclude-pattern>
  <exclude-pattern>/server.php</exclude-pattern>
  <exclude-pattern>/app/Console/Kernel.php</exclude-pattern>
  <exclude-pattern>/tests/CreatesApplication.php</exclude-pattern>
</ruleset>
```

`phpcs`を実行する

```
./vendor/bin/phpcs --standard=phpcs.xml ./
```


#### Larastan

* https://github.com/nunomaduro/larastan

インストール

```
composer require --dev nunomaduro/larastan
```

こんな感じの設定ファイル `phpstan.neon`を用意する
```
includes:
  - ./vendor/nunomaduro/larastan/extension.neon

parameters:
  paths:
    - app
  level: 5
  checkMissingIterableValueType: false
```

使用方法

```
./vendor/bin/phpstan analyse
```

`composer.json`のスクリプトに登録する

```
"scripts": {
    :
  "larastan": [
    "@php vendor/bin/phpstan analyze"
  ]
}
```

## IntelliJ（PhpStorm）から開発する

`backend`を指定してプロジェクトを開く

### PHPの設定

#### インタープリターの設定

1. 「File | Settings | 言語 & フレームワーク | PHP」を開く
1. 「CLIインタープリター」を追加する（「From Docker, Vagrant, VM, WSL, Remote...」を選ぶ）
1. 「Docker Compose」を選び，「サービス」に「app」を指定する
   * `docker-compose.yml`は`./backend`の **ひとつ上** にあるほうを選ぶ

#### artisanコマンドの設定

「File | Settings | ツール | コマンドラインツールのサポート」に以下のコマンドを追加する

* ツール選択：Laravel
* エイリアス：artisan
* 「pharまたはphpスクリプト」を選択
  * 「PHPインタープリター」に **インタープリターの設定** で追加したPHPを指定（app）
  * 「スクリプトのパス」に `artisan` を指定

以降，Ctrl×2（Run anywhere）から `artisan` が使える

#### あると便利そうなプラグイン

* https://plugins.jetbrains.com/plugin/7276-php-advanced-autocomplete
  * PHPのコード補完強化
* https://plugins.jetbrains.com/plugin/7532-laravel
  * Laravelサポートプラグイン（Laravel Ideaは有料）
* https://plugins.jetbrains.com/plugin/9525--env-files-support
  * .envファイルのサポート
* https://plugins.jetbrains.com/plugin/14756-laravel-live-templates
  * Laravel用ライブテンプレートのセット
* https://plugins.jetbrains.com/plugin/13762-tree-of-usages
  * PHPの関数（Function）の使用箇所を追跡する
* https://plugins.jetbrains.com/plugin/14612-laravel-make-integration
  * 新規作成にLaravelのコンポーネントを追加する

https://github.com/barryvdh/laravel-ide-helper
https://pleiades.io/help/phpstorm/laravel.html


#### コード補完を有効にする

* https://github.com/barryvdh/laravel-ide-helper

Laravelファサードの補完情報とPhpStorm用の補完用メタファイルの生成。
```
php artisan ide-helper:generate
php artisan ide-helper:meta
```

それぞれカレントディレクトリに以下のファイルができあがる（VCSの管理対象から外した方がよいかも）

* `_ide_helper.php`
* `.phpstorm.meta.php`

`composer.json`に以下の記述を追加すると `composer update`のたびに更新してくれるみたい

```yml
"scripts": {
    "post-update-cmd": [
        "Illuminate\\Foundation\\ComposerScripts::postUpdate",
        "@php artisan ide-helper:generate",
        "@php artisan ide-helper:meta"
    ]
},
```

モデルの補完情報の生成。
```
php artisan ide-helper:models --nowrite
```

カレントディレクトリに `_ide_helper_models.php` が生成される。`--nowrite`を付けるとモデルを汚さないので，こちらがオススメ。


### データベースの設定

```
jdbc:postgresql://localhost:5432/laravel_local
postgres / password
```

## VSCodeから開発する

### 必要な拡張

* Remote Development
  * https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack

### はじめに（Remote Containerの設定）

* VSCodeでこのディレクトリを開く
* 左下の「`><`」から「Remote-Container: Reopen in Container」を選ぶ
  * 「From 'docker-compose.yml'」と「app」を選ぶ
  * しばらくするとRemote Containerに接続する
* `.devcontainer/devcontainer.json`を開き，以下のように修正する
  * `"workspaceFolder": "/workspace"` を `"workspaceFolder": "/var/www/html"` に書き換える
  * `"extensions": []`を以下のように書き換える
```yml
"extensions": [
  "felixfbecker.php-intellisense",
  "felixfbecker.php-debug",
  "onecentlin.laravel-extension-pack",
  "ms-ossdata.vscode-postgresql",
  "editorconfig.editorconfig",
  "calebporzio.better-phpunit",
  "neilbrayfield.php-docblocker"
```
* `.devcontainer/docker-compose.yml`を開き，最後の行（`command:～`）をコメントアウトする
* 左下の「`><`」から「Remote-Container: Reopen Locally」を選び，Remote Containerに再接続する
  * ローカルに戻ると「Reopn in Containerする？」と聞かれるので，再度Remote Containerに接続する
  * 途中コンテナを「Rebuildするか？」と聞かれるのでRebuildする

上記の手順でやったこと
* Remote Containerを有効にする
* Remote ContainerにPHP用のVSCode拡張を追加する
* Remote Containerの作業ディレクトリを `/var/www/html`（ローカルの`./backend`）に設定する

#### デバッガの設定

* Remote Containerに接続した状態で「Run (Ctrl+SHIFT+D)」を選び「create a launch.json file」をクリック，PHPを選ぶ
* `launch.json`を以下の内容にする（`./backend/.vscode`にできる）
```yml
{
  "version": "1.2.0",
  "configurations": [
    {
      "name": "Listen for XDebug",
      "type": "php",
      "request": "launch",
      "port": 9003
    },
  ]
}
```

#### PostgreSQLの接続設定

* コマンドパレット（Ctrl+SHIFT+P）から「PostgreSQL:New Query」を実行する
* それぞれの質問に以下のように答える
  * プロファイルの選択 : Enter（Create Connection Profileを選ぶ）
  * Server name : `postgres`
  * Database name : `laravel_local`
  * User name : `postgres`
  * Password : `password`
  * Port : Enter （デフォルト:5432のまま）
  * Profile Name : `postgres` （次回以降，このプロファイルを選べば良い）
* クエリーシートにSQLを入力して，コンテキストメニューから「Execute Query」を選ぶと実行する


### 初回以降

`.devcontainer`があるディレクトリを開けば「Remote Containerで開く？」と聞いてくるので，それに従うだけ。

* デバッガはRemote Container内で，「Run (Ctrl+SHIFT+D)」→「Listen for XDebug」を選べば有効になる
* `artisan`コマンドはコマンドパレットから「`Artisan: ～`」を実行する
* PHPUnitの実行はテストコードを開き，コマンドパレットから「`Better PHPUnit: run`」や「`Better PHPUnit: run suite`」を選ぶ


## TODO（順不同）
* ~~nginxをapacheに変更する~~
* ~~phpのデバッガ設定~~
* ~~VSCodeでの利用方法~~
* duskの利用方法
* ~~phpUnitの利用方法~~
* その他テストフレームワークの利用方法
* コードインスペクションの調査と利用方法
* その他便利機能があれば追加する
