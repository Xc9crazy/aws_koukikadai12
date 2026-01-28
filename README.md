セットアップ手順書

前提条件

Docker、Docker Composeがインストールされていること
ポート80、6379が使用可能であること

手順

1. プロジェクトの配置
ダウンロードしたzipファイルを解凍し、任意のディレクトリに配置する。

aws_kadai12/
├── compose.yml
├── Dockerfile
├── php.ini
├── public/
├── nginx/
└── *.sql

2. Docker環境の起動

プロジェクトのルートディレクトリで以下のコマンドを実行する。

bashdocker compose up -d

初回起動時はイメージのビルドに時間がかかる場合がある。

3. データベースのセットアップ

MySQLコンテナに接続してテーブルを作成する。

bashdocker compose exec mysql mysql -u root example_db

MySQLプロンプトで以下のSQLファイルを順番に実行する。

sqlsource /var/lib/mysql/create_users.sql;

source /var/lib/mysql/create_bbs_entries.sql;

source /var/lib/mysql/create_user_relationships.sql;

source /var/lib/mysql/create_access_logs.sql;

または、コンテナ外から直接実行する。

bashdocker compose exec mysql mysql -u root example_db < create_users.sql

docker compose exec mysql mysql -u root example_db < create_bbs_entries.sql

docker compose exec mysql mysql -u root example_db < create_user_relationships.sql

docker compose exec mysql mysql -u root example_db < create_access_logs.sql

4. アプリケーションへのアクセス

ブラウザで以下のURLにアクセスする。

http://localhost

http://パブリックアドレス/

5. 動作確認

以下のページが正常に表示されることを確認する。

ログインページ: http://localhost/login.php または http://パブリックアドレス/login.php

会員登録ページ: http://localhost/signup.php または http://パブリックアドレス/signup.php

会員登録後、ログインしてタイムラインが表示されることを確認する。

停止と再起動

停止
bashdocker compose down

再起動
bashdocker compose up -d

トラブルシューティング

ポートが使用中の場合
compose.ymlのポート番号を変更する。

yamlservices:
  web:
    ports:
      - 8080:80  # 80を8080に変更

アクセスURLは http://localhost:8080 となる。

データベース接続エラーの場合

コンテナが正常に起動しているか確認する。

bashdocker compose ps

全てのコンテナが "running" になっていることを確認する。

ログの確認

bashdocker compose logs web

docker compose logs php

docker compose logs mysql

開発時の注意点

ファイルの編集
public/ 内のファイルを編集した場合、変更は即座に反映される。ブラウザをリロードして確認する。

CSSの適用
style.css は public/style.css に配置されており、全ページで読み込まれる。

データベースの初期化

データベースをリセットする場合は、以下のコマンドを実行する。

bashdocker compose down -v

docker compose up -d

その後、再度SQLファイルを実行してテーブルを作成する。

**なお、そのほかの環境構築や、コードにおいては前期・後期の授業でやったものを参考、使用している。**
