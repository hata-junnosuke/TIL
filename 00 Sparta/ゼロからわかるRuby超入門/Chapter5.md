# 便利な道具を使う
## メソッドの機能を調べる
### リファレンスマニュアル
> https://docs.ruby-lang.org/ja/
- uniqを調べると [![Image from Gyazo](https://i.gyazo.com/22f7a29aa0cad17f12a5b8ed9bcbc861.png)](https://gyazo.com/22f7a29aa0cad17f12a5b8ed9bcbc861)
- uniq!などのように！がつくメソッドがある。
- uniqとuniq!の違いは「！」は自分自身の要素が変わることにある。 [![Image from Gyazo](https://i.gyazo.com/934dfdee5cad6dc569941d10ec657dc6.png)](https://gyazo.com/934dfdee5cad6dc569941d10ec657dc6)
  - uniqではarr2のみを変更して、新しい配列を作っている。（arr1とarr2は別物）
  - uniq!ではarr2だけでなくarr1も変更されている。つまり、破壊的変更が行われている。（arr1とarr2は同じもの）
- { |n| N.to_s }をブロックという。メソッドの後ろにブロックを書いていることを「ブロックを渡す」と表現する。

## 演習問題
[![Image from Gyazo](https://i.gyazo.com/8b09fd0da1832499e61b0c8f5deda2ce.png)](https://gyazo.com/8b09fd0da1832499e61b0c8f5deda2ce)
