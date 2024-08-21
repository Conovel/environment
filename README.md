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
次に、データベースを手動で生成します。

#### Step 1: バックエンドコンテナにアクセス
バックエンドコンテナにアクセスします。
```sh
$ docker-compose exec backend bash
```

#### Step 2: データベースの作成
バックエンドコンテナ内で、以下のコマンドを実行してデータベースを作成します。
```sh
bin/rails db:create
```

#### Step 3: マイグレーションの実行
データベースのマイグレーションを実行します。
```sh
bin/rails db:migrate
```

#### Step 4: サーバーの起動
最後に、Railsサーバーを起動してアプリケーションが正しく動作することを確認します。
```sh
bin/rails server -b 0.0.0.0
```

フロントエンド：http://localhost:3000
バックエンド：http://localhost:3001