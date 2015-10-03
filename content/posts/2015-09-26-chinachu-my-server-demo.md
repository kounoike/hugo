---
date: 2015-09-26T21:45:35+09:00
draft: true
slug: ""
socialsharing: true
tags:
- "chinachu"
- ""
title: Chinachu fork開発版デモサイト
---
[Chinachuのfork開発版](https://github.com/kounoike/Chinachu/tree/my-server) のデモサイトを用意しました。
ChinachuのSlackか、twitterのどちらかでDMくださればURLと認証情報をお伝えします。
ただし、使用しているJavaScriptの関数の都合で、Windows 10上の11を除いたすべてのIEは今のところ非サポートです。Win10のIEも
使ってないので良く分かりません。なので、Firefox/Chromeをお使いください。（Operaは知らない・・・）
あくまでもfork版ですので、オリジナルの作者に問い合わせたりすることの無いようよろしくお願いします。
<!--more-->

# どんな違いがあるの？

2015/9/26時点で以下の点を改造しています。

* [番組検索をルール判定と同じロジックにする](https://github.com/kanreisa/Chinachu/pull/220)
* [ルール・番組検索等で全角・半角を区別しないようにする](https://github.com/kanreisa/Chinachu/pull/218)
* [config.jsonとrules.jsonのチェックを行う chinachu check コマンドの追加](https://github.com/kanreisa/Chinachu/pull/225)
* [あちこちにフックコマンドを追加](https://github.com/kanreisa/Chinachu/pull/214)

まあ、WUIに関係あるのは前の2つだけです。

## 番組検索をルール判定と同じロジックにする

その名の通り、オリジナルの Chinachu はルールでの判定とWUIでの番組検索が入力フォームからして違っていたために、


[#161](https://github.com/kanreisa/Chinachu/issues/161)や[#157](https://github.com/kanreisa/Chinachu/issues/157)などのような
ことが課題になっていました。

そこで、番組検索時にもルール判定と同じロジックで検索するように変更したのがこのプルリクです。

ちなみに、今日になってから「元の検索ロジック残しておいて、新しいボタンで検索とかした方が良いかも」と思い直したので、
大きく手を入れる可能性があります。

## ルール・番組検索等で全角・半角を区別しないようにする

これは、夏期アニメで言えば「Charlotte」とか「WORKING」が局によって半角だったり全角だったりするのですが、どちらで入力しても
検索／ルール判定でマッチするようにする変更です。

全角カナと半角カナとか、丸数字と全角・半角数字とかを同一視してくれます。詳しくは http://nomenclator.la.coocan.jp/unicode/normalization.htm に
あります。うちの設定ではこの中の「NFKC」という設定になっています（なので、丸数字が半角数字と一致します）。

更に言えば、検索対象の番組名などの文字列と、検索ルールの文字列の両方をNFKCの形に変換してから一致検索をします。

この機能はconfig.jsonの設定によって、NFD/NFC/NFKD/NFKCを選ぶこともできますし、オリジナルの Chinachu と変わらない動作にすることもできます。

# 他人に公開して大丈夫なの？

まあ大丈夫でしょう。

`~/chinachu` が録画などに使っている Chinachu が入っているディレクトリで、この中にあるchinachu-operatorとchinachu-wuiが自分用に
動くようになっています。

もう一つ別のディレクトリの`~/chinachu-test/`ディレクトリに Chinachu 環境を構築して、data ディレクトリは自分用のとは別になっています。
そして、こっちはwuiだけを動かして、operatorを動かしていません。なのでWUIで予約とかの操作はできるものの、その情報を見て録画するプロセスが
いないため、何も起こらないわけです。

これだけだと、番組情報が取得できていなくて番組表を表示したり、ルールから予約を作ったりすることが出来ません。そこで活躍するのが、
[あちこちにフックコマンドを追加](https://github.com/kanreisa/Chinachu/pull/214)です。

「あちこち」の中の、「EPG情報取得が終わってschedule.jsonを更新した後」にシェルスクリプトを実行します（schedule.jsonは番組表情報が入っているファイルです）。
そのシェルスクリプトの中で、`~/chinachu/data/schedule.json`を`~/chinachu-test/data/schedule.json`にコピーします。
これにより、チェックから漏れている方法で`~/chinachu-test/data/schedule.json`が更新されてしまったとしても、自分用のデータは
書き換わっていないですし、次のEPG取得で上書きされるので問題ありません。

書いた当時は「使い道思いつかないけど入れとくかー」みたいに思って書いた機能が日の目をみました・・・と思っていました。
・・・これ、getEpgが非同期っぽい。意味ないじゃん。

