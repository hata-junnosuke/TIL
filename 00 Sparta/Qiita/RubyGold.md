# Ruby Gold 76点が送る合格のための最低限の学習基準(2023年6月受験)

# 記事の目的
Ruby Association Certified Ruby Programmer Gold version 3.1（以後RubyGoldで表記）に76点でギリギリ合格できました。（合格点75点）
そこで私の学習に使用した教材やその達成度を記すことで今後の受験者のために合格の最低ラインを提示して参考にしていただければと思い記事にしました。

## はじめに結論
ここまでやりました。結果ギリギリ合格ができました。
- REx：運がよければ100点取れる
- Ruby公式資格教科書：RubyGoldで出題される範囲は理解をする
- メタプログラミングRuby 第2版：1~5章を一読し、以後は辞書的に活用
- CTC：試験直前に使用。初回で9割の正答率で解説までしっかり理解する
- ネット記事：補足として使用（参考にした記事は全て理解した）

以下解説していきます。

## 【1】使用した教材と達成度
### REx（最終達成度：100点取れるようになる）
Ruby技術者認定試験の模擬テストができるサイトです。
難易度は高めです。

まずはこのサイトを活用して、RubyGoldの試験レベルを悟りました。
最初はコテンパンにされ、38点でした。
<img  src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2517030/cd01d263-e557-ab9e-d69d-a97f5db929e6.png">

その後、RExを反復でやりました。繰り返しやっていると問題を覚えてしまいますが、解きながらプログラムの流れを口ずさむなどして理解を深めながらやっていくと点数が伸びていきました。
最終的には必ず９割は取れるようになり、運が良ければ100点取れるまでになりました。
<img  src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2517030/a6cb630d-22b2-10da-6056-2a98e874b8e0.png">


また間違えた問題や理解が足りない問題はGitHubにまとめてました。
おかげで試験の直前で見直しができました。

■RExリンク

https://rex.libertyfish.co.jp/

---
### [改訂2版］Ruby技術者認定試験合格教本（Silver/Gold対応）Ruby公式資格教科書（最終達成度：RubyGoldで出題される範囲は理解をする）
Ruby技術者認定試験を受けるなら必読の教科書。
私はversion2.1対応版をおさがりでいただき、やってました。
ですが、現在の試験では3.1で実施されているため、2.1から3.1までの過程で追加された機能についても出題されて撃沈したので、キャッチアップの必要があるので注意です。

またirbを活用して実際に動かしてみると理解が深まるのでやってました。（これは結構大事です。）

RubyGoldから試験範囲となる添付ライブラリについてはボリューミーですが、サクッと流し読みでRExなどの試験を通じて定着させていきました。

巻末の模擬試験は試験の1週間前に実施しました。初めて解いた時は6割くらいでした。
巻末の模擬試験はREx同様反復して解きました。
ただRExと違って問題が変わらないので、解説まで理解するところまでやりました。

■[改訂2版］Ruby技術者認定試験合格教本（Silver/Gold対応）Ruby公式資格教科書]

https://www.amazon.co.jp/dp/B0756VF9Y3

### メタプログラミングRuby 第2版（最終達成度：1~5章を一読、以後は辞書的に活用）
オライリーのRubyを深掘りした本。
RubyGold合格に向けて1~5章を読んでください。
難しいなどレビューがありますが、小説のような形式で書かれているので意外と読みやすいです。
テスト範囲がドンピシャの1冊となっているので、一読するべきかと思います。

先述の教材で理解ができなかったり、深掘りしたい場合に辞書的に使っていました。

■メタプログラミングRuby 第2版

https://www.amazon.co.jp/dp/4873117437

### Ruby技術者認定試験【Gold】模擬問題 - Rubyの研修・教育ならCTC教育サービス（最終達成度：解説理解まで）
CTCテクノロジーのオリジナル問題集。
難易度はRExより簡単。
試験1週間前に確認テストして活用して60問中で9割ほどの正答率でした。
その後は解説を読んで理解まで徹底してやりました
ここでの問題が実際に試験で出題されたので解くことをオススメします。

■Ruby技術者認定試験【Gold】模擬問題 - Rubyの研修・教育ならCTC教育サービス

https://www.school.ctc-g.co.jp/ruby/training_ruby_gold_01_10.html

### Qiitaなどネット記事
テストに向けて先人の方々が残してくれた記事を参考にして補っていました。
私が参考にしていた記事を載せておきます。
- 全般
[1ヶ月後「Ruby Association Certified Ruby Programmer Gold version 2.1」に合格する方法](https://qiita.com/sogu/items/5ff2bf8702e14daf1804)
[Ruby Goldに合格したので、勉強方法をまとめてみた(2020年11月版)](https://qiita.com/jonakp/items/c7b7ecfe3eb9ee8c950e)
- Rubyコマンドラインオプション
[Ruby勉強 インターネットの企画職な人がプログラミングを勉強するブログ](https://peakp.hatenablog.com/entry/2015/05/06/213912)
- 特殊変数
[脳汁portal Rubyの特殊変数($@とか$`とか$数字とか・・・)](https://portaltan.hatenablog.com/entry/2015/10/26/181540)



## 【2】反省点
### 2.1から3.1の間に追加された機能をキャッチアップすべきでした。
私は2.1対応版の合格教本で勉強していましたが3.1になるまでに追加された機能が出題されたため、本番の試験で対応できませんでした。
例えば、2.7で導入された番号指定パラメータなどはキャッチアップしとくべきでした。
- 番号指定パラメータ
[【初心者向け】Ruby _1 って何？（番号指定パラメータ）](https://supersoftware.jp/tech/20230413/18684/)

### テストまでの日程が長すぎた。
「受験料が安くないこと」と「難易度が高い」と思い、勉強期間を1ヶ月半くらいを見積もっていましたが、長すぎました。
RubyGoldの試験は結構、深掘りした内容で「実務でいつ使うのかな？」みたいなことを勉強することになります。
最終的に試験日を前倒しにして受験しましたが、モチベーションのピークは過ぎ去っていました。
なので、できるだけ短いスパンでサクッと勉強してダレる前に受験すべきでした。

## 【3】さいごに
以上で最低限のレベルの学習内容の報告を終わります。
やれば合格できるので、頑張ってください。
その際にこの記事が参考になれば幸いです。

---
### 参考資料
#### 参考書のAmazon
- [[改訂2版］Ruby技術者認定試験合格教本（Silver/Gold対応）Ruby公式資格教科書]
](https://www.amazon.co.jp/dp/B0756VF9Y3)
- [メタプログラミングRuby 第2版](https://www.amazon.co.jp/dp/4873117437)
#### Web上でのテストツール
- [REx](https://rex.libertyfish.co.jp/)
- [Ruby技術者認定試験【Gold】模擬問題 - Rubyの研修・教育ならCTC教育サービス](https://www.school.ctc-g.co.jp/ruby/training_ruby_gold_01_10.html)
#### ネット記事
- [1ヶ月後「Ruby Association Certified Ruby Programmer Gold version 2.1」に合格する方法](https://qiita.com/sogu/items/5ff2bf8702e14daf1804)
- [Ruby Goldに合格したので、勉強方法をまとめてみた(2020年11月版)](https://qiita.com/jonakp/items/c7b7ecfe3eb9ee8c950e)
- [Ruby勉強 インターネットの企画職な人がプログラミングを勉強するブログ](https://peakp.hatenablog.com/entry/2015/05/06/213912)
- [脳汁portal Rubyの特殊変数($@とか$`とか$数字とか・・・)](https://portaltan.hatenablog.com/entry/2015/10/26/181540)
- [【初心者向け】Ruby _1 って何？（番号指定パラメータ）](https://supersoftware.jp/tech/20230413/18684/)
