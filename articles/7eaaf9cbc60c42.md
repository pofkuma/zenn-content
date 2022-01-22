---
title: "RailsでモデルのインスタンスからURLのパスを取得する仕組み"
emoji: "📚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["rails"]
published: true
---

次のように`redirect_to`の引数にインスタンス変数を指定する書き方について、何が行われているのかわからなかったので調べてみた。

```ruby
redirect_to @book
```
このように、`@book`というインスタンス変数でオブジェクトを渡すと、`/books/:id`といったパスにリダイレクトされる。
例えば`@book.id`が`9`なら、`/book/9`となる。

これは、**リソースフルルーティング**と呼ばれる仕組みによって実現されている。

```ruby:route.rb
Rails.application.routes.draw do
  resources :books
end
```

`resources`メソッドを使って上記のように定義すると、以下のルーティングが作成される。
(`rails routes`コマンドで確認)

Prefix | Verb | URI Pattern | Controller#Action
---------|----------|---------|---------
books | GET | /books(.:format) | books#index
 | | POST | /books(.:format) | books#create g
new_book | GET | /books/new(.:format) | books#new
edit_book | GET | /books/:id/edit(.:format) | books#edit
book | GET | /books/:id(.:format) | books#show
 | | PATCH | /books/:id(.:format) | books#update
 | | PUT | /books/:id(.:format) | books#update
 | | DELETE | /books/:id(.:format) | books#destroy


`redirect_to @book`は、実際には`book_path`というヘルパーメソッドを呼び出してリダイレクトが行われる。(`_path`のプレフィクスが`book`)
```ruby
redirect_to book_path(@book)
```
つまり、`@book`に格納されているオブジェクト(インスタンス)を元にURLが生成され、リダイレクト先が自動的に決まる。この場合、`books#show`アクションが呼び出される。

また、`link_to`でも同様に書ける。
```ruby
<%= link_to 'Book', @book >
```

## 参考リンク
https://railsguides.jp/routing.html
https://api.rubyonrails.org/v7.0/classes/ActionDispatch/Routing/Mapper/Resources.html#method-i-resources
https://api.rubyonrails.org/classes/ActionController/Redirecting.html#method-i-redirect_to
