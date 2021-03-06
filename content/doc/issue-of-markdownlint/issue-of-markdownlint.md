---
title: "Markdownlint で遭遇しているトラベルのメモ"
date: 2020-05-04T01:00:00+09:00
categories: [Web]
tags: [software, Markdown]
toc: false
draft: false
---

このサイトや会社のメモ書きをするにあたって遭遇してすんなり解決していないトラブル各種をメモしておく。

- code block 内でスペースのみの行に [MD009 - Trailing spaces](https://github.com/DavidAnson/markdownlint/blob/v0.20.2/doc/Rules.md#md009---trailing-spaces) が該当してしまう
  - 例:  diff を貼り付けた場合、変更箇所前後にある空行はスペース1文字の行になるので、これが「行末のスペースが 0 または 2 個ではない」と判定されてしまう
  - 対策: 当該 code block 前後に MD009 の無効化・有効化コメントを挿入（html ファイルにはそのコメントは出力されないので、まあ妥協）
  - そもそも code block 内部がどうなっていようが markdown としては関係ないはずで、なんでここでエラーを出してくるのか理解できない
- 値としてダブルクォーテーションを含む `title` 属性を含めたリンク記法で、URL 文字列が [MD034 - Bare URL used](https://github.com/DavidAnson/markdownlint/blob/v0.20.2/doc/Rules.md#md034---bare-url-used) に該当してしまう
  - 例: `[Google](https://www.google.com ""hoge"")` → `<a href="https://www.google.com" title="&quot;hoge&quot;">Google</a>`
  - 対策
    - `title` 属性の値をシングルクォーテーションで囲う（エラーも出ず正常に出力もされるが、書式としてシングルクォーテーションが許容されているという記述は何処にも見当たらないので、いつ動作しなくなるか分からない）
    - 当該箇所だけ Hugo の文字実体参照への置換を停止し、ダブルクォーテーションは　文字実体参照として自分で入力する（属性値内は parser がダブルクォーテーションやアンパサンドなどを文字実体参照に置換してくれているが、この機能のせいで手動で文字実体参照を記載すると記法の先頭に来るアンパサンドが `&amp;` に置換されてしまい、意図した表示にならない）
  - そもそもリンクの記法で内部にそのままダブルクォーテーションを含んでよいのかよくわからないので正解がなんだかよく分からない。`title` 属性にダブルクォーテーション入れることなんて早々あるわけではないからみんなあんまり困って無さそう。なぜこういう変な問題ばっかりに出くわしてしまうのか。
