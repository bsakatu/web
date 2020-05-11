---
title: "ハードウェア キーボード レイアウトの変更"
date: 2018-11-25T01:22:59+09:00
categories: [Input]
tags: [keyboard, key-layout, Windows]
toc: false
draft: false

markup: "mmark"
---

Windows 10 において、日本語 <abbr title="Input Method Editor">IME</abbr> のオプションに「ハードウェア キーボード レイアウト」という設定項目がある（ということを今日初めて知った……）。

{{< figure  src="/img/post/2018/change-hardware-keyboard-layout-ime-jp.png" title="ハードウェア キーボード レイアウト変更の設定画面" width="880" height="792" >}}

この設定では「日本語キーボード(106/109 キー)」と「英語キーボード(101/102 キー)」を選択出来る。これによって下記のレジストリが変化する。

Registry path: `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\i8042prt\Parameters`

| 名前                       | 種類         | 日本語（106/109 キー） | 英語（101/102 キー） |
| ----                      | ----        | ----               | ----              |
| `LayerDriverJPN`          | `REG_SZ`    | `kbd106.dll`       | `kbd101.dll`      |
| `OverrideKeyboardSubtype` | `REG_DWORD` | `2`                | `0`               |
| `OverrideKeyboardType`    | `REG_DWORD` | `7`                | `7`               |
Table: 「ハードウェア キーボード レイアウト」の変更によるレジストリの変化

日本語キーボードと英語キーボードとをどうやって識別しているか軽く調べてみた結果、恐らく `LayerDriver JPN` エントリの値を見ているだけと思われる。`OverrideKeyboardType` などをいくら変更しても何も変わらなかったが、`LayerDriver JPN` をちょっとでも標準の `kbd106.dll` または `kbd106n.dll` から変更すると「英語キーボード」が勝手に選択されていた（オプション画面を閉じておいて再度開きに行けば反映されている）。この設定があれば、以前の Windows よりは楽に JIS 配列と US 配列の行き来が出来るようになるので、まあ少なくとも改善と考えていいと思われる。

この設定を調査している中で、最初の1回だけ日本語キーボード設定に戻したら `LayerDriver JPN` が `kbd106n.dll` になっていたのだが、あれは何だったのか……。その後再現せず。すっきりしないけどまあ、大差はないしどうせ使わないキーだし、ここで出来る設定変更は自分に関わりないし、面倒くさいので調査はここまで。
