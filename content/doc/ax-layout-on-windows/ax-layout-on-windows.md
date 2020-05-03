---
title: "AX キーボードの配列を現行の Windows で使う"
date: 2018-12-11T00:23:00+09:00
draft: false

markup: "mmark"
---

## そもそも AX キーボードとは

ネット上にあんまり資料がないのですが、こんなものです（ちなみに私自身は実物を触ったことも見たこともありません……）。

<blockquote cite="http://d.hatena.ne.jp/keyword/AX%A5%AD%A1%BC%A5%DC%A1%BC%A5%C9">
  <p>IBM PC/AT互換機用MS-DOSを日本語化するための1方式、AX規格で採用されていた日本語キーボード。</p>
  <p>現在日本語キーボードの主流となっている106/109配列と比較してより英語キーボードに近いレイアウトになっており、英数キーの配置が通称ASCII配列(106系はJIS配列準拠)である点や、日本語関連キーが少なくシンプルな操作が可能であり、スペースキー周りのレイアウトに余裕がある点、特殊キーの[AX]キーが存在する、などの特徴がある。</p>

  <footer><cite><a href="http://d.hatena.ne.jp/keyword/AX%A5%AD%A1%BC%A5%DC%A1%BC%A5%C9">AXキーボードとは - はてなキーワード</a></cite></footer>
</blockquote>

<figure>
  <a title="By Yes0song [CC BY-SA 3.0 ( https://creativecommons.org/licenses/by-sa/3.0 ) or GFDL ( http://www.gnu.org/copyleft/fdl.html )], from Wikimedia Commons" href="https://commons.wikimedia.org/wiki/File:KB_Japanese_AX_keyboard.svg"><img width="512" alt="KB Japanese AX keyboard" src="https://upload.wikimedia.org/wikipedia/commons/thumb/d/d7/KB_Japanese_AX_keyboard.svg/800px-KB_Japanese_AX_keyboard.svg.png"></a>
  <figcaption>AX キーボード配列</figcaption>
</figure>

配列を見れば分かりますが、通常の JIS 配列（106キー）との違いは以下の通り。ざっくり言ってしまうと記号類が概ね US 配列と同じになっている、US 配列と JIS 配列の中間のような存在。

- 大半の記号配列が US 配列と同じ
- 「¥|」「\」は JIS 配列と同様の配置（※ただし `Shift + \` は `|` であるので実質同じキーが2個あることになる）
- 「`~」は「'"」の右に配置
- 「Ctrl」と「Caps Lock」が逆
- 「F1」の左が「AX」（「半角全角/漢字」ではない）
- 「Esc」が「1」の左に配置
- 「変換」の右が「漢字」（「Alt」ではない）
- 「右 Shift」の下が「英数カナ」
- 「Win」キーや「App」キーは存在しない（※しかし Windows の定義ファイルにはしれっと入っているので JIS キーボードと同じ設定のまま使える）

この配列がスタンダードになっていれば現代の色々な苦労はもっと減ったはず……、と思ったけど一部 US 配列と同一配置でスキャンコードを異なるものにしていたりもするから、これはこれで面倒を引き起こしそう。

## どうやってこの配列を使うか

下記レジストリを表の通りに書き換えれば AX 配列となる（[参考サイト](http://www.atmarkit.co.jp/ait/articles/0001/26/news001.html "Windowsで右Altキーに［漢字］キーを割り当てる方法（AXキーボード設定を利用する方法）：Tech TIPS - ＠IT")）。なお下表で指定している `kbdax2.dll` は特に弄っていなければ標準で Windows のシステムフォルダに入っている。レガシーなファイルがいまだに入っているこういうところは Windows さすがだなと思います。

Registry path: `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\i8042prt\Parameters`

| name                         | type        | data         |
| ----                         | ----        | ----         |
| `LayerDriverJPN`             | `REG_SZ`    | `kbdax2.dll` |
| `OverrideKeyboardIdentifier` | `REG_SZ`    | `AX_105KEY`  |
| `OverrideKeyboardSubtype`    | `REG_DWORD` | `1`          |
| `OverrideKeyboardType`       | `REG_DWORD` | `7`          |

ただしこのままだと JIS 配列には存在するけど AX 配列には存在しないキーが正常に使えないので、スキャンコードの入れ替えや変更をする必要がある。詳細はスキャンコードマップの書き換えで[別記事](Scancode Map による Scancode の変更)を書いてあるのでそちらを参照のこと。

## スキャンコードマップの書き換え

よく、左 Ctrl キーと CapsLock キーの入れ替えや、Esc キーと 半角全角キーとの入れ替えに使うレジストリの書き換えです。[詳細は別途書いてある](/doc/scancode-map/)のでそちらを参照して下さい。

【極力 JIS 配列と揃えるマップ】

```registry
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Keyboard Layout]
"Scancode Map"=hex:00,00,00,00,\
                   00,00,00,00,\
                   0a,00,00,00,\
                   1d,00,1d,e0,\
                   38,00,38,e0,\
                   1D,E0,70,00,\
                   38,e0,29,00,\
                   5b,00,79,00,\
                   5a,00,7b,00,\
                   29,00,2b,00,\
                   2b,00,7d,00,\
                   2b,00,73,00,\
                   00,00,00,00
```

| Scancode (JIS -> AX) | Key Name (JIS -> AX)          | Note |
| ---         | ---                                    | ---  |
| E0-1D -> 1D | 英数カナ (AX) -> Left Control            | ※本来は Right Control にしたいが AX 配列には存在しないので Left で代替 |
| E0-38 -> 38 | 漢字 -> Left Alt                        | ※本来は Right Alt にしたいが AX 配列には存在しないので Left で代替 |
| 70 -> E0-1D | カタカナひらがな (JIS) ->英数カナ (AX)       | ※ほぼ同じキーだが内部的には挙動が異なるキー |
| 29 -> E0-38 | ESC -> 漢字 (AX)                        | ※半角全角キーに相当するキーはないが同様の動作が実現出来る漢字キーで代替 |
| 79 -> 5B    | 変換 (JIS) -> 変換 (AX)                  | |
| 7B -> 5A    | 無変換 (JIS) -> 無変換 (AX)               | |
| 2B -> 29    | [{ -> `~                               | |
| 7D -> 2B    | ¥\| (JIS) -> ¥\| (AX)                  | |
| 73 -> 2B    | \\_ -> ¥\| (AX)                        | ※\\_キーはAX配列には存在しないため、近いこのキーで代替 |

以下、問題点の話など結構長いのでとにかく設定が欲しい人は[ページ最後](#設定の適用)まで飛ばした方が賢明です。

## 問題点

さてこの方法、大抵は問題ないですが問題が発生するケースもあります。

1. 同じキーのようで挙動が違うキーを使うケース
2. 同じキーとして機能しているけど配列の違いによって生じるスキャンコードの違いが原因でスキャンコードからキー判定しているソフトが正常に動作しないケース
3. 日本語は JIS 配列と決め打ちで作られたソフトを使う際に本来の動作をしてくれないケース

1個目のケースは特に[「英数カナ」キーの挙動について後述](#「英数カナ」キーの挙動)しています。

2個目のケースについては、ありがちなのが <kbd>無変換</kbd> と <kbd>変換</kbd> キーです。この2つは Windows がキーを処理した結果である仮想キーとしては同一ですが、キーボードから来る信号としてのスキャンコードが異なります。DvorakJ などはキー判定にこのスキャンコードを使用しているので、この2つのキーを用いて親指シフトなどをしようとすると正常に動作しません。その場合は内部のスクリプトの書き換えなどが必要になり、なかなか手間がかかります。

3個目のケースについては、気づいたのはスクリーンキーボードが正常に動作しないというケースでした。文字系のキーは当然動作しますが、スキャンコードマップで書き換えている日本語固有キーなどは押しても反応がなく、動作を確認してみるとどうやら今までやったレジストリの設定にかかわらず JIS 配列として動作しているようでした。そのため、色々キーが変わっている AX 配列にしていると正しくキーが入力されない、というわけです。

というわけで、AX 配列を上手く使おうとする場合は、上記の問題点を解決するか関わらないようにして使っていく必要があります。まあがんばってください。ちなみにわたしはまさに DvorakJ を使っているのでスクリプト書き換えが色々大変だと分かったので別の手段に乗り換えました。そのネタはまた別途書きます。

## 「英数カナ」キーの挙動

前述したスキャンコードマップでは JIS 配列の「カタカナひらがな」キーと近いキーとして AX 配列の「英数カナ」キーを割り当てています。しかし軽く書いてはいますが、実は微妙に挙動が異なります。その挙動の違いというのが、このキーを使う人には結構悩ましいものになっています（あまりいないとは思いますが……）。以下にそれぞれの定義部分を載せます。

【AX 配列「英数カナ」キー挙動定義の一部 ([kbdax2.c](https://www.google.com/search?q=%22kbdax2.c%22 '"kbdax2.c" - Google 検索'))】

```c
        VK_DBE_KATAKANA,      // Base Vk
        KBDNLS_TYPE_TOGGLE,   // NLSFEProcType
        KBDNLS_INDEX_NORMAL,  // NLSFEProcCurrent
        0x8, /* 00001000 */   // NLSFEProcSwitch
        {                     // NLSFEProc
            {KBDNLS_KATAKANA,0},               // Base
            {KBDNLS_HIRAGANA,0},               // Shift
            {KBDNLS_ROMAN,0},                  // Control
            {KBDNLS_SEND_PARAM_VK,VK_KANA},    // Shift+Control
            {KBDNLS_ALPHANUM,0},               // Alt
            {KBDNLS_ALPHANUM,0},               // Shift+Alt
            {KBDNLS_ALPHANUM,0},               // Control+Alt
            {KBDNLS_ALPHANUM,0}                // Shift+Control+Alt
        },
```

【JIS 配列「カタカナひらがな」キー挙動定義の一部 ([kbd106.c](https://github.com/Microsoft/Windows-driver-samples/blob/65196cb16bfcf7b3ebc6cc52032fd7a0bb7d6691/input/layout/fe_kbds/jpn/106/kbd106.c#L523-L536))】

```c
        VK_DBE_HIRAGANA,     // Base Vk
        KBDNLS_TYPE_TOGGLE,  // NLSFEProcType
        KBDNLS_INDEX_NORMAL, // NLSFEProcCurrent
        0x08, /* 00001000 */ // NLSFEProcSwitch
        {                    // NLSFEProc
            {KBDNLS_HIRAGANA,0},             // Base
            {KBDNLS_KATAKANA,0},             // Shift
            {KBDNLS_HIRAGANA,0},             // Control
            {KBDNLS_SEND_PARAM_VK,VK_KANA},  // Shift+Control
            {KBDNLS_ROMAN,0},                // Alt
            {KBDNLS_ROMAN,0},                // Shift+Alt
            {KBDNLS_ROMAN,0},                // Control+Alt
            {KBDNLS_NOEVENT,0}               // Shift+Control+Alt
        },
```

載せたコードの中に書かれている定義の説明は以下。

| 定義 | 仮想キー名 | キー名 | 補足 |
| --- | --- | --- | --- |
| `KBDNLS_KATAKANA`              | `VK_DBE_KATAKANA`     | カタカナ | |
| `KBDNLS_HIRAGANA`              | `VK_DBE_HIRAGANA`     | ひらがな | |
| `KBDNLS_ROMAN`                 | `VK_DBE_ROMAN`        | ローマ字 | |
| `KBDNLS_SEND_PARAM_VK,VK_KANA` | `VK_KANA`             | カナ    | ローマ字入力とかな入力の入れ替えなどに使用 |
| `KBDNLS_ALPHANUM,0`            | `VK_DBE_ALPHANUMERIC` | 英数    | JIS 配列の「英数 CapsLock」を単押し相当 |

ここには載せていませんが、日本語キーボード固有のいくつかのキーは modifier との組合せで発生する仮想キーが異なります。今回のケースでは JIS 配列は「ひらがな」が、AX 配列は「カタカナ」がベースとなったキーになっていることが分かります。内部的にはどちらも「ひらがな」「カタカナ」等々が網羅されているので問題無さそうに見えますが、調べていたら一つ問題となる事例に当たりました。それは、MS-IME を使用しているケースです。

{{< figure title="MS-IME のキー設定画面" src="/img/post/2018/key-config-window_MS-IME.png" width="532" height="567" >}}

画像を見ると分かるように、<kbd>Shift</kbd> + <kbd>カタカナ</kbd>はありますが、<kbd>Shift</kbd> + <kbd>ひらがな</kbd> がありません。しかし AX 配列では「ひらがな」は <kbd>カタカナ</kbd> を <kbd>Shift</kbd> 付きで入力して初めて認識されるので、実質的には <kbd>ひらがな</kbd> は <kbd>Shift</kbd> +  <kbd>ひらがな</kbd> の組み合せでしか存在出来ないということになります。それなのに MS-IME ではそのキー設定がそもそも存在しないため、このキーに対して機能を割り当てることができない、というのが問題となるケースの1つですです。仮にこのキーの動作を JIS 配列のそれに完全に合わせようとした場合、 AutoHotkey などのキー置換ソフトを用いる必要があります（別の話になるのでこの記事ではそれについては特に詳しくは触れません）。

ちなみに私は普段 Mac も Windows も（さらに iOS も）ATOK を使っていて、そこで使われるはずのこのキー（カナロックとして機能）は完全に無効化しているので、MS-IME での挙動はわざわざ調べていて初めてちゃんと知りました……。1つのキーにいくつも機能を仕込むという意味では、自作キーボードのレイヤーなどと近いものを感じます。

---

## 設定の適用

今まで説明した設定適用のためにレジストリファイルを置いておきます。併せて元に戻すファイルも。

- [AX_layout.reg](https://gist.github.com/bsakatu/fccc375610b903a3a4c8d8bb3a6f832f/raw/08becdfd9194ccc7feff95a5c50a36c9ec0732e1/AX_layout.reg):  AX 配列化設定
- [JIS_layout.reg](https://gist.github.com/bsakatu/fccc375610b903a3a4c8d8bb3a6f832f/raw/08becdfd9194ccc7feff95a5c50a36c9ec0732e1/JIS_layout.reg): JIS 配列に戻す設定
