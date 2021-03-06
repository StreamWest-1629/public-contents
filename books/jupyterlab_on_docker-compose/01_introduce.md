---
title: はじめに
---
# Anaconda環境をお使いの皆様，いかがお過ごしでしょうか

さて，はじめて書く本だということもあって，文章のはじめはどのようにすればいいのだろうかと考えてしまいますが，結局いつも通りになってしまいました．
今回は，Dockerを上手く使いこなすためのひとつの手段として，JupyterLab環境の構築をテーマに「開発環境用のコンテナを最大限極めてみよう」というアプローチをしていきます．

Dockerに対して割とよく耳にする意見の中には，コンテナはサーバーと強く関連する概念であり，サーバーを持っていない・触らない自分にはいらない・学習コストが高い，という事を聞きます．

普段サーバーを触らない方でも，ローカル環境にコンテナを構築してそれを実際に開発環境として運用することでDockerを学習するというメリットがあります．

皆様は，ローカルにDockerを入れているでしょうか？
ローカルに入っているあなたのDockerは埃を被せることなく使っていますか？

もし埃を被せているようであれば，またそうでなくてもDockerコンテナの「開発環境用のコンテナ」という使い道を提案できれば，この記事は8割以上目標を達成したと言って問題はないでしょう．

:::message alert
この本はあくまでも私個人の感想であり，所属している組織などは一切関係ないことをご理解ください．
:::

:::message
なお，今回，最終的に構築するのはGPUが利用可能なDockerコンテナなのでホストマシンにはLinux，Windows10 21H2，Windows11のいずれかをご用意ください．
:::
