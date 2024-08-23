# environment

## リポジトリ

リモートリポジトリをローカルにクローン
```sh
$ git clone https://github.com/Conovel/environment.git
```

リポジトリに移動
```sh
$ cd environment
```

.gitmodulesでfrontendとbackendのリポジトリを読み込んでいます。

サブモジュールの初期化と更新
```sh
$ git submodule update --init --recursive
```

## Dockerコンテナ

※Docker起動時はDocker desktopを起動する

Dockerコンテナを起動
```sh
# バックグラウンドで起動
$ docker-compose up -d

# フォアグラウンドで起動
$ docker-compose up
```

Dockerコンテナ再起動
```sh
$ docker-compose restart
```

Dockerコンテナの停止
```sh
$ docker-compose down

# 不要なコンテナのクリーンアップ
$ docker-compose down --remove-orphans
```

Dockerコンテナのビルド
```sh
$ docker-compose build
```

ログの出力
```sh
$ docker-compose logs

# 特定のコンテナのログ
$ docker-compose logs db
```

### データベースの手動生成
通常、データベースのデータはDockerボリュームを使用して永続化されるため、コンテナの再起動後もデータは保持されます。そのため、手動でデータベースの初期化やマイグレーションを行う必要はありません。

もしデータベースの初期化やマイグレーションが必要な場合（例：エラーが発生した場合やデータベースをリセットする必要がある場合）、以下の手順を実行してください。

#### 1. バックエンドコンテナにアクセス
バックエンドコンテナにアクセスします。
```sh
$ docker-compose exec backend bash
```

#### 2. データベースの作成
バックエンドコンテナ内で、以下のコマンドを実行してデータベースを作成します。
```sh
bin/rails db:create
```

#### 3. マイグレーションの実行
データベースのマイグレーションを実行します。
```sh
bin/rails db:migrate
```

#### 4. サーバーの起動
最後に、Railsサーバーを起動してアプリケーションが正しく動作することを確認します。
```sh
bin/rails server -b 0.0.0.0
```

# ローカル環境のurl
docker起動で下記のローカルサーバも起動する
- フロントエンド：http://localhost:3000
- バックエンド：http://localhost:3001