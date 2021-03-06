---
title: "Hugo で各ページ固有のスタイルシートを呼び出す"
date: 2018-11-13T01:16:08+09:00
categories: [Web]
tags: [software, Hugo, HTML, CSS,]
toc: false
draft: false
---

[Hugo](https://gohugo.io/) で各ページ固有の CSS を適用したい場合にどうするか悩んだのでその備忘録。

このサイトでは<time datetime="2018-11-13">現時点</time>で blog 的なページは `/post` 以下、個別のしっかりした記事は `/doc` 以下、というふうに分けている（つもり）。その中で /doc 以下の階層で個別のスタイルシートを適用したくなり、かつそれを記事ファイルと同じフォルダに置きたかったので、一工夫必要でした。

まず記事の元となる `hoge.md` と個別スタイルの `custom.css` が `/doc/hoge/` フォルダにいるとします。ここでテンプレートの `header.html` のスタイルシートを呼び出しているあたりに下記のコードを追加します。

```golang
{{ if ne (print (.Resources.Match "custom.css")) "[]" }}
<link rel="stylesheet" href="custom.css" />
{{ end }}
```

`custom.css` が同階層に存在すれば `custom.css` を呼び出す HTML タグを挿入するという流れです。ここでしばらくはまったのが、まずどの関数で存在判定をするのか。結果としては `.Resources.Match "hoge"` だったわけですが、他にも `.Get` などの機能が似ていそうな関数がいくつかあって調べるのに時間がかかりました。`.Resources.Match` は指定されたファイル等が存在しない場合に空文字列を返します。これが `"[]"` でした。しかしこのままでは判定に失敗するので、 `.Resources.Match` を `print` でラップしてあげて文字列化し、無事判定に成功しました。

ここまで書いて判定文字列 `"[]"` をダブルクォーテーションで囲わなければよかったような気もしますが面倒なので今回は無視します。
{{< ins datetime="2020-05-04T00:33:00+09:00">}}やっぱり無理だったからこれで良し。{{</ ins >}}

{{< ins datetime="2020-05-22T21:00:00+09:00" >}}
空の array は `nil` で表現できるようなので結局こうなった。すっきり。

```golang
{{- if ne (.Resources.Match "custom.css") nil -}}
<link rel="stylesheet" href="custom.css" />
{{- end -}}
```

{{</ ins >}}
