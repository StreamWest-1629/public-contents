---
title: "#適度に駄弁る作業部屋 を作った話"
emoji: 🕯️
type: idea
topics: [golang, もくもく会, discord, コミュニティ, ポエム]
published: true
---

# もくもく会参加中の方々，いかがお過ごしでしょうか．
今回はボクの今年最後の制作物である，「適度に駄弁る作業部屋」を作った動機やその実情を話していきたいと思っています．

:::message
この記事は[「言語化コミュニティ Advent Calendar 2021」](https://adventar.org/calendars/6262) 2本目となる記事ですが，1本目である[「今年の総括(2020-2021)とコミュニティ」](./annual_summary-2020to2021)を事前にお読みいただくとより一層楽しめるかもしれません（保証は無理です）．
:::

作業部屋に関して，いつでも参加者を募集しています．この記事を読んで参加したいと思った方は下の方にリンクがあるので参加お待ちしております．

# 開発動機とか
まず初めに，適度に駄弁る作業部屋を作るきっかけについて話すのですが，そのためには大前提としてもくもく会というものを知っている必要があるので少しだけ．
もくもく会というのは，簡単にいうと複数人で集まって静かに作業をする会です．私は一人で作業できる性分ではないので，こういうイベント（基本的にオンライン開催）があると積極的に参加していたのですが，それぞれのもくもく会で別の問題が発生するようになりました．

- 本当に集まるだけ集まってずっとミュートのもくもく会では，そもそも集まっているという実感がわかなかった．
- ミュートを外した状態で話ながら作業するもくもく会（？）では，会話の方に集中が傾いてしまい，作業が進まなかった．

これらの問題を解決しようと思い，適度に駄弁る作業部屋を作ろうと思ったのです．

# 開発中の要件
当初の開発要件としては以下の通りでした．

1. BotによってDiscordサーバーを監視し，人が入ってきた時点で作業時間を始め，全員ミュートする（約50分）
1. 一定時間の作業を終えると，作業部屋とは別に休憩用のチャンネルをいくつか作成し，5人程度ずつ振り分ける（約15分）
1. 全員作業が終わって誰もいなくなるまで，1, 2を繰り返す

結果として，これらの目標は完遂され，今回はGo言語でDiscord Botを作りました．

# 実際に運用してみて分かったこと
最初の人の集まり方はすごく，知り合いやものを頻繁に作っている人を積極的に集めたので2週間足らずで100人以上の参加を実現しました．しかし，実際に運用してみるといくつか問題が浮上し始めたのです．

- サーバーに入ったけど一度ももくもくVCに入らない人が多い（作業部屋のボイスチャットに参加する際の敷居がまだ高い）
- 入る人も同時に複数人入るわけではないので孤独感が拭えていない

そこで，当初「既存のもくもく会の欠点を埋めることを目指したコミュニティ」という感じの銘を打っていたのですが，ここから方針を転換して **「寂しさを感じない個人作業」** という体験を提供することを目標にDiscord Botに応援機能をつけたのです．具体的には，一定間隔でDiscord Botの向こう側にいる琴葉茜が喋るという機能をつけることで作業中の孤独感の解消を図ったのでした．

:::message
ちなみに，応援機能の喋る声は追加することができるので「我こそは！」という方がいらっしゃれば人の声／機械音声問わず[ガイドライン](https://sugared-shoemaker-836.notion.site/Bot-2c80ccec8731439aae887b837daf5cca)を読んだ上で私のTwitterにDMしてください．
:::

結果から言うと，参加の敷居はまだまだ高いままで解消できていないのですが，それでも確実に作業中の寂しさがなくなると言うその一点においては解決できたと思っています．この点において，私はオニオンアーキテクチャに助けられたと言っていいでしょう．

# 裏目標の話とか
これは開発動機というよりも，開発中やコミュニティとして人を呼び込む際に思ったことではあるのですが，私はこの2年間の経験の中で世の中には **「様々なバックグラウンドや武器を持つ人がいる」** ということ， **「人間離れしているように見えても近づいて見ると実は思っているよりも人間である」** ということを何度も教えられました．

## 言語化しても伝わらない
けれど，それは私自身がいろいろなコミュニティを入ったことで得た経験であり，それを言語化できても伝達できた試しがないのです．それは言語として伝えるよりも先に，「自分と離れた価値観（立場）を持つ人」と対等に会話できるのだろうかという怯えであると私は結論付けています．実際，私自身もこのアドベントカレンダーの主催者であるイワケンさんをはじめとして様々な方々と会話するまではこんなことを言われたとしても信じていなかったと思います．
まとめると，人間離れした人や自分から離れた人に抱く畏怖の念と形容されるような感情に言語化するだけでは伝達手段としては不足なのです．
だから私はこの事を伝えるために作業時間と作業時間の間の休憩時間の僅かな時間ではあるのですが，この事実を経験として提供することを目標に今は運営しています．

しかしながら，現状ではまだボイスチャットの同時参加人数が少ないのでまだ達成できていないと言わざるを得ません．この壁を越えるためにもう少し考えを練りたいな，って思っています．

:::message alert
正直，アドバイスほしいです．コメント待ってます．
:::

# 終わりに
この作業部屋は様々な界隈の方を集め，様々な人の経験を共有する場として提供しているので作業内容に指定はありません．
皆様の面白い話を聞かせてください．皆様の参加お待ちしております．あとTwitterはフォローされると，私嬉しいです．

[![Discord](https://img.shields.io/discord/882251541757718609?color=%23f00&label=適度に駄弁るもくもく会&logo=Discord&logoColor=%23fF0&style=for-the-badge)](https://discord.gg/pcR7uwYxe9)
