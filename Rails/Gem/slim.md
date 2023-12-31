# slimとは

slimとはRails５以降に対応したテンプレートエンジンです。slim記法を用いることでHTMLファイルを最低限の記述で作成することが出来ます。
また、非常に軽量であることから処理速度が早く、記述が少ないことによりメンテナンスも簡単に行うことが出来ます。
slimが適用されたファイルは`〇〇.html.slim`というファイル名になっており、slim→htmlの順にコンパイルされます。


# インストール方法
次のコマンドでインストールするかGemfileにslimを記述し`bundle install`します。
```
gem install slim
```
これにより`html.slim`ファイルを使用することができるようになります。


# 既存のerbファイルのslim化

'html2slim'というgemを使用することで元々存在するerbファイルをslimに変換することができます。
Gemfileに`gem 'html2slim'`を記述し、`bundle install`することでインストールします。

gemをインストールしたら以下のコマンドでerbファイルをslimファイルに変換します。

```
# erbファイルを残して変換
erb2slim 変換するファイルがあるフォルダまたはファイル名

# erbを削除して変換
erb2slim 変換するファイルがあるフォルダまたはファイル名 -d
```


# 記述方法

slimには閉じタグという概念は存在しません。
代わりにインデントを使用しマークアップする範囲を指定します。
インデントは何マスでも問題ありませんが、最低１マス分は空ける必要があります。


## HTMLタグ
slimで記述したHTMLタグはそれぞれ次のようにコンパイルされます。
```slim
div
html
meta
header
footer
table
h1
p
a
ul
li
```
```html
<div></div>
<html></html>
<meta></meta>
<header></header>
<footer></footer>
<table></table>
<h1></h1>
<p></p>
<a><a>
<ul></ul>
<li></li>
```


## テキスト　|
`|`（パイプ）を使うことでslimは`|`より深いインデントに記述された物をテキストとして読み込みます。以下の例のように`|`の下の行にテキストを記述することで以下のようなHTMLに変換されます。
```slim
body
  p
    |
      これはテキストブロックのテストです。
```
```html
<body><p>これはテキストブロックのテストです。</p></body>
```


## =, -
`<%= %>`, `<% %>`といったerb記法をslimで記述する場合はそれぞれ`=`, `-`を使用します。if文のような`<% %>`記法を使う必要がある際にこれらを使用します。

制御コード`-`
```slim
- if current_user.id == @user.id
  p ログアウト
- else
  p ログイン
```
```html
<% if current_user.id == @user.id %>
  <p>ログアウト</p>
<% else %>
  <p>ログイン</P>
<% end %>
```

出力`=`
```slim
- sum = 1 + 1
= sum
```
```html
<% sum = 1 + 1 %>
<%= sum %>


## コメント
`/`でコードコメント、`/!`でHTMLコメントを作成することが出来ます。
```slim
body
  p
    / この行は表示されません。
      この行も表示されません。
    /! html コメントとして表示されます。
```


## id、class
idは`#`、classは`.`で指定します。
```slim
dev.content
  p.title タイトル
dev#content
  p#title タイトル
```
```html
<dev class="content">
  <p class="title">タイトル</hoge>
</dev>
<dev id="content">
  <p id="title">タイトル</hoge>
</dev>
```


## 属性
属性の記述は次のように行います。

- aタグ
```slim
a href='http://www.sample.com' リンク
```
```html
<a href="http://www.sample.com">リンク</a>
```

- imgタグ
``` slim
img src="sample.jpg" alt="画像"
```
```html
<img  src="sample.jpg" alt="画像">
```



# 参考サイト
[github slim](https://github.com/slim-template/slim/blob/main/README.jp.md)

[【爆速で習得】Railsでslimを使う方法から基本文法まで - Qiita](https://qiita.com/ngron/items/c03e68642c2ab77e7283)

[【Rails】 slimの書き方をマスターしよう！ | Pikawaka](https://pikawaka.com/rails/slim)
