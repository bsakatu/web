---
title: "USB HID Usage ID の Scancode 変換と対応するキー"
date: 2020-07-14T22:02:25+09:00
categories: [Input]
tags: [keyboard, key-layout, Windows, macOS, reference]
toc: true
draft: false
---

## 概要 {#general}

USB キーボードの内部で発生するコードである Usage ID、それに対応する Scancode とキーをまとめる。

## そもそも USB HID Usage ID とは

<abbr title="Universal Serial Bus Human Interface Device">USB HID</abbr> は各キーに対して Scancode と似たような固有の ID というかコードを定義しており、それを Usage ID と呼ぶ。
Usage ID は HID の Usage Page というグループ毎に定義されているので、同じ Usage ID は各 Usage Page 毎に存在する。
一次資料は USB の規格策定をしている <abbr title="USB Implementers Forum">USB-IF</abbr> が公開しているので興味あれば本記事末尾のリンクから参照のこと。

本記事ではキーボードのグループである Keyboard/Keypad Page (0x07) と、一部のキーが属する Generic Desktop Page (0x01) のみを取り扱う。
なお Usage ID から Scancode への変換に関する情報を macOS についても調べたが、そういう情報にたどり着けなかったため Windows に寄った内容でまとめてある。

## USB HID Usage ID と Scancode (Set 1) の変換対応表

USB-IF の Usgae ID の定義、Microsoft が公開する Usage ID から Scancode への変換対応表、そして Windows の内部で実際にその変換をしていると思われる `hid.dll` の解析結果をまとめた。
Table 1 はキーボードのキーである "Keyboard/Keypad Page (0x07)" について、Table 2 では Microsoft が公開する変換対応表に記載されている電源関係に関するキーのみを抽出した "Generic Desktop Page (0x01)" の一部について、となっている。
なお `hid.dll` で下記内容が定義されている範囲については適当にバイナリエディタで探せば見つかる、とだけ書いておく。手元の Windows 10 (1909) と以前使っていた Windows 7 SP1 では内容に変わりはなかった。

{{< table >}}
Caption: Usage IDs of Keyboard/Keypad Page (0x07)
| Usage ID   || Usage Name                        | Scancode (Set 1)         || diff    | Key                                                                                                                                          ||||||
| dec  | hex  | ^^^^^^^^^^                        | MS Ref.      | `hid.dll`  | ^^^^    | ANSI                                        || ISO (typical)                               || JIS                                                ||
| ^^^  | ^^^  | ^^^^^^^^^^                        | ^^^^^^^      | ^^^^^^^^^  | ^^^^    | base                   | shift               | base                   | shift               | base                          | shift               |
|------|------|-----------------------------------|--------------|------------|---------|------------------------|---------------------|------------------------|---------------------|-------------------------------|---------------------|
| 0    | `00` | Reserved (no event indicated)     | `None`       | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 1    | `01` | Keyboard ErrorRollOver            | `FF`         | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 2    | `02` | Keyboard POSTFail                 | `FC`         | `FF`       | &check; | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 3    | `03` | Keyboard ErrorUndefined           | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 4    | `04` | Keyboard a and A                  | `1E`         | `1E`       |         | <kbd>a</kbd>           | <kbd>A</kbd>        | <kbd>a</kbd>           | <kbd>A</kbd>        | <kbd>a</kbd>                  | <kbd>A</kbd>        |
| 5    | `05` | Keyboard b and B                  | `30`         | `30`       |         | <kbd>b</kbd>           | <kbd>B</kbd>        | <kbd>b</kbd>           | <kbd>B</kbd>        | <kbd>b</kbd>                  | <kbd>B</kbd>        |
| 6    | `06` | Keyboard c and C                  | `2E`         | `2E`       |         | <kbd>c</kbd>           | <kbd>C</kbd>        | <kbd>c</kbd>           | <kbd>C</kbd>        | <kbd>c</kbd>                  | <kbd>C</kbd>        |
| 7    | `07` | Keyboard d and D                  | `20`         | `20`       |         | <kbd>d</kbd>           | <kbd>D</kbd>        | <kbd>d</kbd>           | <kbd>D</kbd>        | <kbd>d</kbd>                  | <kbd>D</kbd>        |
| 8    | `08` | Keyboard e and E                  | `12`         | `12`       |         | <kbd>e</kbd>           | <kbd>E</kbd>        | <kbd>e</kbd>           | <kbd>E</kbd>        | <kbd>e</kbd>                  | <kbd>E</kbd>        |
| 9    | `09` | Keyboard f and F                  | `21`         | `21`       |         | <kbd>f</kbd>           | <kbd>F</kbd>        | <kbd>f</kbd>           | <kbd>F</kbd>        | <kbd>f</kbd>                  | <kbd>F</kbd>        |
| 10   | `0A` | Keyboard g and G                  | `22`         | `22`       |         | <kbd>g</kbd>           | <kbd>G</kbd>        | <kbd>g</kbd>           | <kbd>G</kbd>        | <kbd>g</kbd>                  | <kbd>G</kbd>        |
| 11   | `0B` | Keyboard h and H                  | `23`         | `23`       |         | <kbd>h</kbd>           | <kbd>H</kbd>        | <kbd>h</kbd>           | <kbd>H</kbd>        | <kbd>h</kbd>                  | <kbd>H</kbd>        |
| 12   | `0C` | Keyboard i and I                  | `17`         | `17`       |         | <kbd>i</kbd>           | <kbd>I</kbd>        | <kbd>i</kbd>           | <kbd>I</kbd>        | <kbd>i</kbd>                  | <kbd>I</kbd>        |
| 13   | `0D` | Keyboard j and J                  | `24`         | `24`       |         | <kbd>j</kbd>           | <kbd>J</kbd>        | <kbd>j</kbd>           | <kbd>J</kbd>        | <kbd>j</kbd>                  | <kbd>J</kbd>        |
| 14   | `0E` | Keyboard k and K                  | `25`         | `25`       |         | <kbd>k</kbd>           | <kbd>K</kbd>        | <kbd>k</kbd>           | <kbd>K</kbd>        | <kbd>k</kbd>                  | <kbd>K</kbd>        |
| 15   | `0F` | Keyboard l and L                  | `26`         | `26`       |         | <kbd>l</kbd>           | <kbd>L</kbd>        | <kbd>l</kbd>           | <kbd>L</kbd>        | <kbd>l</kbd>                  | <kbd>L</kbd>        |
| 16   | `10` | Keyboard m and M                  | `32`         | `32`       |         | <kbd>m</kbd>           | <kbd>M</kbd>        | <kbd>m</kbd>           | <kbd>M</kbd>        | <kbd>m</kbd>                  | <kbd>M</kbd>        |
| 17   | `11` | Keyboard n and N                  | `31`         | `31`       |         | <kbd>n</kbd>           | <kbd>N</kbd>        | <kbd>n</kbd>           | <kbd>N</kbd>        | <kbd>n</kbd>                  | <kbd>N</kbd>        |
| 18   | `12` | Keyboard o and O                  | `18`         | `18`       |         | <kbd>o</kbd>           | <kbd>O</kbd>        | <kbd>o</kbd>           | <kbd>O</kbd>        | <kbd>o</kbd>                  | <kbd>O</kbd>        |
| 19   | `13` | Keyboard p and P                  | `19`         | `19`       |         | <kbd>p</kbd>           | <kbd>P</kbd>        | <kbd>p</kbd>           | <kbd>P</kbd>        | <kbd>p</kbd>                  | <kbd>P</kbd>        |
| 20   | `14` | Keyboard q and Q                  | `10`         | `10`       |         | <kbd>q</kbd>           | <kbd>Q</kbd>        | <kbd>q</kbd>           | <kbd>Q</kbd>        | <kbd>q</kbd>                  | <kbd>Q</kbd>        |
| 21   | `15` | Keyboard r and R                  | `13`         | `13`       |         | <kbd>r</kbd>           | <kbd>R</kbd>        | <kbd>r</kbd>           | <kbd>R</kbd>        | <kbd>r</kbd>                  | <kbd>R</kbd>        |
| 22   | `16` | Keyboard s and S                  | `1F`         | `1F`       |         | <kbd>s</kbd>           | <kbd>S</kbd>        | <kbd>s</kbd>           | <kbd>S</kbd>        | <kbd>s</kbd>                  | <kbd>S</kbd>        |
| 23   | `17` | Keyboard t and T                  | `14`         | `14`       |         | <kbd>t</kbd>           | <kbd>T</kbd>        | <kbd>t</kbd>           | <kbd>T</kbd>        | <kbd>t</kbd>                  | <kbd>T</kbd>        |
| 24   | `18` | Keyboard u and U                  | `16`         | `16`       |         | <kbd>u</kbd>           | <kbd>U</kbd>        | <kbd>u</kbd>           | <kbd>U</kbd>        | <kbd>u</kbd>                  | <kbd>U</kbd>        |
| 25   | `19` | Keyboard v and V                  | `2F`         | `2F`       |         | <kbd>v</kbd>           | <kbd>V</kbd>        | <kbd>v</kbd>           | <kbd>V</kbd>        | <kbd>v</kbd>                  | <kbd>V</kbd>        |
| 26   | `1A` | Keyboard w and W                  | `11`         | `11`       |         | <kbd>w</kbd>           | <kbd>W</kbd>        | <kbd>w</kbd>           | <kbd>W</kbd>        | <kbd>w</kbd>                  | <kbd>W</kbd>        |
| 27   | `1B` | Keyboard x and X                  | `2D`         | `2D`       |         | <kbd>x</kbd>           | <kbd>X</kbd>        | <kbd>x</kbd>           | <kbd>X</kbd>        | <kbd>x</kbd>                  | <kbd>X</kbd>        |
| 28   | `1C` | Keyboard y and Y                  | `15`         | `15`       |         | <kbd>y</kbd>           | <kbd>Y</kbd>        | <kbd>y</kbd>           | <kbd>Y</kbd>        | <kbd>y</kbd>                  | <kbd>Y</kbd>        |
| 29   | `1D` | Keyboard z and Z                  | `2C`         | `2C`       |         | <kbd>z</kbd>           | <kbd>Z</kbd>        | <kbd>z</kbd>           | <kbd>Z</kbd>        | <kbd>z</kbd>                  | <kbd>Z</kbd>        |
| 30   | `1E` | Keyboard 1 and !                  | `02`         | `02`       |         | <kbd>1</kbd>           | <kbd>!</kbd>        | <kbd>1</kbd>           | <kbd>!</kbd>        | <kbd>1</kbd>                  | <kbd>!</kbd>        |
| 31   | `1F` | Keyboard 2 and @                  | `03`         | `03`       |         | <kbd>2</kbd>           | <kbd>@</kbd>        | <kbd>2</kbd>           | <kbd>&quot;</kbd>   | <kbd>2</kbd>                  | <kbd>@</kbd>        |
| 32   | `20` | Keyboard 3 and #                  | `04`         | `04`       |         | <kbd>3</kbd>           | <kbd>#</kbd>        | <kbd>3</kbd>           | <kbd>&pound;</kbd>  | <kbd>3</kbd>                  | <kbd>#</kbd>        |
| 33   | `21` | Keyboard 4 and $                  | `05`         | `05`       |         | <kbd>4</kbd>           | <kbd>&dollar;</kbd> | <kbd>4</kbd>           | <kbd>&dollar;</kbd> | <kbd>4</kbd>                  | <kbd>&dollar;</kbd> |
| 34   | `22` | Keyboard 5 and %                  | `06`         | `06`       |         | <kbd>5</kbd>           | <kbd>%</kbd>        | <kbd>5</kbd>           | <kbd>%</kbd>        | <kbd>5</kbd>                  | <kbd>%</kbd>        |
| 35   | `23` | Keyboard 6 and ^                  | `07`         | `07`       |         | <kbd>6</kbd>           | <kbd>^</kbd>        | <kbd>6</kbd>           | <kbd>^</kbd>        | <kbd>6</kbd>                  | <kbd>^</kbd>        |
| 36   | `24` | Keyboard 7 and &amp;              | `08`         | `08`       |         | <kbd>7</kbd>           | <kbd>&amp;</kbd>    | <kbd>7</kbd>           | <kbd>&amp;</kbd>    | <kbd>7</kbd>                  | <kbd>&amp;</kbd>    |
| 37   | `25` | Keyboard 8 and &ast;              | `09`         | `09`       |         | <kbd>8</kbd>           | <kbd>\*</kbd>       | <kbd>8</kbd>           | <kbd>\*</kbd>       | <kbd>8</kbd>                  | <kbd>\*</kbd>       |
| 38   | `26` | Keyboard 9 and &lpar;             | `0A`         | `0A`       |         | <kbd>9</kbd>           | <kbd>&lpar;</kbd>   | <kbd>9</kbd>           | <kbd>&lpar;</kbd>   | <kbd>9</kbd>                  | <kbd>&lpar;</kbd>   |
| 39   | `27` | Keyboard 0 and &rpar;             | `0B`         | `0B`       |         | <kbd>0</kbd>           | <kbd>&rpar;</kbd>   | <kbd>0</kbd>           | <kbd>&rpar;</kbd>   | <kbd>0</kbd>                  | <kbd>&rpar;</kbd>   |
| 40   | `28` | Keyboard Return (ENTER)           | `1C`         | `1C`       |         | <kbd>Enter</kbd>       |                     | <kbd>Enter</kbd>       |                     | <kbd>Enter</kbd>              |                     |
| 41   | `29` | Keyboard ESCAPE                   | `01`         | `01`       |         | <kbd>Escape</kbd>      |                     | <kbd>Escape</kbd>      |                     | <kbd>Escape</kbd>             |                     |
| 42   | `2A` | Keyboard DELETE (Backspace)       | `0E`         | `0E`       |         | <kbd>Backspace</kbd>   |                     | <kbd>Backspace</kbd>   |                     | <kbd>Backspace</kbd>          |                     |
| 43   | `2B` | Keyboard Tab                      | `0F`         | `0F`       |         | <kbd>Tab</kbd>         |                     | <kbd>Tab</kbd>         |                     | <kbd>Tab</kbd>                |                     |
| 44   | `2C` | Keyboard Spacebar                 | `39`         | `39`       |         | <kbd>Spacebar</kbd>    |                     | <kbd>Spacebar</kbd>    |                     | <kbd>Spacebar</kbd>           |                     |
| 45   | `2D` | Keyboard - and (underscore)       | `0C`         | `0C`       |         | <kbd>-</kbd>           | <kbd>\_</kbd>       | <kbd>-</kbd>           | <kbd>\_</kbd>       | <kbd>-</kbd>                  | <kbd>=</kbd>        |
| 46   | `2E` | Keyboard = and +                  | `0D`         | `0D`       |         | <kbd>=</kbd>           | <kbd>+</kbd>        | <kbd>=</kbd>           | <kbd>+</kbd>        | <kbd>^</kbd>                  | <kbd>~</kbd>        |
| 47   | `2F` | Keyboard &lsqb; and &lcub;        | `1A`         | `1A`       |         | <kbd>&lsqb;</kbd>      | <kbd>&lcub;</kbd>   | <kbd>&lsqb;</kbd>      | <kbd>&lcub;</kbd>   | <kbd>@</kbd>                  | <kbd>\`</kbd>       |
| 48   | `30` | Keyboard &rsqb; and &rcub;        | `1B`         | `1B`       |         | <kbd>&rsqb;</kbd>      | <kbd>&rcub;</kbd>   | <kbd>&rsqb;</kbd>      | <kbd>&rcub;</kbd>   | <kbd>&lsqb;</kbd>             | <kbd>&lcub;</kbd>   |
| 49   | `31` | Keyboard &bsol; and &vert;        | `2B`         | `2B`       |         | <kbd>&bsol;</kbd>      | <kbd>&vert;</kbd>   | `NA`                   |                     | `NA`                          |                     |
| 50   | `32` | Keyboard Non-US # and ~           | `2B`         | `2B`       |         | `NA`                   |                     | <kbd>#</kbd>           | <kbd>~</kbd>        | <kbd>&rsqb;</kbd>             | <kbd>&rcub;</kbd>   |
| 51   | `33` | Keyboard ; and :                  | `27`         | `27`       |         | <kbd>;</kbd>           | <kbd>:</kbd>        | <kbd>;</kbd>           | <kbd>:</kbd>        | <kbd>;</kbd>                  | <kbd>+</kbd>        |
| 52   | `34` | Keyboard ' and "                  | `28`         | `28`       |         | <kbd>&apos;</kbd>      | <kbd>&quot;</kbd>   | <kbd>&apos;</kbd>      | <kbd>@</kbd>        | <kbd>:</kbd>                  | <kbd>\*</kbd>       |
| 53   | `35` | Keyboard Grave Accent and Tilde   | `29`         | `29`       |         | <kbd>\`</kbd>          | <kbd>~</kbd>        | <kbd>\`</kbd>          | <kbd>&not;</kbd>    | <kbd>Hankaku / Zenkaku</kbd>  |                     |
| 54   | `36` | Keyboard , and &lt;               | `33`         | `33`       |         | <kbd>,</kbd>           | <kbd>&lt;</kbd>     | <kbd>,</kbd>           | <kbd>&lt;</kbd>     | <kbd>,</kbd>                  | <kbd>&lt;</kbd>     |
| 55   | `37` | Keyboard . and &gt;               | `34`         | `34`       |         | <kbd>.</kbd>           | <kbd>&gt;</kbd>     | <kbd>.</kbd>           | <kbd>&gt;</kbd>     | <kbd>.</kbd>                  | <kbd>&gt;</kbd>     |
| 56   | `38` | Keyboard / and ?                  | `35`         | `35`       |         | <kbd>/</kbd>           | <kbd>?</kbd>        | <kbd>/</kbd>           | <kbd>?</kbd>        | <kbd>/</kbd>                  | <kbd>?</kbd>        |
| 57   | `39` | Keyboard Caps Lock                | `3A`         | `F1 08`    | &check; | <kbd>Caps Lock</kbd>   |                     | <kbd>Caps Lock</kbd>   |                     | <kbd>Eisu / Caps Lock</kbd>   |                     |
| 58   | `3A` | Keyboard F1                       | `3B`         | `3B`       |         | <kbd>F1</kbd>          |                     | <kbd>F1</kbd>          |                     | <kbd>F1</kbd>                 |                     |
| 59   | `3B` | Keyboard F2                       | `3C`         | `3C`       |         | <kbd>F2</kbd>          |                     | <kbd>F2</kbd>          |                     | <kbd>F2</kbd>                 |                     |
| 60   | `3C` | Keyboard F3                       | `3D`         | `3D`       |         | <kbd>F3</kbd>          |                     | <kbd>F3</kbd>          |                     | <kbd>F3</kbd>                 |                     |
| 61   | `3D` | Keyboard F4                       | `3E`         | `3E`       |         | <kbd>F4</kbd>          |                     | <kbd>F4</kbd>          |                     | <kbd>F4</kbd>                 |                     |
| 62   | `3E` | Keyboard F5                       | `3F`         | `3F`       |         | <kbd>F5</kbd>          |                     | <kbd>F5</kbd>          |                     | <kbd>F5</kbd>                 |                     |
| 63   | `3F` | Keyboard F6                       | `40`         | `40`       |         | <kbd>F6</kbd>          |                     | <kbd>F6</kbd>          |                     | <kbd>F6</kbd>                 |                     |
| 64   | `40` | Keyboard F7                       | `41`         | `41`       |         | <kbd>F7</kbd>          |                     | <kbd>F7</kbd>          |                     | <kbd>F7</kbd>                 |                     |
| 65   | `41` | Keyboard F8                       | `42`         | `42`       |         | <kbd>F8</kbd>          |                     | <kbd>F8</kbd>          |                     | <kbd>F8</kbd>                 |                     |
| 66   | `42` | Keyboard F9                       | `43`         | `43`       |         | <kbd>F9</kbd>          |                     | <kbd>F9</kbd>          |                     | <kbd>F9</kbd>                 |                     |
| 67   | `43` | Keyboard F10                      | `44`         | `44`       |         | <kbd>F10</kbd>         |                     | <kbd>F10</kbd>         |                     | <kbd>F10</kbd>                |                     |
| 68   | `44` | Keyboard F11                      | `57`         | `57`       |         | <kbd>F11</kbd>         |                     | <kbd>F11</kbd>         |                     | <kbd>F11</kbd>                |                     |
| 69   | `45` | Keyboard F12                      | `58`         | `58`       |         | <kbd>F12</kbd>         |                     | <kbd>F12</kbd>         |                     | <kbd>F12</kbd>                |                     |
| 70   | `46` | Keyboard PrintScreen              | `E0 37`      | `F3`       | &check; | <kbd>PrintScreen</kbd> |                     | <kbd>PrintScreen</kbd> |                     | <kbd>PrintScreen</kbd>        |                     |
| 71   | `47` | Keyboard Scroll Lock              | `46`         | `F1 09`    | &check; | <kbd>Scroll Lock</kbd> |                     | <kbd>Scroll Lock</kbd> |                     | <kbd>Scroll Lock</kbd>        |                     |
| 72   | `48` | Keyboard Pause                    | `E1 1D 45`   | `E1 1D 45` |         | <kbd>Pause</kbd>       |                     | <kbd>Pause</kbd>       |                     | <kbd>Pause</kbd>              |                     |
| 73   | `49` | Keyboard Insert                   | `E0 52`      | `F0`       | &check; | <kbd>Insert</kbd>      |                     | <kbd>Insert</kbd>      |                     | <kbd>Insert</kbd>             |                     |
| 74   | `4A` | Keyboard Home                     | `E0 47`      | `F0 01`    | &check; | <kbd>Home</kbd>        |                     | <kbd>Home</kbd>        |                     | <kbd>Home</kbd>               |                     |
| 75   | `4B` | Keyboard PageUp                   | `E0 49`      | `F0 02`    | &check; | <kbd>PgUp</kbd>        |                     | <kbd>PgUp</kbd>        |                     | <kbd>PgUp</kbd>               |                     |
| 76   | `4C` | Keyboard Delete Forward           | `E0 53`      | `F0 03`    | &check; | <kbd>Delete</kbd>      |                     | <kbd>Delete</kbd>      |                     | <kbd>Delete</kbd>             |                     |
| 77   | `4D` | Keyboard End                      | `E0 4F`      | `F0 04`    | &check; | <kbd>End</kbd>         |                     | <kbd>End</kbd>         |                     | <kbd>End</kbd>                |                     |
| 78   | `4E` | Keyboard PageDown                 | `E0 51`      | `F0 05`    | &check; | <kbd>PgDn</kbd>        |                     | <kbd>PgDn</kbd>        |                     | <kbd>PgDn</kbd>               |                     |
| 79   | `4F` | Keyboard RightArrow               | `E0 4D`      | `F0 06`    | &check; | <kbd>Right</kbd>       |                     | <kbd>Right</kbd>       |                     | <kbd>Right</kbd>              |                     |
| 80   | `50` | Keyboard LeftArrow                | `E0 4B`      | `F0 07`    | &check; | <kbd>Left</kbd>        |                     | <kbd>Left</kbd>        |                     | <kbd>Left</kbd>               |                     |
| 81   | `51` | Keyboard DownArrow                | `E0 50`      | `F0 08`    | &check; | <kbd>Down</kbd>        |                     | <kbd>Down</kbd>        |                     | <kbd>Down</kbd>               |                     |
| 82   | `52` | Keyboard UpArrow                  | `E0 48`      | `F0 09`    | &check; | <kbd>Up</kbd>          |                     | <kbd>Up</kbd>          |                     | <kbd>Up</kbd>                 |                     |
| 83   | `53` | Keypad Num Lock and Clear         | `45`         | `F1 0A`    | &check; | <kbd>Num Lock</kbd>    |                     | <kbd>Num Lock</kbd>    |                     | <kbd>Num Lock</kbd>           |                     |
| 84   | `54` | Keypad /                          | `E0 35`      | `E0 35`    |         | <kbd>KP-/</kbd>        |                     | <kbd>KP-/</kbd>        |                     | <kbd>KP-/</kbd>               |                     |
| 85   | `55` | Keypad &ast;                      | `37`         | `37`       |         | <kbd>KP-*</kbd>        |                     | <kbd>KP-*</kbd>        |                     | <kbd>KP-*</kbd>               |                     |
| 86   | `56` | Keypad -                          | `4A`         | `4A`       |         | <kbd>KP-—</kbd>        |                     | <kbd>KP-—</kbd>        |                     | <kbd>KP-—</kbd>               |                     |
| 87   | `57` | Keypad +                          | `4E`         | `4E`       |         | <kbd>KP-+</kbd>        |                     | <kbd>KP-+</kbd>        |                     | <kbd>KP-+</kbd>               |                     |
| 88   | `58` | Keypad ENTER                      | `E0 1C`      | `E0 1C`    |         | <kbd>KP-Enter</kbd>    |                     | <kbd>KP-Enter</kbd>    |                     | <kbd>KP-Enter</kbd>           |                     |
| 89   | `59` | Keypad 1 and End                  | `4F`         | `4F`       |         | <kbd>KP-1</kbd>        | <kbd>End</kbd>      | <kbd>KP-1</kbd>        | <kbd>End</kbd>      | <kbd>KP-1</kbd>               | <kbd>End</kbd>      |
| 90   | `5A` | Keypad 2 and Down Arrow           | `50`         | `50`       |         | <kbd>KP-2</kbd>        | <kbd>Down</kbd>     | <kbd>KP-2</kbd>        | <kbd>Down</kbd>     | <kbd>KP-2</kbd>               | <kbd>Down</kbd>     |
| 91   | `5B` | Keypad 3 and PageDn               | `51`         | `51`       |         | <kbd>KP-3</kbd>        | <kbd>PgDn</kbd>     | <kbd>KP-3</kbd>        | <kbd>PgDn</kbd>     | <kbd>KP-3</kbd>               | <kbd>PgDn</kbd>     |
| 92   | `5C` | Keypad 4 and Left Arrow           | `4B`         | `4B`       |         | <kbd>KP-4</kbd>        | <kbd>Left</kbd>     | <kbd>KP-4</kbd>        | <kbd>Left</kbd>     | <kbd>KP-4</kbd>               | <kbd>Left</kbd>     |
| 93   | `5D` | Keypad 5                          | `4C`         | `4C`       |         | <kbd>KP-5</kbd>        |                     | <kbd>KP-5</kbd>        |                     | <kbd>KP-5</kbd>               |                     |
| 94   | `5E` | Keypad 6 and Right Arrow          | `4D`         | `4D`       |         | <kbd>KP-6</kbd>        | <kbd>Right</kbd>    | <kbd>KP-6</kbd>        | <kbd>Right</kbd>    | <kbd>KP-6</kbd>               | <kbd>Right</kbd>    |
| 95   | `5F` | Keypad 7 and Home                 | `47`         | `47`       |         | <kbd>KP-7</kbd>        | <kbd>Home</kbd>     | <kbd>KP-7</kbd>        | <kbd>Home</kbd>     | <kbd>KP-7</kbd>               | <kbd>Home</kbd>     |
| 96   | `60` | Keypad 8 and Up Arrow             | `48`         | `48`       |         | <kbd>KP-8</kbd>        | <kbd>Up</kbd>       | <kbd>KP-8</kbd>        | <kbd>Up</kbd>       | <kbd>KP-8</kbd>               | <kbd>Up</kbd>       |
| 97   | `61` | Keypad 9 and PageUp               | `49`         | `49`       |         | <kbd>KP-9</kbd>        | <kbd>PgUp</kbd>     | <kbd>KP-9</kbd>        | <kbd>PgUp</kbd>     | <kbd>KP-9</kbd>               | <kbd>PgUp</kbd>     |
| 98   | `62` | Keypad 0 and Insert               | `52`         | `52`       |         | <kbd>KP-0</kbd>        | <kbd>Insert</kbd>   | <kbd>KP-0</kbd>        | <kbd>Insert</kbd>   | <kbd>KP-0</kbd>               | <kbd>Insert</kbd>   |
| 99   | `63` | Keypad . and Delete               | `53`         | `53`       |         | <kbd>KP-.</kbd>        | <kbd>Delete</kbd>   | <kbd>KP-.</kbd>        | <kbd>Delete</kbd>   | <kbd>KP-.</kbd>               | <kbd>Delete</kbd>   |
| 100  | `64` | Keyboard Non-US &bsol; and &vert; | `56`         | `56`       |         | `NA`                   |                     | <kbd>&bsol;</kbd>      | <kbd>&vert;</kbd>   | `NA`                          |                     |
| 101  | `65` | Keyboard Application              | `E0 5D`      | `E0 5D`    |         | <kbd>App</kbd>         |                     | <kbd>App</kbd>         |                     | <kbd>App</kbd>                |                     |
| 102  | `66` | Keyboard Power                    | `E0 5E`      | `E0 5E`    |         | <kbd>Power</kbd>       |                     | <kbd>Power</kbd>       |                     | <kbd>Power</kbd>              |                     |
| 103  | `67` | Keypad =                          | `59`         | `59`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 104  | `68` | Keyboard F13                      | `64`         | `64`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 105  | `69` | Keyboard F14                      | `65`         | `65`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 106  | `6A` | Keyboard F15                      | `66`         | `66`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 107  | `6B` | Keyboard F16                      | `67`         | `67`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 108  | `6C` | Keyboard F17                      | `68`         | `68`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 109  | `6D` | Keyboard F18                      | `69`         | `69`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 110  | `6E` | Keyboard F19                      | `6A`         | `6A`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 111  | `6F` | Keyboard F20                      | `6B`         | `6B`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 112  | `70` | Keyboard F21                      | `6C`         | `6C`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 113  | `71` | Keyboard F22                      | `6D`         | `6D`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 114  | `72` | Keyboard F23                      | `6E`         | `6E`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 115  | `73` | Keyboard F24                      | `76`         | `76`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 116  | `74` | Keyboard Execute                  | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 117  | `75` | Keyboard Help                     | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 118  | `76` | Keyboard Menu                     | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 119  | `77` | Keyboard Select                   | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 120  | `78` | Keyboard Stop                     | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 121  | `79` | Keyboard Again                    | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 122  | `7A` | Keyboard Undo                     | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 123  | `7B` | Keyboard Cut                      | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 124  | `7C` | Keyboard Copy                     | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 125  | `7D` | Keyboard Paste                    | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 126  | `7E` | Keyboard Find                     | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 127  | `7F` | Keyboard Mute                     | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 128  | `80` | Keyboard Volume Up                | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 129  | `81` | Keyboard Volume Down              | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 130  | `82` | Keyboard Locking Caps Lock        | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 131  | `83` | Keyboard Locking Numb Lock        | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 132  | `84` | Keyboard Locking Scroll Lock      | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 133  | `85` | Keypad Comma                      | `7E`         | `7E`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 134  | `86` | Keypad Equal Sign                 | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 135  | `87` | Keyboard International1           | `73`         | `73`       |         | `NA`                   |                     | `NA`                   |                     | <kbd>&bsol;</kbd>             | <kbd>\_</kbd>       |
| 136  | `88` | Keyboard International2           | `70`         | `70`       |         | `NA`                   |                     | `NA`                   |                     | <kbd>Hira / Kana</kbd>        |                     |
| 137  | `89` | Keyboard International3           | `7D`         | `7D`       |         | `NA`                   |                     | `NA`                   |                     | <kbd>&yen;</kbd>              | <kbd>&vert;</kbd>   |
| 138  | `8A` | Keyboard International4           | `79`         | `79`       |         | `NA`                   |                     | `NA`                   |                     | <kbd>Convert</kbd>            |                     |
| 139  | `8B` | Keyboard International5           | `7B`         | `7B`       |         | `NA`                   |                     | `NA`                   |                     | <kbd>NonConvert</kbd>         |                     |
| 140  | `8C` | Keyboard International6           | `5C`         | `5C`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 141  | `8D` | Keyboard International7           | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 142  | `8E` | Keyboard International8           | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 143  | `8F` | Keyboard International9           | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 144  | `90` | Keyboard LANG1                    | `F2`         | `F2`       |         | `NA`                   |                     | `NA`                   |                     | <kbd>Kana (macOS)</kbd>       |                     |
| 145  | `91` | Keyboard LANG2                    | `F1`         | `F2 01`    | &check; | `NA`                   |                     | `NA`                   |                     | <kbd>Eisu (macOS)</kbd>       |                     |
| 146  | `92` | Keyboard LANG3                    | `78`         | `78`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 147  | `93` | Keyboard LANG4                    | `77`         | `77`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 148  | `94` | Keyboard LANG5                    | `76`         | `76`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 149  | `95` | Keyboard LANG6                    | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 150  | `96` | Keyboard LANG7                    | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 151  | `97` | Keyboard LANG8                    | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 152  | `98` | Keyboard LANG9                    | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 153  | `99` | Keyboard Alternate Erase          | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 154  | `9A` | Keyboard SysReq/Attention         | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 155  | `9B` | Keyboard Cancel                   | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 156  | `9C` | Keyboard Clear                    | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 157  | `9D` | Keyboard Prior                    | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 158  | `9E` | Keyboard Return                   | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 159  | `9F` | Keyboard Separator                | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 160  | `A0` | Keyboard Out                      | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 161  | `A1` | Keyboard Oper                     | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 162  | `A2` | Keyboard Clear/Again              | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 163  | `A3` | Keyboard CrSel/Props              | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 164  | `A4` | Keyboard ExSel                    | `UNASSIGNED` | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 165  | `A5` | Reserved                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 166  | `A6` | Reserved                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 167  | `A7` | Reserved                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 168  | `A8` | Reserved                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 169  | `A9` | Reserved                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 170  | `AA` | Reserved                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 171  | `AB` | Reserved                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 172  | `AC` | Reserved                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 173  | `AD` | Reserved                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 174  | `AE` | Reserved                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 175  | `AF` | Reserved                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 176  | `B0` | Keypad 00                         | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 177  | `B1` | Keypad 000                        | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 178  | `B2` | Thousands Separator               | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 179  | `B3` | Decimal Separator                 | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 180  | `B4` | Currency Unit                     | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 181  | `B5` | Currency Sub-unit                 | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 182  | `B6` | Keypad &lpar;                     | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 183  | `B7` | Keypad &rpar;                     | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 184  | `B8` | Keypad &lcub;                     | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 185  | `B9` | Keypad &rcub;                     | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 186  | `BA` | Keypad Tab                        | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 187  | `BB` | Keypad Backspace                  | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 188  | `BC` | Keypad A                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 189  | `BD` | Keypad B                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 190  | `BE` | Keypad C                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 191  | `BF` | Keypad D                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 192  | `C0` | Keypad E                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 193  | `C1` | Keypad F                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 194  | `C2` | Keypad XOR                        | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 195  | `C3` | Keypad ^                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 196  | `C4` | Keypad %                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 197  | `C5` | Keypad &lt;                       | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 198  | `C6` | Keypad &gt;                       | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 199  | `C7` | Keypad &amp;                      | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 200  | `C8` | Keypad &amp;&amp;                 | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 201  | `C9` | Keypad &vert;                     | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 202  | `CA` | Keypad &vert;&vert;               | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 203  | `CB` | Keypad :                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 204  | `CC` | Keypad #                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 205  | `CD` | Keypad Space                      | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 206  | `CE` | Keypad @                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 207  | `CF` | Keypad !                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 208  | `D0` | Keypad Memory Store               | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 209  | `D1` | Keypad Memory Recall              | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 210  | `D2` | Keypad Memory Clear               | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 211  | `D3` | Keypad Memory Add                 | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 212  | `D4` | Keypad Memory Subtract            | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 213  | `D5` | Keypad Memory Multiply            | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 214  | `D6` | Keypad Memory Divide              | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 215  | `D7` | Keypad +/-                        | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 216  | `D8` | Keypad Clear                      | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 217  | `D9` | Keypad Clear Entry                | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 218  | `DA` | Keypad Binary                     | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 219  | `DB` | Keypad Octal                      | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 220  | `DC` | Keypad Decimal                    | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 221  | `DD` | Keypad Hexadecimal                | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 222  | `DE` | Reserved                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 223  | `DF` | Reserved                          | `RESERVED`   | `FF`       |         | `NA`                   |                     | `NA`                   |                     | `NA`                          |                     |
| 224  | `E0` | Keyboard LeftControl              | `1D`         | `F1`       | &check; | <kbd>LCtrl</kbd>       |                     | <kbd>LCtrl</kbd>       |                     | <kbd>LCtrl</kbd>              |                     |
| 225  | `E1` | Keyboard LeftShift                | `2A`         | `F1 01`    | &check; | <kbd>LShift</kbd>      |                     | <kbd>LShift</kbd>      |                     | <kbd>LShift</kbd>             |                     |
| 226  | `E2` | Keyboard LeftAlt                  | `38`         | `F1 02`    | &check; | <kbd>LAlt</kbd>        |                     | <kbd>LAlt</kbd>        |                     | <kbd>LAlt</kbd>               |                     |
| 227  | `E3` | Keyboard Left GUI                 | `E0 5B`      | `F1 03`    | &check; | <kbd>LWin</kbd>        |                     | <kbd>LWin</kbd>        |                     | <kbd>LWin</kbd>               |                     |
| 228  | `E4` | Keyboard RightControl             | `E0 1D`      | `F1 04`    | &check; | <kbd>RCtrl</kbd>       |                     | <kbd>RCtrl</kbd>       |                     | <kbd>RCtrl</kbd>              |                     |
| 229  | `E5` | Keyboard RightShift               | `36`         | `F1 05`    | &check; | <kbd>RShift</kbd>      |                     | <kbd>RShift</kbd>      |                     | <kbd>RShift</kbd>             |                     |
| 230  | `E6` | Keyboard RightAlt                 | `E0 38`      | `F1 06`    | &check; | <kbd>RAlt</kbd>        |                     | <kbd>RAlt</kbd>        |                     | <kbd>RAlt</kbd>               |                     |
| 231  | `E7` | Keyboard Right GUI                | `E0 5C`      | `F1 07`    | &check; | <kbd>RWin</kbd>        |                     | <kbd>RWin</kbd>        |                     | <kbd>RWin</kbd>               |                     |
{{</ table >}}

{{< table >}}
Caption: Usage IDs of Generic Desktop Page (0x01) (partial; 81 – 83)
| Usage ID  || Usage Name        | Scancode (Set 1)   || diff | Key          |
| dec | hex  | ^^^^^^^^^^        | MS Ref. | `hid.dll` | ^^^^ | ^^^          |
|-----|------|-------------------|---------|-----------|------|--------------|
| 129 | `81` | System Power Down | `E0 5E` | `E0 5E`   |      | System Power |
| 130 | `82` | System Sleep      | `E0 5F` | `E0 5F`   |      | System Sleep |
| 131 | `83` | System Wake Up    | `E0 63` | `E0 63`   |      | System Wake  |
{{</ table >}}

## Microsoft の変換対応表と実際処理しているであろう `hid.dll` との違いについて

Microsoft の変換対応表と Windows の `hid.dll` を比較してみた結果、対応表とは異なるキーがそこそこ見受けられた。
1つを除いて Scancode の prefix が `0xF0`&ndash;`0xF3` のいずれかとなっており、内部的に特別な処理をしているんだろうなあという印象を受ける。
大まかに分類した結果は以下の通り。

- Keyboard POSTFail (上記で書いた prefix のつかない唯一の例外)
- Scancode (Set 1) において特殊な変換をするキー (Insert, PrintScreen, 等。詳しくは [Scancode まとめ記事](/doc/scancode/)参照のこと。)
- Modifier
- Keyboard LANG2  
- Keyboard Caps Lock

上記の中で、Keyboard POSTFail, Scancode (Set 1) において特殊な変換をするキー、Modifiers、は特殊な扱いであることは概ね理解できる。
Modifier は HID では8種それぞれ 1 bit ずつの flag としても扱われているので、なおのこと。
ただし、<kbd>Pause</kbd> だけそのまま処理されているのはちょっと意外。
LANG2 については、macOS において <kbd>英数</kbd> に割り当てられており、また prefix が LANG1 (<kbd>かな</kbd>) に割り当てられている `0xF2` であるのでこの関係か、と思えてしまう。
しかし標準状態では特に使えるようにはなっていないので、なぜわざわざ prefix を与えて別で処理しているのかはよく分からない。改造配列を自分で作れば使えるかもしれないが……。
Caps Lock についてはますます分からない。日本語環境で一般的な JIS 配列だと <kbd>英数 Caps Lock</kbd> となるから別の処理をしているのか、という程度しか想像できない。

全体的に分かったような分からないような、いまいちしっくりこない感じ。詳しい人に聞いてみたいところ。

## 参考資料 {#reference}

[HID Usage Tables 1.12 (<time datetime="2004-10-28">10/28/2004</time>)](https://www.usb.org/sites/default/files/hut1_12.pdf)
: USB-IF が公開している USB HID Usage の定義。直リンクしてしまっているのでリンク切れしていたら検索でたどり着いて。

[USB HID to PS/2 Scan Code Translation Table](http://download.microsoft.com/download/1/6/1/161ba512-40e2-4cc9-843a-923143f3456c/translate.pdf)
: Microsoft 社が公開している USB HID の Usage ID から Scancode (Set 1) への変換対応表。
  