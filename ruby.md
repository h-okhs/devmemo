# 開発 memo

## Windows 環境構築

### Ruby 本体

Ruby のバージョンは切り替えられるようにしておいたほうがよさそう。
chocolatry(パッケージ管理), uru(rbenv みたいなの)といったツールを利用してインストールを行う。

1. 前準備
   1. gem インストール時のデフォルトオプションの指定：`msys2\home\[USERNAME]\.gemrc に gem: --no-ri --no-rdoc`
2. chocolatey インストール
   1. <https://chocolatey.org/install>
   2. 環境変数 ChocolateyToolsLocation(chocolatey でのパッケージインストール先)変更
3. ruby インストール
   1. \$ cinst ruby –version 2.4.3.1 -ia ‘/dir=C:\tools\ruby2431’
   2. 二つ目以降は-force
4. uru インストール
   1. <https://bitbucket.org/jonforums/uru/wiki/Downloads>
   2. \$ cinst uru.0.8.5.nupkg
   3. \$ Set-ExecutionPolicy -ExecutionPolicy RemoteSigned
   4. \$ uru admin add E:\tools\ruby2431\bin --tag ruby243
5. MSYS2 インストール
   1. `$ cinst MSYS2 --params "/NoUpdate"`
   2. 前準備で作った`.gemrc`を`msys2/home/USERNAME`にコピー
   3. cmd/powershell 再起動(VSCode のターミナルの場合は VSCode 再起動)
   4. `$ ridk install 2 3`
6. 各種 GEM インストール
   1. \$ uru ruby243
   2. \$ gem install bunder

### VSCode

#### 必要 GEM

- rubocop
- solargraph
- ruby-debug-ide
- debase (or byebug)

#### 拡張機能

- Ruby
- Beautify
- Ruby Comment Doc
- Ruby Solorgraph

## gem

gem のインストールには二種類ある。PC 内の Ruby 環境全体に適用されるものと、アプリケーション内にのみ適用されるもの。よく見る gem コマンドでインストールした場合は、Ruby 環境全体に適用される。ただし、全体に適用してしまうとバージョンの整合性を取るのが難しくなるため?、あまり使わないのが一般的のよう。
代わりに Bundler という gem を利用してアプリケーションごとに gem をインストールするのが良いらしい。

## Rails アプリケーション生成

bundler でまず rails のみをインストールしてアプリケーションを生成し、アプリケーション内の Gemfile を使用して各種 gem をアプリケーション内にのみインストールする。
E:\repo\rails01 に生成する場合

1. \$ uru ruby243
2. E:\repo で \$ bundle init
3. Gemfile 変更 gem "rails", "5.1.6"
4. \$ bundle install --path=vendor/bundle
5. \$ bundle exec rails new rails01 --skip-bundle(ruby 本体にインストールされるのを防止) -T(テスト生成しない)
6. ※オプション .bundle, vendor, gemfile, gemfile.lock を削除
7. \$ cd rails01
8. \$ bundle install --path=vendor/bundle --without production
9. gitignore に/vendor 追加

これで rails アプリケーション内で使われる gem はアプリケーション内にのみ適用される状態になった。bundle install のオプションは.bundle/config に保存されるため、gem を追加する場合など、2 回目以降は bundle install のみで OK。ここでインストールした gem を実行する場合は、bundle exec rails などのように bundle exec をつけて実行する。

## Rails チュートリアル

### 1 章

- Helloworld と bundle を使用した環境構築(上記)
- コントローラへのメソッド追加と、それに対応するページとの紐づけを routes.rb に記述
- デプロイ環境ごとの gem 定義、bundle install
  - group :development, :test do ～、group :production do ～
  - \$ bundle install --without production
- ローカルサーバ起動
  - \$ bundle exec rails server
- heroku へのデプロイ
  - \$ git push heroku master

### 2 章

- scaffold によるソース自動生成
  - \$ bundle exec rails generate scaffold User name:string email:string
- モデルに合わせたデータベースの構築
  - \$ bundle exec rails db:migrate
- モデルでのバリデーション
  - 長さ: validates :content, length: { maximum:140 }
  - 必須: validates :name, presence: true
- モデル間の関連
  - 相手 n : has_many :microposts
  - 相手 1 : belongs_to:user
- モデルは ActiveRecord:base を継承

### 3 章

- Controller 自動生成
  - \$ rails generate controller StaticPages home help
    - ルーティング、views に erb、helper、test 自動追加
  - 削除
    - \$ rails destroy controller StaticPages
- test
  - RSpec の項に記載
- コード
  - erb でのスクリプティング
    - 変数(ラベル)宣言：<% provide(:ラベル名, 値) %>
    - 変数内容出力：<%= yield(:ラベル名)>
  - application.html.erb が全ページのベーステンプレート？

### 4 章

- view では組み込み関数以外に自作の関数も利用可
- application_helper.rb でのヘルパメソッドの定義
- bundle exec rails console
- API <http://ruby-doc.org/> <https://docs.ruby-lang.org/ja/search/>
- 文字列
  - 文字列展開 "#{変数名} 文字列"
  - s.split("z")
  - s.upcase, s.downcase
  - ダブルクオート:式展開・エスケープあり　シングル:なし
- puts:改行あり print:改行なし
- 末尾の?は boolean 返却　 s.nil?, s.empty?, s.include?("aaa")
- 論理演算子=C と同じ
- 後続の if, unless で条件を満たす場合のみ実行
- メソッドから return しない場合、最後に評価された式が返却される
- 配列
  - a[1,2,3] ⇒ a[0] 添え字マイナスも可
  - a.first, a.second, a.last, a.empty, a.include, a.reverse, a.shuffle ⇒ a は変更されない
  - a.sort! ⇒ a が変更される=破壊的 ⇒ !をつける
  - a.push(6), a << "7" << "8" ⇒ 異なる型が共存可能
  - a.join ⇒ "123678", a.join(",") ⇒ "1,2,3,6,7,8"
  - %w[a b c] ⇒ ["a","b","c"]
- 範囲オブジェクト
  - 0..9 , (1..10).to_a, a[0..2]⇒[1,2,3], ('a'..'c').to_a⇒["a","b","c"]
  - Range.new(1,10)
- ブロック
  - {} もしくは do..end
  - |変数|
  - 3.times {puts "aa"} ⇒ "aa"\n"aa"\n"aa"
  - map は渡されたブロック内の処理を各要素に対して適用し結果を返す
    - %w[a b c].map {|c| c.upcase} = %w[a b c].map {&:defupcase}
  - yeller
    ```Ruby
      def yeller (a = [])
        a.map{|b| b.upcase}.join
      end
    ```
  - "foobar".split('').shuffle.join
- ハッシュ

  - =連想配列
  - a = {} 空ハッシュ、a["key"] = "value"、a = {"key1"=>"value1", "key2"=>"value2"}
  - key にはシンボルを使うほうが一般的
  - a = {:key1=>"value1", :key2=>"value2"}, a[:key1]でアクセス
  - a = {key1:"value1", key2:"value2"}も等価
  - each 可
    - hashes.each do |key, value|
  - メソッド呼び出しの最後の引数の場合、{}不要

- シンボル

  - :symbol
  - オブジェクトではない変数のようなもの？
  - でも .inspect は使用可

- クラス
  - String, Hash, Range, Array...
  - a = String.new("AAAA")
  - a.class ⇒ String, a.class.superclass ⇒ Object, a.class.superclass.superclass ⇒ BasicObject
  - 継承
    - class SampleClass < String
  - 組み込みクラスを変更可
  - attr_accessor ⇒ getter setter 定義
  - インスタンス変数は常に@始まり

### 5 章

- link_to "Link text", "url"
- image_tag "ファイル名", alt: "alt"
  - app/assets/images/ を自動的に参照
- パーシャル
  - `<%= render 'layouts/shim' %>` ⇒ `layouts/_shim.html.erb` を埋め込む
- アセットパイプライン
  - 静的コンテンツを最小化、圧縮、連結する
  - assets という名前のフォルダ(app 以外も含む)配下がすべて対象、assets.rb で設定も可能

## Rails

- `stylesheet_link_tag 'application', media: 'all', 'data-turbolinks-track': 'reload'` ⇒ `<link data-turbolinks-track="true" href="/assets/application.css" media="all" rel="stylesheet" />`

## RSpec

### Rspec インストール/実行

1. Gemfile に 'rspec-rails', 'wdm', gem 'spring-commands-rspec' 追加
2. \$ bundle install --without production
3. \$ bundle exec rails g rspec:install
4. \$ bundle exec spring binstub rspec
5. \$ bundle exec rspec

### テスト作成

spec フォルダ内の\_spec.rb ファイルがテスト対象を作る

#### Controller

- \$ bundle exec rails g rspec:controller コントローラ名 でテストコード雛形生成
- レスポンスの中身をチェックする場合は render_views を記述
- assert_select 使用可

## Guard

### Guard インストール/実行

1. Gemfile に 'guard-rspec' 追加
2. \$ bundle install --without production
3. \$ bundle exec guard init rspec ⇒ Guardfile 生成
4. \$ bundle exec guard
