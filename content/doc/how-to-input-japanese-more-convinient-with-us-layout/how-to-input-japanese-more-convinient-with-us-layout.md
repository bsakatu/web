---
title: "Windows 日本語入力環境で US 配列をより使いやすくする"
date:  2018-12-11T00:25:00+09:00
draft: false 

markup: "mmark"
---

[自作キーボード #2 Advent Calendar 2018 11日目](https://adventar.org/calendars/2964#list-2018-12-11)の記事です。  
昨日は鴨南蛮さん ([&#64;ralfdegen](https://twitter.com/ralfdegen)) の「[今年1年の自作キーボード活動まとめ](https://kamobanban.hateblo.jp/entry/2018/12/10/231150)」でした。

ハードウェアやソフトウェア的につよつよな方がたくさんいらっしゃるので、しょぼしょぼしたネタでお茶を濁しておきます。残念ながら自作キーボードもキーキャップもキースイッチもケーブルも写真は一枚も出てきませんし [<abbr title="Quantum Mechanical Keyboard">QMK</abbr>](https://github.com/qmk/qmk_firmware) の話題もほぼありません!!! （わずかにあると言えばあるけど……）

---

## Windows 日本語環境で US 配列を使うには

日本語環境の Winodows の場合、一般的にはいわゆる [JIS 配列](https://ja.wikipedia.org/wiki/キー配列#109キーボード)のキーボード使用していると思います。しかし興味や様々な事情や思想や宗教上の理由などにより、[US 配列（ANSI 配列などとも）](https://ja.wikipedia.org/wiki/キー配列#101キーボード)を使ってみたい・使わなければならない人もいるはずです。また自作キーボード界隈であれば、キーボード、キーキャップ、ファームウェアなどが US 配列前提だったりするので、US 配列を避けて通るのはなかなか難しいでしょう。

さて例えば、JIS キーボードを使っている人が US 配列に乗り換える第 1 歩としては、ハード環境は JIS キーボードのまま US 配列が使えればお手軽でしょう。実現するにはいくつかの方法でがありますが、例えば簡単な方法としては以下に挙げるような方法をとることになるかもしれません。

- [ハードウェア キーボード レイアウトの変更で英語キーボードを選択する](https://bsakatu.net/post/2018/11/25/change-hardware-keyboard-layout-ime-jp/)
- 入力言語を英語 (QWERTY) にする
- キー置換ソフト (DvorakJ 等) などで配列を入れ替える
- QMK などを搭載したキーボードで配列を英語 (QWERTY) 風にする

ただこれら方法で配列を変更した場合、当然ながら前者の2パターンでは US 配列のキーボードとして動作することになるので、日本語環境固有のキー（無変換、変換、等々）が使えなくなってしまいます。それで困らない方もいるとは思いますが、困る人も当然いるでしょう。またソフトウェア上のキー置換で実現した場合、ソフトによるのかもしれませんが、私の使っている DvorakJ （親指シフトなどの配列変更などに用いるキー置換ソフト）では入力文字の置き換えは出来ても、ショートカットキーの置き換えが実現出来ず、不完全でした（問題ない人もいる? ようなので環境に寄るのかもしれませんが）。QMK で実現した場合は、前述したキー置換とほとんど同じく、記号類のショートカットキーが置き換えられていない、これまた不完全です。

上記の問題を回避しつつ US 配列を使う方法は、現実的かつ私が知っている方法としては次の2通りがあります（他にあったらどなたか教えて欲しい）。

- 微調整した AX 配列を使う
- 改造版 JIS 配列を使う

ここで、AX 配列を使うやり方は[別記事](/doc/ax-layout-on-windows/ "AX キーボードの配列を現行の Windows で使う")を書いているのでそちらを参照して下さい。今回の記事では JIS 配列を改造する手法について解説していきます。

## どこをいじるのか

レジストリを弄ります。見るべきは3つのエントリで、それらは標準では以下のようになっているはずです。

| Name                      | Type        | Value (JP default) |
| ----                      | ----        | ----               |
| `LayerDriverJPN`          | `REG_SZ`    | `kbd106.dll`       |
| `OverrideKeyboardSubtype` | `REG_DWORD` | `2`                |
| `OverrideKeyboardType`    | `REG_DWORD` | `7`                |
Table: 【標準状態】 `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\i8042prt\Parameters`

この中の `LayerDriverJPN` というエントリで指定されている `kbd106.dll` がキーボードから入力された内容を最終的に OS 内の仮想キーコードへと変換する変換表になっています。これを望む変換表が書かれたファイルに指定を変更してあげれば所望の動作をしてくれるようになります。たとえば純粋な US 配列であれば `kbdus.dll` というファイルがあるのでそれを指定します。これらの変換表を含む定義ファイルは `C:\Windows\System32` フォルダに格納されています。見てみると分かりますが、他にも色々な定義ファイル (`kbd***.dll`) があります。気になる方は覗いてみるとよいです。

## ところでスキャンコードとか仮想キーコードとは何か

※知っている人は飛ばしてもらって構わない内容です。本当はこれだけで記事書いてもよかったけど時間がなかった（力尽きた）。

今ではほとんど見ることもなくなった PS/2 キーボード、これのキーを押したときに PC に送信されるコードが[スキャンコード](https://ja.wikipedia.org/wiki/スキャンコード)です（リファレンスは「OADG テクニカルリファレンス」でググって下さいとだけ言っておきます）。これはキーボードの配列によってどの位置はどのコード、という風に定義されているので、JIS 配列のキーボードだろうが US 配列のキーボードだろうが、同じ位置のキーを押すと同じキーコードが送信されます。このスキャンコードを受け取って Windows 内部で用いる[仮想キーコード](https://docs.microsoft.com/en-us/windows/desktop/inputdev/virtual-key-codes)へと変換するための設定が、まさに前述した `kbd106.dll` などになります。

ちなみに現在一般的に使われている USB 接続のキーボードはもう一段階増えていて、[HID Usage ID](https://www.usb.org/document-library/hid-usage-tables-112) (※リンク先にある PDF の 53 ページ以降参照)という、これまた似たようなキー毎にコードを割り当てたものを PC に送信する。この後の処理は根拠になる資料が見つからないので多分に想像ですが、`C:\Windows\System32\hid.dll` 内部や [Microsoft の資料](https://download.microsoft.com/download/1/6/1/161ba512-40e2-4cc9-843a-923143f3456c/translate.pdf)を見ると、Usage ID から Scancode への変換表が存在するので、PS/2 キーボードに一手間加えて Usage ID → スキャンコード → 仮想キーコード、という流れで入力文字を処理しているのだと思われます。ちなみに macOS はそう言う資料を全然見つけられていないのでさっぱり分かりません。Usage ID から直接仮想キーのようなものを生成しているんじゃないかと想像はしていますが。

## 新たな `kbd106.dll` を作る

さて本筋に戻ります。今回弄りたい `kbd106.dll` ですが、Microsoft が[ソースを GitHub で公開している](https://github.com/Microsoft/Windows-driver-samples/tree/master/input/layout/fe_kbds/jpn/106)ので弄り放題です。以前は <abbr title="Windows Driver Kit">WDK</abbr> に同梱されていたのでダウンロードしてインストールして、と多少の手間がかかったのですが、今は Web で簡単に見られるようになりました。
（※ちなみに OS に含まれる `kbd106.dll` を直接バイナリ編集してしまうことも出来るのですが、それはライセンス的にグレーなのでここではまっとうな方法だけ書いておきます。一応バイナリエディタであれすればごにょごにょ。）

以下、Windows のメジャーバージョンは 10 であると仮定して書きます。7 や 8 系はそれぞれに適した WDK をインストールして、以降で説明するソース編集をして dll を生成すれば同じく実現可能です。7, 8 系は所持していないので未確認（持っていたけど 10 にアップグレードしてしまった）。

### 環境構築

簡単に見られるとは言っても dll ファイルをコンバイルする必要があるので、下記リンク先の Microsoft が公開している手順に従って環境をセットアップする必要があります。とは言ってもたいしてやることはなく、Visual Studio Community 2017 （<time datetime="2018-12-11">2018/12/11</time> 時点）をインストールし、その後上記の GitHub リポジトリを clone するだけです。GitHub へのアクセスはVisual Studio 用の GitHub 拡張機能をインストールするとよいでしょう。

[Windows Driver Kit (WDK) のダウンロードします。 | Microsoft Docs](https://docs.microsoft.com/ja-jp/windows-hardware/drivers/download-the-wdk)  
（※タイトルが変なのは Microsoft が悪い）

### 改造内容

ちなみに `kbd106.dll` の改造自体は[他に紹介している記事](https://ku6.jp/report/80.html "HHKBをAXキーボードレイアウトで使っているFF14プレイヤーの話 : 傾き指向プログラミング")があったりするのですが、その内容が入力される見かけの文字を変えることしか考えておらず、仮想キーコードはそのままという内容でした。これだとショートカットキーがいくつかおかしな事になります。

さて環境が作れたので編集に入っていきます。ちょっと煩雑なのでずらずら書いていきます。なおやり方は私の自己流なのでビルド出来れば方法は問いません（ほぼ備忘録）。

1. ソースのフォルダを開く。GitHub 拡張機能でダウンロードした場合、そのデータはユーザーフォルダに作成される `source` フォルダに保存されます。キー配列関係のファイルは `C:\Users\hoge\source\repos\Microsoft\Windows-driver-samples\input\layout` にまとまっています。
2. 上記フォルダ下の `.\fe_kbds\jpn\106` のフォルダを複製して `106_us` とでもリネームする。
3. その中の `kbd106.c` のファイル名を適当に `kbd106_us.c` とでもする。
4. `kbd106.rc` を開いて、dll ファイルの説明や元のファイル名として表示される情報を編集する（しなくてもいい）
5. `layout` フォルダの `kbd.sln` を開いて `106` フォルダと同階層に適当に `106_us` フォルダを作り、プロジェクトファイルを読み込ませる。
6. プロジェクトのプロパティを開いて、`構成のプロパティ → 全般 → ターゲット名`を `kbd106_us` に変更。
7. `kbd106_us.c` に以下の変更を適用する（`patch` 等で適当にやって下さい。解説は省きます）

<!-- markdownlint-disable MD009 -->
```diff
--- kbd106.c
+++ kbd106_us.c
@@ -28,14 +28,7 @@
     T10, T11, T12, T13, T14, T15, T16, T17,
     T18, T19, T1A, T1B, T1C, T1D, T1E, T1F,
     T20, T21, T22, T23, T24, T25, T26, T27,
-    T28,
-
-    /*
-     * Hankaku/Zenkaku/Kanji key must have KBDSPECIAL bit set (NLS key)
-     */
-    T29 | KBDSPECIAL,
-
-              T2A, T2B, T2C, T2D, T2E, T2F,
+    T28, T29, T2A, T2B, T2C, T2D, T2E, T2F,
     T30, T31, T32, T33, T34, T35,
 
     /*
@@ -48,14 +48,7 @@
      */
     T37 | KBDMULTIVK,
 
-    T38, T39,
-
-    /*
-     * Alphanumeric/CapsLock key must have KBDSPECIAL bit set (NLS key)
-     */
-    T3A | KBDSPECIAL,
-
-                   T3B, T3C, T3D, T3E, T3F,
+    T38, T39, T3A, T3B, T3C, T3D, T3E, T3F,
     T40, T41, T42, T43, T44,
 
     /*
@@ -87,7 +73,13 @@
     T53 | KBDNUMPAD | KBDSPECIAL,   // Numpad . (Del),
 
     T54, T55, T56, T57, T58, T59, T5A, T5B,
-    T5C, T5D, T5E, T5F, T60, T61, T62, T63,
+
+    /*
+     * Hankaku/Zenkaku/Kanji key must have KBDSPECIAL bit set (NLS key)
+     */
+    T5C | KBDSPECIAL,
+
+         T5D, T5E, T5F, T60, T61, T62, T63,
     T64, T65, T66, T67, T68, T69, T6A, T6B,
     T6C, T6D, T6E, T6F,
 
@@ -237,14 +230,14 @@
 static ALLOC_SECTION_LDATA VK_TO_WCHARS4 aVkToWch4[] = {
     //                               |          |   SHIFT  |  KANA  | K+SHFT |
     //                               |          |==========|========|========|
-    {'0'          ,          KANALOK ,'0'       ,WCH_NONE  ,WCH_WA  ,WCH_WO  },
+    {'0'          ,          KANALOK ,'0'       ,')'       ,WCH_WA  ,WCH_WO  },
     {'1'          ,          KANALOK ,'1'       ,'!'       ,WCH_NU  ,WCH_NU  },
     {'3'          ,          KANALOK ,'3'       ,'#'       ,WCH_A   ,WCH_AA  },
     {'4'          ,          KANALOK ,'4'       ,'$'       ,WCH_U   ,WCH_UU  },
     {'5'          ,          KANALOK ,'5'       ,'%'       ,WCH_E   ,WCH_EE  },
-    {'7'          ,          KANALOK ,'7'       ,0x27      ,WCH_YA  ,WCH_YAA },
-    {'8'          ,          KANALOK ,'8'       ,'('       ,WCH_YU  ,WCH_YUU },
-    {'9'          ,          KANALOK ,'9'       ,')'       ,WCH_YO  ,WCH_YOO },
+    {'7'          ,          KANALOK ,'7'       ,'&'       ,WCH_YA  ,WCH_YAA },
+    {'8'          ,          KANALOK ,'8'       ,'*'       ,WCH_YU  ,WCH_YUU },
+    {'9'          ,          KANALOK ,'9'       ,'('       ,WCH_YO  ,WCH_YOO },
     {'A'          , CAPLOK | KANALOK ,'a'       ,'A'       ,WCH_TI  ,WCH_TI  },
     {'B'          , CAPLOK | KANALOK ,'b'       ,'B'       ,WCH_KO  ,WCH_KO  },
     {'C'          , CAPLOK | KANALOK ,'c'       ,'C'       ,WCH_SO  ,WCH_SO  },
@@ -271,14 +264,14 @@
     {'X'          , CAPLOK | KANALOK ,'x'       ,'X'       ,WCH_SA  ,WCH_SA  },
     {'Y'          , CAPLOK | KANALOK ,'y'       ,'Y'       ,WCH_NN  ,WCH_NN  },
     {'Z'          , CAPLOK | KANALOK ,'z'       ,'Z'       ,WCH_TU  ,WCH_TUU },
-    {VK_OEM_1     ,          KANALOK ,':'       ,'*'       ,WCH_KE  ,WCH_KE  },
+    {VK_OEM_1     ,          KANALOK ,';'       ,':'       ,WCH_RE  ,WCH_RE  },
     {VK_OEM_2     ,          KANALOK ,'/'       ,'?'       ,WCH_ME  ,WCH_MD  },
-    {VK_OEM_3     ,          KANALOK ,'@'       ,'`'       ,WCH_VS  ,WCH_VS  },
-    {VK_OEM_7     ,          KANALOK ,'^'       ,'~'       ,WCH_HE  ,WCH_HE  },
+    {VK_OEM_3     ,          KANALOK ,'`'       ,'~'       ,WCH_RO  ,WCH_RO  },
+    {VK_OEM_7     ,          KANALOK ,0x27      ,'"'       ,WCH_KE  ,WCH_KE  },
     {VK_OEM_8     , 0                ,WCH_NONE  ,WCH_NONE  ,WCH_NONE,WCH_NONE},
     {VK_OEM_COMMA ,          KANALOK ,','       ,'<'       ,WCH_NE  ,WCH_IC  },
     {VK_OEM_PERIOD,          KANALOK ,'.'       ,'>'       ,WCH_RU  ,WCH_IP  },
-    {VK_OEM_PLUS  ,          KANALOK ,';'       ,'+'       ,WCH_RE  ,WCH_RE  },
+    {VK_OEM_PLUS  ,          KANALOK ,'='       ,'+'       ,WCH_HE  ,WCH_HE  },
     {VK_TAB       , 0                ,'\t'      ,'\t'      ,'\t'    ,'\t'    },
     {VK_ADD       , 0                ,'+'       ,'+'       ,'+'     ,'+'     },
     {VK_DECIMAL   , 0                ,'.'       ,'.'       ,'.'     ,'.'     },
@@ -294,10 +287,10 @@
     {VK_BACK      , 0       ,'\b'      ,'\b'      ,'\b'    ,'\b'    , 0x7f      , 0x7f      },
     {VK_CANCEL    , 0       ,0x03      ,0x03      ,0x03    ,0x03    , 0x03      , 0x03      },
     {VK_ESCAPE    , 0       ,0x1b      ,0x1b      ,0x1b    ,0x1b    , 0x1b      , 0x1b      },
-    {VK_OEM_4     , KANALOK ,'['       ,'{'       ,WCH_SVS ,WCH_OB  , 0x1b      , 0x1b      },
+    {VK_OEM_4     , KANALOK ,'['       ,'{'       ,WCH_VS  ,WCH_OB  , 0x1b      , 0x1b      },
     {VK_OEM_5     , KANALOK ,'\\'      ,'|'       ,WCH_PS  ,WCH_PS  , 0x1c      , 0x1c      },
     {VK_OEM_102   , KANALOK ,'\\'      ,'_'       ,WCH_RO  ,WCH_RO  , 0x1c      , 0x1c      },
-    {VK_OEM_6     , KANALOK ,']'       ,'}'       ,WCH_MU  ,WCH_CB  , 0x1d      , 0x1d      },
+    {VK_OEM_6     , KANALOK ,']'       ,'}'       ,WCH_SVS ,WCH_CB  , 0x1d      , 0x1d      },
     {VK_RETURN    , 0       ,'\r'      ,'\r'      ,'\r'    ,'\r'    , '\n'      , '\n'      },
     {VK_SPACE     , 0       ,' '       ,' '       ,' '     ,' '     , 0x20      , 0x20      },
     {0            , 0       ,0         ,0         ,0       ,0       , 0         , 0         }
@@ -306,9 +299,9 @@
 static ALLOC_SECTION_LDATA VK_TO_WCHARS8 aVkToWch8[] = {
     //                      |          |   SHIFT  |  KANA  | K+SHFT |  CONTROL  |  K+CTRL   | SHFT+CTRL |K+SHFT+CTRL|
     //                      |          |==========|========|========|===========|===========|===========|===========|
-    {'2'          , KANALOK ,'2'       ,'"'       ,WCH_HU  ,WCH_HU  , WCH_NONE  , WCH_NONE  , 0x00      , 0x00      },
-    {'6'          , KANALOK ,'6'       ,'&'       ,WCH_O   ,WCH_OO  , WCH_NONE  , WCH_NONE  , 0x1e      , 0x1e      },
-    {VK_OEM_MINUS , KANALOK ,'-'       ,'='       ,WCH_HO  ,WCH_HO  , WCH_NONE  , WCH_NONE  , 0x1f      , 0x1f      },
+    {'2'          , KANALOK ,'2'       ,'@'       ,WCH_HU  ,WCH_HU  , WCH_NONE  , WCH_NONE  , 0x00      , 0x00      },
+    {'6'          , KANALOK ,'6'       ,'^'       ,WCH_O   ,WCH_OO  , WCH_NONE  , WCH_NONE  , 0x1e      , 0x1e      },
+    {VK_OEM_MINUS , KANALOK ,'-'       ,'_'       ,WCH_HO  ,WCH_HO  , WCH_NONE  , WCH_NONE  , 0x1f      , 0x1f      },
     {0            , 0       ,0         ,0         ,0       ,0       , 0         , 0         , 0         , 0         }
 };
 
@@ -368,7 +360,6 @@
     0x0f,    L"Tab",
     0x1c,    L"Enter",
     0x1d,    L"Ctrl",
-    0x29,    (LPWSTR)SZ_KEY_NAME_HANKAKU_ZENKAKU,  // NLS Key
     0x2a,    L"Shift",
     0x36,    L"Right Shift",
     0x37,    L"Num *",
@@ -403,7 +394,8 @@
     0x54,    L"Sys Req",
     0x57,    L"F11",
     0x58,    L"F12",
-    0x70,    (LPWSTR)SZ_KEY_NAME_HIRAGANA,
+    0x5c,    (LPWSTR)SZ_KEY_NAME_HANKAKU_ZENKAKU,  // NLS Key
+    0x70,    (LPWSTR)SZ_KEY_NAME_HIRAGANA,
     0x79,    (LPWSTR)SZ_KEY_NAME_HENKAN,
     0x7b,    (LPWSTR)SZ_KEY_NAME_MUHENKAN,
     0x7C,    L"F13",
@@ -479,7 +471,7 @@
      * Type and subtype.
      */
     KEYBOARD_TYPE_JAPAN,    // Japanese Keyboard Layout
-    MAKEWORD(MICROSOFT_KBD_106_TYPE, NLSKBD_OEM_MICROSOFT),
+    MAKEWORD(MICROSOFT_KBD_AX_TYPE, NLSKBD_OEM_MICROSOFT),
 };
 
 PKBDTABLES KbdLayerDescriptor(VOID)
```
<!-- markdownlint-enable MD009 -->

これを適用すれば仮想キー毎の入力文字は US 配列と同等になりますが、これだけでは不十分で実際の配列も変更する必要があります。押したキーに対してどんな仮想キーを割り当てるかは別のファイルで定義されており、それは前述した GitHub のリポジトリにはない Windows <abbr title="Software Development Kit">SDK</abbr> の一部である `kbd.h` となります。保存場所は環境やバージョンに寄るのかもしれませんが、手元の環境ではこんな場所でした (Visual Studio のバージョンは 15.9.2, 同梱される Windows Driver Kit は 10.0.17740.1000)。

Path: `C:\Program Files (x86)\Windows Kits\10\Include\10.0.17763.0\um\kbd.h`

このファイルを `106_us` フォルダにコピーし、以下の変更を適用します。その後プロジェクトの `Header Files` に追加します。

```diff
--- kbd.h (original)
+++ kbd.h (modify)
@@ -1240,7 +1240,7 @@
 #define T0A _EQ(           '9'                                                              )
 #define T0B _EQ(           '0'                                                              )
 #define T0C _EQ(           OEM_MINUS                                                        )
-#define T0D _NE(OEM_7,     OEM_7,     OEM_PLUS,  OEM_PLUS,  OEM_PLUS,  OEM_PLUS,  OEM_PLUS  )
+#define T0D _NE(OEM_7,     OEM_PLUS,  OEM_PLUS,  OEM_PLUS,  OEM_PLUS,  OEM_PLUS,  OEM_PLUS  )
 #define T0E _EQ(           BACK                                                             )
 #define T0F _EQ(           TAB                                                              )
 #define T10 _EQ(           'Q'                                                              )
@@ -1253,8 +1253,8 @@
 #define T17 _EQ(           'I'                                                              )
 #define T18 _EQ(           'O'                                                              )
 #define T19 _EQ(           'P'                                                              )
-#define T1A _NE(OEM_4,     OEM_3,     OEM_4,     OEM_4,     OEM_4,     OEM_4,     OEM_4     )
-#define T1B _NE(OEM_6,     OEM_4,     OEM_6,     OEM_6,     OEM_6,     OEM_6,     OEM_6     )
+#define T1A _NE(OEM_4,     OEM_4,     OEM_4,     OEM_4,     OEM_4,     OEM_4,     OEM_4     )
+#define T1B _NE(OEM_6,     OEM_6,     OEM_6,     OEM_6,     OEM_6,     OEM_6,     OEM_6     )
 #define T1C _EQ(           RETURN                                                           )
 #define T1D _EQ(           LCONTROL                                                         )
 #define T1E _EQ(           'A'                                                              )
@@ -1266,11 +1266,11 @@
 #define T24 _EQ(           'J'                                                              )
 #define T25 _EQ(           'K'                                                              )
 #define T26 _EQ(           'L'                                                              )
-#define T27 _NE(OEM_PLUS,  OEM_PLUS,  OEM_1,     OEM_1,     OEM_1,     OEM_1,     OEM_1     )
-#define T28 _NE(OEM_1,     OEM_1,     OEM_7,     OEM_7,     OEM_7,     OEM_7,     OEM_7     )
-#define T29 _NE(OEM_3,     DBE_SBCSCHAR, OEM_3,  OEM_3,     OEM_3,     OEM_3,     OEM_3     )
+#define T27 _NE(OEM_PLUS,  OEM_1,     OEM_1,     OEM_1,     OEM_1,     OEM_1,     OEM_1     )
+#define T28 _NE(OEM_1,     OEM_7,     OEM_7,     OEM_7,     OEM_7,     OEM_7,     OEM_7     )
+#define T29 _NE(OEM_3,     OEM_3,     OEM_3,     OEM_3,     OEM_3,     OEM_3,     OEM_3     )
 #define T2A _EQ(           LSHIFT                                                           )
-#define T2B _NE(OEM_5,     OEM_6,     OEM_5,     OEM_5,     OEM_5,     OEM_5,     OEM_5     )
+#define T2B _NE(OEM_5,     OEM_5,     OEM_5,     OEM_5,     OEM_5,     OEM_5,     OEM_5     )
 #define T2C _EQ(           'Z'                                                              )
 #define T2D _EQ(           'X'                                                              )
 #define T2E _EQ(           'C'                                                              )
@@ -1285,7 +1285,7 @@
 #define T37 _EQ(           MULTIPLY                                                         )
 #define T38 _EQ(           LMENU                                                            )
 #define T39 _EQ(           ' '                                                              )
-#define T3A _NE(DBE_ALPHANUMERIC,DBE_ALPHANUMERIC,CAPITAL,CAPITAL,CAPITAL,CAPITAL,CAPITAL   )
+#define T3A _NE(DBE_ALPHANUMERIC,CAPITAL,CAPITAL,CAPITAL,CAPITAL,CAPITAL,CAPITAL   )
 #define T3B _EQ(           F1                                                               )
 #define T3C _EQ(           F2                                                               )
 #define T3D _EQ(           F3                                                               )
@@ -1313,13 +1313,13 @@
 #define T53 _EQ(           DELETE                                                           )
 #define T54 _EQ(           SNAPSHOT                                                         )
 #define T55 _EQ(           _none_                                                           )
-#define T56 _NE(_none_,    _none_,    OEM_102,   OEM_102,   OEM_102,   OEM_102,   OEM_102   )
+#define T56 _NE(_none_,    OEM_102,   OEM_102,   OEM_102,   OEM_102,   OEM_102,   OEM_102   )
 #define T57 _EQ(           F11                                                              )
 #define T58 _EQ(           F12                                                              )
 #define T59 _EQ(           CLEAR                                                            )
 #define T5A _NE(NONAME,    NONAME,    NONCONVERT,OEM_WSCTRL,OEM_WSCTRL,OEM_WSCTRL,OEM_WSCTRL)
 #define T5B _NE(NONAME,    NONAME,    CONVERT,   OEM_FINISH,OEM_FINISH,OEM_FINISH,OEM_FINISH)
-#define T5C _NE(NONAME,    NONAME,    OEM_AX,    OEM_JUMP,  OEM_JUMP,  OEM_JUMP,  OEM_JUMP  )
+#define T5C _NE(NONAME,    DBE_SBCSCHAR, OEM_AX,    OEM_JUMP,  OEM_JUMP,  OEM_JUMP,  OEM_JUMP  )
 #define T5D _EQ(           EREOF                                                            )
 #define T5E _NE(_none_,    _none_,    _none_,    OEM_BACKTAB,OEM_BACKTAB,OEM_BACKTAB,OEM_BACKTAB)
 #define T5F _NE(NONAME,    NONAME,    NONAME,    OEM_AUTO,  OEM_AUTO,  OEM_AUTO,  OEM_AUTO  )
```

変更点をまとめると下記の通り。

- 下記以外: JIS 配列のスキャンコードと仮想キーコードの対応をUS 配列の定義に合わせて変更
- `scancode = 0x3A`: JIS 配列の <kbd>英数 CapsLock</kbd> から US 配列の <kbd>CapsLock</kbd> へ変更
- `scancode = 0x5C`: `VK_NONAME` で無指定になっているところに `VK_DBE_DBCSCHAR` (<kbd>半角全角</kbd>) を割り当て（※本来の位置は `VK_OEM_3` に明け渡しているため）

<kbd>CapsLock</kbd> については、日本語版の <kbd>英数 CapsLock</kbd> の挙動が好きではない・弄りにくくて不便なので US 配列仕様のシンプル動作版に変更（こうすると AutoHotkey で <kbd>Ctrl</kbd> ↔ <kbd>CapsLock</kbd> というやりたいのにやれない設定を適用出来るようになる）。<kbd>半角全角</kbd>については、`scancode = 0x5C` がスキャンコードとしては US 配列にも JIS 配列にも定義がないですが、Windows の設定としては USB HID の "Keyboard International6 (`0x8C`)" が変換元として定義されており、またどうやら PC-98 のテンキーカンマ用だったものの当然現在では限りなく使用されないはずのものなので、好きに使います。

なおよく見ると分かりますが、`VK_OEM_5` (<kbd>\\|</kbd>) が2つ存在しています。JIS 配列における <kbd>¥|</kbd> と <kbd>]}</kbd> の位置です。ここは好みや使用環境に寄るところもあるので、各自で好きに弄ってもらった方がいいと思いそのままにしてあります。あんまり弄りすぎると QMK や DvorakJ などで配列を作るときに苦労すると思うので、そういうツールを使う方々は程々に……。

### ビルド

さて、これで改造版 `kbd106.dll` を作成する準備は整いました。

1. ビルドの構成を `Debug` から `Release` に変更し、プラットフォームは適用したい環境を選択し、ビルドする。
2. ソースフォルダにフォルダが作られビルドしたファイル等々が生成されているはず。

## 設定適用

ついに dll ファイルが完成したので設定適用していきます。

`106_us` フォルダに生成されるビルド後のフォルダやファイルの中から `kbd106_us.dll` をとりだし、これを `C:\Windows\System32` などのシステムフォルダにコピーし、レジストリを下記の通り編集し、再起動すれば設定は適用されているはずです

| Name                      | Type        | Value           |
| ----                      | ----        | ----            |
| `LayerDriverJPN`          | `REG_SZ`    | `kbd106_us.dll` |
| `OverrideKeyboardSubtype` | `REG_DWORD` | `1`             |
| `OverrideKeyboardType`    | `REG_DWORD` | `7`             |
Table: 【設定適用後】 `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\i8042prt\Parameters`

ちなみに `OverrideKeyboardIdentifier` は変更しません。特に設定に関与していないですし、`PCAT_106KEY` である意味あっているので。なおここで `OverrideKeyboardSubtype = 1` に変更していますが、これはこの改造配列以外の配列を同時に使えるようにするための配慮です。`kbd106_us.c` で `MICROSOFT_KBD_106_TYPE` を `MICROSOFT_KBD_AX_TYPE` に変更しているのもこれのためです。どういうことかと言うと、この配列を本来の `Subtype = 1` である AX 配列に上書きする形でシステムに設定しています。この設定は[キーボードの配列を複数共存させる場合](/doc/use-different-keyboard-layout-simultaneously/ "Windows で同時に複数のキーボード配列を使用する | capyBaral")に役に立つのですが、それはまた別の話なのでここではそうなんだ、とだけ思っておいて下さい。1 つの配列しか使わないのであれば特に関係ありません。

さて、再起動が完了すればあとは動作確認です。メモ帳などで一通り打ってみて問題ないか確認し、できれば [Keymill](http://kts.sakaiweb.com/keymill.html "KT Software - Keymill") などのスキャンコードと仮想キーコードを確認出来るソフトでテストするとよいでしょう。スキャンコードと仮想キーコードの対応を変えたキーの動作確認には特に。

そういえば 32 bit 版のことをすっかり忘れていましたが、必要な場合は 32 bit 用にビルドして `C:\Windows\SysWOW64` に放り込めばいいと思います。これがないと動かないケースがあるのかいまいちちゃんと調べていませんが、今後ますます 32 bit のソフトウェアを使うことは減っていくのであんまり必要ないかも……。

というわけで、これで動作すれば今回の改造は完了です。この改造方法はわりとどうにでもなるので、さらなるオレオレ配列を作るなりして遊んでみてもいいでしょう。よく分からない場合はメールなり Twitter なりで質問して下さい。コメント欄はまだ作っていないので……。

## 欄外

この記事をアドカレのネタとして採用するのかは自分の中でもわりと迷いがありました。しかし、自作キーボードで一般的に流通するキーキャップの文字（レジェンド）が基本的に US 配列を前提とし、その他の配列はオプションかそもそも無い、という状況であるのは覆すことの出来ない事実なので、もっと日本人が気軽に US 配列を使えれば日本での自作キーボード界の発展にほんのわずかばかりでも貢献出来るんじゃないか、と自分に言い聞かせて書いてみました。まあ半分は自分の備忘録なんですけど。

---
明日の[自作キーボード #2 Advent Calendar 2018 12日目の記事](https://adventar.org/calendars/2964#list-2018-12-12)は [hinastory さん](https://github.com/hinastory)による「気が付いたら本棚がキーボード棚に変わっていた話」の予定です。

この記事は Fortitude60 (Switch: Aliaz, Gateron silent red [lubed]; Keycap: MDA Big Bang) で書きました。
