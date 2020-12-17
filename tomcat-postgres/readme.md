# メモ

## 実行環境について

tomcat(8.5.61)とpostgres(10.11)，およびPostgreSQLを操作するためのpgAdminが含まれる

* tomcatは，ホスト側からは 8080 ポートでアクセス可能（リモートデバッグ用に 8000 ポートも開けておく）
* PostgreSQLはホスト側から，5432ポートでアクセス可能
* pdAdminはホスト側から，8801ポートでアクセス可能（不要なら削除してよい）

コンテナの名称はそれぞれ以下の通り（右側がコンテナ名）。
* tomcat → app
* postgresql → pgdb
* pgAdmin → pgadm


### warファイルのデプロイ

`tomcat/cont/Catalina/localhost`にあるXMLファイルに合わせたWARファイルを `webapps` に配置する。XMLファイルの名称や数はすきに変更してよい

### サンプルデータベース

JDBC接続文字列とアカウントは以下の通り。

|DB名|JDBC接続文字列|アカウント（ID/pass）|
|------|------|------------------|
|postgres|jdbc:postgresql://localhost:5432/postgres|postgres / password|

サンプルデータは `postgres/initdb.d` に従って作成（好きに直して良い）

