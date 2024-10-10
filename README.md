# environment

## リポジトリ

リモートリポジトリをローカルにクローン
```sh
$ git clone https://github.com/Conovel/environment.git
```

※Windows環境だと改行コードが変換されてしまう可能性があります。その場合は`git clone`を実行する前に下記のコマンドを実行してください。
```sh
$ git config --global core.autocrlf input
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

# フォアグラウンドで起動（ログが表示される）
$ docker-compose up
```
- フォアグランドで起動時は常にログが表示されます
- フォアグランドで起動時はターミナルにコマンドを追記できないため、Cntrol + CでDockerを停止するか別のターミナルを開く必要があります

Dockerコンテナの状態を確認
```sh
$ docker ps -a
```
- バックグラウンドで起動時

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
- クリーンアップは大きな修正を行った時など

Dockerコンテナのビルド
```sh
$ docker-compose build
```
- クリーンアップを行った後に実行

ログの出力
```sh
$ docker-compose logs

# 特定のコンテナのログ
$ docker-compose logs db
```

## ローカル環境のurl
docker起動で下記のローカルサーバも起動する
- フロントエンド：http://localhost:3000
- バックエンド：http://localhost:3001

## データベース

### データベースの手動生成
通常、データベースのデータはDockerボリュームを使用して永続化されるため、コンテナの再起動後もデータは保持されます。そのため、手動でデータベースの初期化やマイグレーションを行う必要はありません。

もしデータベースの初期化やマイグレーションが必要な場合（例：エラーが発生した場合やデータベースをリセットする必要がある場合）、以下の手順を実行してください。

#### 1. バックエンドコンテナにアクセス
バックエンドコンテナのシェルにアクセスします（docker起動中に実行）
```sh
$ docker-compose exec backend bash
```
シェルが起動します

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
- 既存テーブルの修正時にも実行

#### 4. シェルから出る
```sh
exit
```

### シードデータの再実行
シードデータを修正した際は、下記のコマンドでシードデータを再度実行します
```sh
docker-compose exec backend bin/rails db:seed
```
- シードデータ：ローカルの開発環境のためにデータベースに登録する初期データ

### データの確認
Railsコンソールを使用して、データが正しく挿入されたか確認します。
```sh
docker-compose exec backend bin/rails console
```

コンソール内で以下のコマンドを実行します。
```sh
ModelName.all # ModelNameはモデル名
```
- Railsコンソールを閉じる→Cmd + C（WindowsはContral + C）
- シェルを閉じる→`exit`

## OpenAPI generator

### enviroment

#### 環境変数
- `.env`ファイルにPROJECT_ROOT追加

例：下記のパスは開発者の環境によって変動します
```sh
PROJECT_ROOT=/Users/hoge/Documents/fuga/conovel/environment
```
- mac or linux などのシェル環境を想定
- Windows環境の場合は[PowerShell](https://learn.microsoft.com/ja-jp/powershell/scripting/samples/managing-current-location?view=powershell-7.4)形式で記載する必要があります

#### ymlファイル
- `conovel_openapi.yml`：APIの定義情報が書かれています

#### APIドキュメント
- `onenapigen/index.html`：上記ymlファイルから生成されるAPIドキュメント

下記のコマンドを実行するとファイルが生成されます
```sh
docker run --rm -v ${PROJECT_ROOT}:/local openapitools/openapi-generator-cli generate -i /local/conovel-openapi.yml -g html -o /local/openapigen
```

もし上記のコマンドが環境変数の設定に失敗する場合、以下のコマンドを実行してシェルセッションに環境変数を設定してください。
```sh
export $(grep -v '^#' .env | xargs)
```
その後、再度OpenAPIドキュメントの生成コマンドを実行してください。

### backend

#### Railsファイル（API設定ファイル）
- `backend/onenapigen`ディレクトリ：上記ymlファイルから生成されるRailsファイル

下記のコマンドを実行するとファイルが生成されます
```sh
docker run --rm -v ${PROJECT_ROOT}:/local openapitools/openapi-generator-cli generate -i /local/conovel-openapi.yml -g ruby-on-rails -o /local/backend/openapigen
```

#### API関連ファイル
下記のファイルを参考にAPI設定をRails本体ファイルに反映してください
- `backend/openapigen/app/controllers/xxxx_controller.rb`: APIエンドポイントのコントローラーが含まれています。ここで各エンドポイントのアクションを定義します（xxx部分はymlファイルの設定によって変動）
- `backend/openapigen/app/models/xxx.rb`: APIで使用されるモデルが含まれています（xxx部分はymlファイルの設定によって変動）
- `backend/openapigen/config/routes.rb`: APIのルーティング設定が含まれています。add_openapi_routeメソッドを使用してルートを追加しています。
- `backend/openapigen/config/application.rb`: Railsアプリケーションの設定が含まれています。
- `backend/openapigen/config/environments/production.rb`: 本番環境の設定が含まれています。
- (`backend/openapigen/db/migrate/xxx_tables.rb`)：データベースのマイグレーションファイル（API仕様の内容のテーブルを作成する内容のため反映には注意が必要）

#### テスト

rspecによるテスト（APIの修正後に実行）
```sh
$ docker-compose exec backend bundle exec rspec
```

#### SwaggerUI
SwaggerUIはopenApiドキュメント

##### SwaggerUIドキュメント生成
```sh
$ docker-compose exec backend bundle exec rake rswag:specs:swaggerize
```
- `http://localhost:3001/api-docs/index.html`で開くとドキュメントが確認できる

##### Swaggerドキュメントの更新
テストファイルの作成/更新: openapiの更新を行った後は`spec/requests/xxxx_spec.rb`などのテストファイルも作成または更新します。
```sh
$ docker-compose exec backend bundle exec rake rswag:specs:swaggerize
```

Swaggerファイルの内容の確認: 生成されたSwaggerファイルの内容が最新のAPI仕様を反映しているか確認します。
```sh
$ docker-compose exec backend cat /app/swagger/v1/swagger.yaml
```

Swaggerファイルの配置: 生成されたSwaggerファイルをpublic/api-docs/v1ディレクトリにコピーします。
```sh
$ docker-compose exec backend mkdir -p /app/public/api-docs/v1
$ docker-compose exec backend cp /app/swagger/v1/swagger.yaml /app/public/api-docs/v1/swagger.yaml
```
- `http://localhost:3001/api-docs/index.html`に変更が反映されない場合はdockerのキャッシュクリア、再ビルド、再起動などを行います

##### SwaggerUIのルーティング設定
`backend/config/routes.rb`の上書き修正などでSwaggerUIのルーティング設定が削除された場合はルーティングエラーになります。その場合は下記のルーティング設定を追加してください
```ruby
# config/routes.rb
Rails.application.routes.draw do
  # 他のルート定義...

  # Swagger UI
  mount Rswag::Ui::Engine => '/api-docs'
  mount Rswag::Api::Engine => '/api-docs'
end
```

### frontend

#### TypeScriptファイル（API設定ファイル）
- `frontend/onenapigen`ディレクトリ：上記ymlファイルから生成されるAxios（typescript-axios）ファイル

下記のコマンドを実行するとファイルが生成されます
```sh
docker run --rm -v ${PROJECT_ROOT}:/local openapitools/openapi-generator-cli generate -i /local/conovel-openapi.yml -g typescript-axios -o /local/frontend/openapigen
```

#### API関連ファイル
下記のファイルを参考にAPI設定をReact本体ファイルに反映してください。
- `frontend/openapigen/configuration.ts`: ConfigurationParametersインターフェースとConfigurationクラスが定義されています。APIの設定を行う際に使用します。
- `frontend/openapigen/base.ts`: RequestArgsインターフェースが定義されています。APIリクエストの引数に関する設定を行う際に使用します。
- `frontend/openapigen/index.ts`: apiとconfigurationモジュールをエクスポートしています。API設定のエントリーポイントとして使用します。
- `frontend/openapigen/common.ts`: APIクライアントの生成に必要な共通のユーティリティ関数や型定義を含むファイルです。