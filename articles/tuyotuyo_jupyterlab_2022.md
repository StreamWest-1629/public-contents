---
title: ぼくのかんがえたさいきょうのJupyterLabかんきょうin2022
emoji: 🔰
type: tech
topics: [Docker, jupyter, jupyterlab]
published: false
---

# Anaconda環境をお使いの皆様，いかがお過ごしでしょうか
今回はPython，特に機械学習用のJupyter Lab開発環境をテーマに話していきたいと思います．
皆様は環境を構築する際，何に気をつけて構築していますか？もちろん，その環境でやりたい事を達成するための機能はもちろんでしょうが，それを必要最小限にするのか，それとも開発の効率を上げるために様々なツールを導入するのか，あるいは複数のデバイスで同様の環境を構築できるような仕組みを整備するのか，それともデバイスに特化した環境を構築するのか，様々な観点があると思います．
今回はその記事にある通り，短い期間ではあるもののJupyterを触り始めてから整えた機械学習環境について紹介していきたいと思います．よろしくお願いします．

:::message
この記事はあくまでも私個人の感想であり，所属している組織などは一切関係ないのでご理解ください．
:::

## Anacondaに対する個人的な感想とDocker環境スコれの会
この記事では，Docker環境を用いて環境構築をしていきたいと思います．
機械学習を行う方が普段使っていることが多いのはAnaconda環境かと思いますが，私は好きではありません．理由は以下の通りです．
1. GUIでがっちり整えているので却って管理しにくい
2. 本当にデータ分析にだけ特化しているので応用が効かない
3. Pythonの仮想環境は本当の意味での仮想環境じゃない

一つ一つ見ていきましょう．

一個目の理由に関しては，最初のうちはそれでも良いかもしれませんが，私の環境には既にDockerが居座っています．機械学習用の仮想環境を立てるためだけにAnacondaを採用すべきだとはとても思えませんでした．正直，他の理由に関しては実際に使ってみてから後から気づいたことであって一番大きい部分はここだと思います．実際，バージョン管理などはGUIでやるよりもDockerfileで記述してしまった方が見通しが良いです（個人の感想です）．

二個目の理由に関しては，ご存じの通り，Anaconda環境がPythonやそれを囲うように付属する部分でできていることに由来します．それに対して，Docker環境は単なるLinux環境でしかないので，追加しようと思えばどんなものであっても追加することができます．

そして最後，三個目の理由に関しては，実際に運用してみると一番大きいメリットであるように感じます．Pythonの仮想環境はローカル環境を汚さないためだけの仮想環境であり，（Anacondaではできるものの）環境変数を設定してみたり，OS全体のファイル構成などを統一してみたりすることなどはできません．しかし，Dockerにはそれができるのです．このことにより，さらに効率的なデータセットなどの運用を行うことが期待できます．

## Docker環境を利用する前に
この記事では先にデメリットを挙げておく必要があるように思えるので，Docker環境を構築する前にそのデメリットを確認しておきます．
結論から言うと，一般的にDocker環境はRAMメモリ，ディスク，バッテリー（M1Macで動かすものではありません）といったリソースの消費が激しいです．そのこともあり，Dockerfileはより小さいコンテナがベストプラクティスとされています．
しかし，今回使用するコンテナはベースとなる[nvidia/cuda](https://registry.hub.docker.com/r/nvidia/cuda)が既に重く，Dockerfile内でも，ライブラリのバージョン変更への対応や整備性の良さなどから，コマンドを分割しています（このことはコンテナイメージを保管する際のファイルサイズに影響します）．

:::message alert
詳細は後述しますが，これらの理由から，実際に運用するにはハードウェア制約が厳しく，NVIDIA GPUに加え，RAMメモリやディスク容量を確認した上で次に進んでください．
:::

# 紹介するリポジトリや必要なものなど
今回は実際に運用している環境を紹介する記事なので，ちゃんと[リポジトリ][container_repository]があります．お納めください．

リポジトリに関して，常に更新し続けるリポジトリになりそうなので，この記事では一応Releaseで指定していますが，まあ良さげに改造するなり好きに使ってください．

今回作成するDockerコンテナはGPU割り当てを行うので，ローカル環境側にハードウェア的な制約がつきます．少なくとも以下のような制約があるのでご注意ください．

- CUDA 11.3.1が利用可能なNVIDIA GPUとそのドライバ
- 仮想環境上でGPU(CUDA)が利用可能なOS/DockerEngine
    - Windows環境はWindows11又はWindows10 21H2
    - Docker Engine 19.03〜

一応動作チェックに関しては以下の環境で確認しています．
- Windows11 on AMD Ryzen7 2700X, RAM 48GB, and GTX 1050Ti
- Windows10 (21H2) on Intel i5-10600K, RAM 32GB, and GTX 750Ti

直接は関係ないのですが，Git（GitHub）を用いたファイル管理を前提としているため，GitHubアカウントを作っておくことをお勧めします（必須ではありません）．

## 登場ディレクトリの確認
このリポジトリでは，最低でも4個の重要なディレクトリが出てくるので，それを確認します．
### `jupyterlab-container`
これはローカル（Windowsなどのホスト）側で`git clone`されたリポジトリの **ローカル側の** ルートディレクトリです．
### `/workspace(WORKSPACE_DIR)`
これはコンテナ（仮想環境）側で認識している`jupyterlab-container`ディレクトリです． **これが絶対パスです**．ローカル側でどこに`git clone`配置していようとも，コンテナでは厳密に`/workspace`というパスに配置されます．
### `datasets` (`$DATASET_LOCALDIR`)
これはデータセットを管理しているローカル側のディレクトリです．あとで解説しますが，データセットを`.gitignore`に入れて除外するよりも，データセットを一元的に管理した方が良いのであえて定義しておきます．
### `/resource/datasets` (`$DATASET_DIR`)
これはコンテナ側で認識している`datasets`ディレクトリです．`/workspace`同様，絶対パスで指定されており，ローカル側のどこに`datasets`ディレクトリがあろうとも関係なく，`/resource/datasets`に入れられます．

### まとめると
これらのディレクトリはそれぞれに同期することができます．
つまりこう言うことです．
| ローカル側ディレクトリ | | コンテナ側ディレクトリ |
| :-: | :-: | :-: |
| （任意） jupyterlab-container | ←同期→ | /workspace （固定） |
| （任意） datasets | ←同期→ | /resource/datasets （固定） |

設定方法などはともかくとして，この関係を理解していないと次の`.env`ファイルの記述ができないので理解してください（懇願）．

# 実際に実行してみる
ここまででも十分に長かったと思いますが（もう既に書いていて疲れた），実はまだ何もしていないです．実際に実行してみましょう．

## ファイル構成の確認
今回使用する[リポジトリ][container_repository]をフォークしてから，`git clone`で自分のローカル環境にダウンロードしてください．おそらくこの段階で以下のようなファイル構成になっているのではないかと思います．
```
./ (jupyterlab_container)   (dir)
┣ .jupyter                  (dir)
┣ container                 (dir)
┃ ┣ requirements            (dir)
┃ ┃ ┗ （省略）
┃ ┗ Dockerfile
┣ projects                  (dir)
┃ ┗ （省略）
┣ test                      (dir)
┃ ┗ （省略）
┣ docker-compose.yml
┗ readme.md
```

## 環境変数の設定①
次に`.env`ファイルを`jupyterlab_container`ディレクトリ内に作成します．これはdockerコンテナをビルド・実行する際に与えられる環境変数のリストで`<name>=<value>` と言う形で指定します．これは`.gitignore`に登録されているのでGitHub上に上がることはありません．以下の環境変数を指定してください．
### `REQUIREMENTS=pytorch.txt`
Pythonにおける，所謂requirements.txtをこの環境変数で指定します．`container/requirements`以下にあるファイルである必要があり，`jupyter.txt`はここで記述していなくても`pip install`されます．初期状態にはおそらく`pytorch.txt`があるのでそれをご利用ください．
### `DATASET_LOCALDIR=D:\datasets`
データセットをまとめている **「ローカルの（Windowsなどのホスト側）」** ディレクトリを指定してください．コンテナ内部から見ると`/resource/datasets`というディレクトリとしてディレクトリ内のファイルにアクセスすることができます．
### `GIT_NAME=Your Name`
Gitで使用する名前を入れてください．（`git config --global user.name $GIT_NAME`が実行されます．）
### `GIT_MAIL=your_email@address.com`
Gitで使用するメールアドレスを入れてください．（`git config --global user.email $GIT_MAIL`が実行されます．）
### `GITHUB_CREDENTIAL=https://your_username:your_token@github.com`
**これは任意です．必須ではないです** GitHubにpushするためにGitHubへの自動ログインを行うための環境変数です．GitHubはこの手の権限管理はすべてトークンで行うことになっているので[Personal access tokens][personal_access_tokens]からトークンを設定して上記の形で設定してください． **別にこれを指定していなくてもコンテナ実行毎にログインすればいいのでセキュリティを気にする方は入れるべきではないです**

:::message alert
`GITHUB_CREDENTIAL`はコンテナのビルド中に設定され，コンテナ内にログイン情報が記録されます．コンテナを`pull`しないようにお願いします．
:::

## 環境変数の設定②
次に`.env`ファイルを次は`container`ディレクトリ内に作成します．これはdockerコンテナを実行する際に与えられる環境変数のリストで，先ほど同様`<name>=<value>`という形で指定し，`.gitignore`に登録されているのでGitHub上に上がることはありません．ここには仮想環境内で設定しておきたい環境変数を入れてください．

```
./ (jupyterlab_container)   (dir)
┣ .jupyter                  (dir)
┣ container                 (dir)
┃ ┣ requirements            (dir)
┃ ┃ ┗ （省略）
┃ ┣ .env
┃ ┗ Dockerfile
┣ projects                  (dir)
┃ ┗ （省略）
┣ test                      (dir)
┃ ┗ （省略）
┣ .env
┣ docker-compose.yml
┗ readme.md
```

[container_repository]:https://github.com/StreamWest-1629/jupyterlab-container
[personal_access_tokens]:https://github.com/settings/tokens