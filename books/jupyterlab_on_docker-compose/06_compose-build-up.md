---
title: JupyterLabを動かそう終編 - Docker Composeで実行する・考察
---

# Docker Composeを回してみよう

前々回，前回でそれぞれdockerfile，docker-compose.ymlを記述してきました．今回はそれをDocker Composeを使って実際に動かしていきたいと思います．

# 動かす
それでは早速動かしていきたいと思います． **とても簡単です**．基本的に私はコマンドで実行したがるので，ターミナル（コマンドプロンプトでもなんでも）をdocker-compose.ymlのあるディレクトリで開いておいてください．

## コンテナをビルド
```sh
$ docker compose build
```
これを実行するだけです．するとコンテナのビルドが始まります．なかなか時間がかかるのでゆっくりと気長に待ちましょう．コンテナのビルドが終わるとキャッシュが残るので，二回目以降のビルドはすぐに終わります．

:::message
`--no-cache`をつけることでキャッシュを無視してビルドすることができます．
:::

## コンテナを実行
```sh
$ docker compose up
```
これで実行されます．そのあとにjupyterのログが表示されるので，`http://127.0.0.1:8888/lab?token=...`という感じのURLを踏むとJupyterlabに入ることができます．

# コンテナに入ってみてからわかること
## コンテナは記憶を保存しない
Jupyterlabを開いて.ipynbファイルを作ってもJupyterlabを閉じてからもう一度コンテナを起動するときには消失しています．このままではいくらJupyterLabには入って編集できたとしても保存ができないことになります．

## 何から何までLinux
Windowsの環境変数はなく，ファイルシステムや環境変数がすべてLinuxです．例えば`PATH`を確認してみると以下のように表示されます．
```sh
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

# 次回予告
~~次回は「コンテナは記憶を保存しない」という問題を解決するために**ボリューム**を使ってローカルにデータが残るような設定をしていきたいと思います．~~
と思ったのですが，その前に環境変数の話をしようと思います（その方がストーリーとして都合がいいだけ）．