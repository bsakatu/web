---
title: "Windows で同時に複数のキーボード配列を使用する"
date: 2018-12-11T00:00:00+09:00
draft: true

isCJKLanguage:  true
markup: "mmark"
---

Windows 標準のキーボード配列設定は下記のレジストリで設定されています。この設定は PS/2 キーボードや <abbr title="Universal Serial Bus">USB</abbr> キーボードを問わずにその <abbr title="Personal Computer">PC</abbr> に接続される全てのキーボードに反映されます。

| Name                      | Type        | Value (日本語標準)   |
| ----                      | ----        | ----               |
| `LayerDriverJPN`          | `REG_SZ`    | `kbd106.dll`       |
| `OverrideKeyboardType`    | `REG_DWORD` | `7`                |
| `OverrideKeyboardSubtype` | `REG_DWORD` | `2`                |
Table: `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\i8042prt\Parameters`

しかしこれをキーボード毎に別の配列にする方法も存在します。ただし、調べた限りでは <abbr title="Human Interface Device">HID</abbr> 接続のキーボードにたいしてのみ有効、かつその設定をしたときの接続 USB ポート使用時限定、です。なお Bluetooth キーボードは手持ちがなく検証していないので不明（HID デバイスだからできるはず……）。

1. まずデバイスマネージャーを開いて、配列をシステム標準から変更したいキーボードを選択し、`プロパティ → 詳細 → プロパティ` `デバイス インスタンス パス` を選択します。ここで出てくる値をメモします(例えば `HID\VID_CB10&PID_1156&MI_00\8&278408A0&0&0000` のような値)。
2. レジストリエディタで `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Enum\HID` の下のキーを確認すると、 `VID_***` のようなキーがたくさんぶら下がっているはずです。この中から、先ほど調べた値に一致するキーの下にぶら下がる `Device Parameters` キーを開きます。（前述した値で言うと、`VID_CB10&PID_1156&MI_00` キー下に `8&278408A0&0&0000` キーがあり、さらにその下に `Device Parameters` キーがある）。
3. `Device Parameters` キーには既定のエントリ以外は何も存在しない場合が多いです。作成または編集するエントリは以下の通り（値は US 配列に設定する例）。

| Name                      | Type        | Value (US 配列)    |
| ----                      | ----        | ----              |
| `KeyboardTypeOverride`    | `REG_DWORD` | `4`               |
| `KeyboardSubtypeOverride` | `REG_DWORD` | `0`               |
Table: `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Enum\HID\VID_***\****\Device Parameters`

注意点としては、システム標準のキーボード配列設定では `OverrideKeyboardType` となっているのに対して、各 HID での設定では `KeyboardTypeOverride` となり *Override* の位置が前後逆になっているという点があります。システム的には両者は別の意味を持っているというのは何となく推測していますが、特に確証はありません。またこのレジストリキーでは、`LayerDriver JPN` は効果ありませんので設定不要です。

上記エントリの2つの値の組み合わせによって決められる配列の定義ファイルがあります。その組み合わせはいくつかありますが、日本語を使うユーザーなら基本的には以下の組み合わせで事足りるかと（他にも日本語配列定義 dll はありますが、実用外なので今回は触れません）。なお下記で記載する「日本語用 US 配列」というのは、US 配列キーボードを日本語ユーザーが使うための設定が施された配列なので、かぎりなく純粋な US 配列と同じ設定です（つまり日本語固有キーは使えない）。

| 配列名 | `KeyboardTypeOverride` | `KeyboardSubtypeOverride` | 配列定義 dll |
| ---           | --- | --- | ---          |
| US 配列        | `4` | `0` | `kbdus.dll`  |
| 日本語用 US 配列 | `7` | `0` | `kbd101.dll` |
| AX 配列        | `7` | `1` | `kbdax2.dll` |
| JIS 配列       | `7` | `2` | `kbd106.dll` |
Table: 配列毎の `Type`, `Subtype`, dll

ただし、システムの標準設定（本記事冒頭にあるレジストリエントリ）を書き換えている場合、そこで指定した値の組み合わせは `LayerDriver JPN` エントリに指定した配列定義ファイルで上書きされています。例えば、JIS 配列 ( `OverrideKeyboardType = 7`, `OverrideKeyboardSubtype = 2` ) に対して自分で別の定義ファイルを割り当てている場合、本来の JIS 配列 (`kbd106.dll`) を使うことはできません。2つの値の組み合わせに対して標準でどの配列定義ファイルを読み込むか、という設定がどこに定義されているのか分かればあるいは、というところなのですが今のところ私は知らないので、誰か知っていれば教えて下さい……。

ちなみに PS/2 キーボードもデバイス別に設定できるか試してみましたが、こちらはだめでした。残念。試したのは `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Enum\ACPI` 以下を USB キーボードと同じ要領で。デバイスインスタンスパスとかはあるんですけどね。