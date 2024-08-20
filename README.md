# environment


リモートリポジトリをローカルにクローン
```
$ git clone https://github.com/Conovel/environment.git
```

リポジトリに移動
```
$ cd environment
```

.gitmodulesでfrontendとbackendのリポジトリを読み込んでいます。

サブモジュールの初期化と更新
```
$ git submodule update --init --recursive
```