---
title: "USB-PD テスターを買った話"
date: 2020-05-02T13:49:52+09:00
draft: false

markup: "mmark"
---

手持ちの機器に USB-PD を使う物がちょいちょい揃い始めたので、Aliexpress で 2 種類ほど買ってみた。

- [RD TC66](https://www.aliexpress.com/item/32968303350.html)
  - 製造: HANGZHOU RUIDENG TECHNOLOGY Co., LTD. のようだが公式サイトは見つからず。サポートは AliEx や各種チャット、メールのみなのでサイトは無いと思われる。
  - Windows のソフトでファームウェア更新可能（既に最新版だったので試せていない）
  - PC接続機能はデータ取り込みが可能で、スクリーンショットやエクセルデータとして保存可能。上位版の TC66C なら Bluetooth でスマホと接続可能らしいが、ソフトがすぐ陳腐化しそうだったので今回はパス（高くなるし）。
  - 値段: 通常時 2000〜2200 円程度。購入時はセールで 1850 円程度（さらにクーポン付けて実質 1700 円程度）。
- [FNIRSI FNC88](https://www.aliexpress.com/item/4000194892939.html)
  - 製造: [SHENZHEN FNIRSI TECHNOLOGY CO.,LTD](http://www.fnirsi.cn/)
  - Windows のソフトでファームウェア更新可能
  - PC接続機能はファームウェア更新のみで、データ取り込みなどはない
  - 価格: 通常時 1500〜1700 円程度。購入時はセールで 1450 円程度（さらにクーポン付けて実質 1300 円弱）。

どちらもケースに入っていて、TC66 はプラケース、FNC88 は金属ケースだった。結構ちゃんとしてて驚き。
{{< figure src="/img/post/2020/usb-pd-testers-tc66-and-fnc88.jpg" title="USB-PD テスター TC66 と FNC88" height="503">}}

やれることはだいたい同じようだが、なぜか iPhone 7 plus の充電を Type-C 端子経由でする場合の電流値が TC66 だけ妙に低く表示される現象が発生してしまって、謎。FNC88 では問題なく 1.5A が出るので、使い分けで対処することにして放置（むしろ PD 使える Type-C ポートで Apple 系の充電に対応するのって規格的にどうなのか、と疑問は残るが）。機能としては正直どちらも似たり寄ったりで、大きな違いはパソコンにデータを取り込めるかどうかというところ。

さて、TC66 はわりとすんなり使えたが、FNC88 でどつぼにはまってしまった。受け取り後に AliEx の商品ページに記載してあったファームウェア（mediafire に保存されている）を適用したところ、画面が左右反転して表示されるようになってしまった。
{{< figure src="/img/post/2020/usb-pd-tester-fnc88-screen-reverse.jpg" title="画面が左右反転した FNC88" height="501">}}
元々のバージョンが 1.03 だったところに 1.02 を適用してしまったのが問題かと思ったのだが、途中で見つけた公式サイトにあったより新しいバージョン (2.00, 2.01) を適用しても解決せず。Clear record や Factory Reset をしないと動作がおかしいみたいな記述も見かけたが、それでは特に解決せず。ダウングレードとかそこから再度アップグレードしたりしてもだめ。ただ、AliEx のレビューで全く同じ事象が解決している人がいたので、希望を捨てずに seller に問い合わせしてみた（製造会社のショップなので公式問い合わせしているのと変わらないが）。

結果から言うと、無事解決した。以下詳細。
{{< figure src="/img/post/2020/usb-pd-tester-fnc88-screen-correct.jpg" title="左右反転が直った正常な FNC88" height="498">}}

まず公式サイトにあるファームウェアは 2 種類存在して、"plastic" screen または "metal" screen かによって適用ファームが別になるとのこと。ちなみに mediafire にあったファームはそういう区分けがなかった。手元のテスターはどう見てもプラスチック（表面はアクリル?、その他は基板と同じ FR-4?）が使われていたし金属のパネルは分解してもなかった。
{{< figure src="/img/post/2020/usb-pd-tester-fnc88-side-view.jpg" title="FNC88 の側面" height="800">}}
なので、それ用を適用。しかし変わらず。問い合わせの結果、"metal" screen 用を適用してと言われたので、それに従いやった結果、解決。一応、見分け方がないのか聞いてみたものの、見た目の構造でわかるよ! と、くその役にも立たない返答があったのみ。それで分かれば今回の問い合わせはなかったし、seller も最初はプラスチックだねと言っていて、途中から技術者に聞いた結果解決したという有様。なんとも釈然としないが、ちゃんと動作するようになったのでまあ良しとした。安いしね。

ことの顛末を AliEx のレビューに書こうとしたら、公式サイトのリンクを含むとどうやってもエラーが出て投稿できないので適当にはしょって投稿するはめに。リンク全般がだめなのか、入れようとした公式サイトの URL が弾かれていたのか、その辺は不明。まあ "metal" 用のファームで反転問題解決するという情報は残せたので、今後同じトラブルに遭遇した人の助けにはなる、かもしれない。