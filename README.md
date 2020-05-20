# [bsakatu.net](https://bsakatu.net/) の裏側

私のサイトである [capyBaral](https://bsakatu.net/) のデータを管理するリポジトリ。コードと文章が混在していてライセンスが面倒くさいことになっているが、Hugo のテーマや shortcode を公開したい気持ちとテーマをわざわざ別で管理する面倒くささとが合わさった結果こういう管理に行き着いた。

## 環境

Hugo を使用。テーマは [Hugo Classic](https://github.com/goodroot/hugo-classic) を元にした自前改造版。
元にはしているが改造しまくりでかなり原形をとどめていない。
Hugo Classic 自体は [Hugo XMin](https://github.com/yihui/hugo-xmin) を元にしているらしいので、私のはオリジナルの孫にあたることになる。

## ライセンス

条文等は [LICENSE](./LICENSE) 参照。

サイトの文章部分以外のコード部分については MIT ライセンスを採用している。
孫世代にあたるテーマを使用しているので、原作者と二次改変者と自分の3名分のコピーライトを表記している。
なお、二次改変者である Hugo Classic の作者はテーマのリポジトリ内にある LICENSE ファイルで MIT ライセンスであることは宣言しているが、原作者の表記を消しつつ自分の表記を載せないということをしているので、こちらで勝手に両製作者の表記を追加した。

サイトの文章部分については [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.ja) を採用する。対象は [`./content`](./content) 以下と [`./docs`](./doc) 以下のコードではない文章部分全て。コードとは、文章自体ではない HTML, CSS, JavaScript 等を指す。
