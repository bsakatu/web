---
title: "Scancode Map による Scancode の変更"
date: 2018-11-12T22:57:00+09:00
draft: false

---

## Scancode Map とは?
基本的には Microsoft の<a title="Keyboard and mouse class drivers - Windows drivers | Microsoft Docs" href="https://docs.microsoft.com/en-us/windows-hardware/drivers/hid/keyboard-and-mouse-class-drivers#scan-code-mapper-for-keyboards">公式資料</a> を読めば分かりますが、元々はキーボードが送出するスキャンコードがおかしかった場合にそれを訂正する設定をするために作られたようです。現在では左 Ctrl キーと CapsLock キーの入れ替えや、Esc キーと 半角全角キーとの入れ替えに使うのがメインだと思いますが……。

ちなみにスキャンコードについては自分で資料を整理してまとめを作っていましたが作ろうとしていたもの以上にしっかりしたページを見つけてしまったので今回はそれを載せておきます。すごい資料です。

[Key Layout and Scancodes](http://hp.vector.co.jp/authors/VA003720/lpproj/others/kbdjpn.htm)

## 書き換え方法
マップのエントリは標準では存在しないので、下記のレジストリパスにバイナリ形式で作る必要があります。

Registry path:  `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Keyboard Layout`

（※ちなみに、 `HKEY_CURRENT_USER\Keyboard Layout` に作成してもいいと書かれた記事もいくつか見かけるが、Windows Vista か 7 の頃から動作しなくなっている。XP の頃は使えたらしいが未確認。なんでちょっと試せばいいだけのことを試さないで書いちゃうかな……。）

データは例えば US 配列のキーボードなどで CapsLock <-> Left Control としたい場合、 `00000000 00000000 03000000 1D003A00 3A001D00 00000000` のようなものになります。何がどういう順番に並んでいるかは以下の通り。

<blockquote cite="https://docs.microsoft.com/en-us/windows-hardware/drivers/hid/keyboard-and-mouse-class-drivers#scan-code-mapper-for-keyboards">

  <table>
    <thead>
      <tr>
        <th>Start offset (in bytes)</th>
        <th>Size (in bytes)</th>
        <th>Data</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>0</td>
        <td>4</td>
        <td>Header: Version Information</td>
      </tr>
      <tr>
        <td>4</td>
        <td>4</td>
        <td>Header: Flags</td>
      </tr>
      <tr>
        <td>8</td>
        <td>4</td>
        <td>Header: Number of Mappings</td>
      </tr>
      <tr>
        <td>12</td>
        <td>4</td>
        <td>Individual Mapping</td>
      </tr>
      <tr>
        <td>...</td>
        <td>...</td>
        <td>...</td>
      </tr>
      <tr>
        <td>Last 4 bytes</td>
        <td>4</td>
        <td>Null Terminator (0x00000000)</td>
      </tr>
    </tbody>
  </table>

  <footer><cite><a href="https://docs.microsoft.com/en-us/windows-hardware/drivers/hid/keyboard-and-mouse-class-drivers#scan-code-mapper-for-keyboards">Keyboard and mouse class drivers - Windows drivers | Microsoft Docs</a><cite></footer>

</blockquote>

<table>
  <thead>
    <tr>
      <th>Value</th>
      <th>Interpretation</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x00000000</td>
      <td>Header: Version. Set to all zeroes.</td>
    </tr>
    <tr>
      <td>0x00000000</td>
      <td>Header: Flags Set to all zeroes.</td>
    </tr>
    <tr>
      <td>0x00000003</td>
      <td>Three entries in the map (including null entry).</td>
    </tr>
    <tr>
      <td>0x1D003A00</td>
      <td>Left Control -> CapsLock (0x003A -> 0x001D)</td>
    </tr>
    <tr>
      <td>0x3A001D00</td>
      <td>CapsLock -> Left Control (0x001D -> 0x003A)</td>
    </tr>
    <tr>
      <td>0x00000000</td>
      <td>Null terminator.</td>
    </tr>
  </tbody>
</table>

レジストリファイルでは以下のように書けます。改行はしなくてもいいですが見やすいので入れています。

```
"Scancode Map"=hex:00,00,00,00,\
                   00,00,00,00,\
                   03,00,00,00,\
                   1d,00,3a,00,\
                   3a,00,1d,00,\
                   00,00,00,00
```

ここで注意しなければならないのが、Scancode Map のデータは[リトルエンディアン](https://ja.wikipedia.org/wiki/エンディアン "エンディアン - Wikipedia")で書かなければならないということです。順々に並ぶのではなく逆順で並んだデータなので一見すると理解しにくいですが、気をつけて下さい。またそれぞれの置き換え指定は「変換後のデータ、変換前のデータ」という順で並んでいるので、これを注意ポイントです。私は最初見事に混乱しました。

<blockquote cite="https://ja.wikipedia.org/wiki/エンディアン">

例えば、十六進法で表現すると <code>1234ABCD</code> という 1 ワードが 4 バイトのデータを、バイト毎に上位側から 「<code>12 34 AB CD</code>」 のように並べる順序はビッグエンディアン、下位側から 「<code>CD AB 34 12</code>」 のように並べる順序はリトルエンディアンである。

  <footer><cite><a href="https://ja.wikipedia.org/wiki/エンディアン">エンディアン - Wikipedia</a></cite></footer>

</blockquote>

ちなみにとあるキーを無効化したい場合は、置き換え後のキーを定義する部分を `0000` （レジストリファイル内なら `00,00`）と指定すれば可能です。なお、使用中の配列データ内に損際しないスキャンコードを割り当てることは出来ない。例えば、US 配列で日本語配列固有のキーを定義してもそもそも US 配列の定義にそれらのキーがそもそも存在しないので指定しても動作しない。