# 開発memo

## Windows環境構築

### Ruby本体

Rubyのバージョンは切り替えられるようにしておいたほうがよさそう。
chocolatry(パッケージ管理), uru(rbenvみたいなの)といったツールを利用してruby本体などのインストールを行う。

1. 前準備
   1. gemインストール時のデフォルトオプションの指定：%USERPROFILE%\.gemrc に gem: --no-ri --no-rdoc
2. chocolateyインストール
   1. <https://chocolatey.org/install>
   2. 環境変数 ChocolateyToolsLocation(chocolateyでのパッケージインストール先)変更
3. rubyインストール
   1. $ cinst ruby –version 2.4.2.2 -ia ‘/dir=C:\tools\ruby2422’
   2. 二つ目以降は-force
4. uruインストール
   1. <https://bitbucket.org/jonforums/uru/wiki/Downloads>
   2. $ cinst uru.0.8.5.nupkg
   3. $ Set-ExecutionPolicy -ExecutionPolicy RemoteSigned
   4. $ uru admin add E:\tools\ruby2422\bin --tag ruby2422
5. MSYS2インストール
   1. $ cinst MSYS2 --params "/NoUpdate"
   2. cmd/powershell再起動(VSCodeのターミナルの場合はVSCode再起動)
   3. $ ridk install 2 3
6. 各種GEMインストール
   1. $ uru ruby2422
   2. $ gem install bunder

### VSCode

#### 必要GEM

- rubocop
- ruby-debug-ide
- debase (or byebug)
- htmlbeautifier

#### 拡張機能

- Ruby
- ERB Formatter/Beautify
- Ruby Comment Doc
- Ruby Solorgraph

## gem

gemのインストールには二種類ある。PC内のRuby環境全体に適用されるものと、アプリケーション内にのみ適用されるもの。よく見るgemコマンドでインストールした場合は、Ruby環境全体に適用される。ただし、全体に適用してしまうとバージョンの整合性を取るのが難しくなるため?、あまり使わないのが一般的のよう。
代わりにBundlerというgemを利用してアプリケーションごとにgemをインストールするのが良いらしい。

## Railsアプリケーション生成

bundlerでまずrailsのみをインストールしてアプリケーションを生成し、アプリケーション内のGemfileを使用して各種gemをアプリケーション内にのみインストールする。
E:\repo\rails01に生成する場合

1. $ uru ruby243
2. E:\repoで $ bundle init
3. Gemfile変更 gem "rails", "5.1.6"
4. $ bundle install --path=vendor/bundle
5. $ bundle exec rails new rails01 --skip-bundle(ruby本体にインストールされるのを防止) -T(テスト生成しない)
6. ※オプション .bundle, vendor, gemfile, gemfile.lockを削除
7. $ cd rails01
8. $ bundle install --path=vendor/bundle
9. gitignoreに/vendor追加

## Railsチュートリアル

### 1章

- Helloworldとbundleを使用した環境構築(上記)
- コントローラへのメソッド追加と、それに対応するページとの紐づけをroutes.rbに記述
- デプロイ環境ごとのgem定義、bundle install
  - group :development, :test do ～、group :production do ～
  - $ bundle install --without production
- ローカルサーバ起動
  - $ bundle exec rails server
- herokuへのデプロイ
  - $ git push heroku master

### 2章

- scaffoldによるソース自動生成
  - $ bundle exec rails generate scaffold User name:string email:string
- モデルに合わせたデータベースの構築
  - $ bundle exec rails db:migrate
- モデルでのバリデーション
  - 長さ: validates :content, length: { maximum:140 }
  - 必須: validates :name, presence: true
- モデル間の関連
  - 相手n : has_many :microposts
  - 相手1 : belongs_to:user
- モデルはActiveRecord:baseを継承

### 3章

- Controller自動生成
  - $ rails generate controller StaticPages home help
    - ルーティング、viewsにerb、helper、test自動追加
  - 削除
    - $ rails destroy controller StaticPages
- test
  - RSpecの項に記載
- コード
  - erbでのスクリプティング
    - 変数(ラベル)宣言：<% provide(:ラベル名, 値) %>
    - 変数内容出力：<%= yield(:ラベル名)>
  - application.html.erbが全ページのベーステンプレート？

### 4章
### 5章

## RSpec

### インストール/実行

1. Gemfileに 'rspec-rails', 'wdm', gem 'spring-commands-rspec' 追加
2. $ bundle install --without production
3. $ bundle exec rails g rspec:install
4. $ bundle exec spring binstub rspec
5. $ bundle exec rspec

### テスト作成

specフォルダ内の_spec.rbファイルがテスト対象を作る

#### Controller

- $ bundle exec rails g rspec:controller コントローラ名 でテストコード雛形生成
- レスポンスの中身をチェックする場合はrender_viewsを記述
- assert_select使用可

## Guard

### インストール/実行

1. Gemfileに 'guard-rspec' 追加
2. $ bundle install --without production
3. $ bundle exec guard init rspec ⇒ Guardfile生成
4. $ bundle exec guard
