# rubymemo

## Windows環境構築

Rubyのバージョンは切り替えられるようにしておいたほうがよさそう。
chocolatry(パッケージ管理), uru(rbenvみたいなの)といったツールを利用してruby本体などのインストールを行う。

1. 前準備
   1. gemインストール時のデフォルトオプションの指定：%USERPROFILE%\.gemrc に gem: --no-ri --no-rdoc
2. chocolatryインストール
   1. <https://chocolatey.org/install>
   2. chocolateyでのパッケージインストール先環境変数変更
3. rubyインストール
   1. cinst ruby –version 2.4.2.2 -ia ‘/dir=C:\tools\ruby2422’
   2. 二つ目以降は-force
4. uruインストール
   1. <https://bitbucket.org/jonforums/uru/wiki/Downloads>
   2. cinst uru.0.8.5.nupkg
   3. Set-ExecutionPolicy -ExecutionPolicy RemoteSigned
   4. uru admin add E:\tools\ruby2422\bin --tag ruby2422
5. MSYS2インストール
   1. cinst MSYS2 --params "/NoUpdate"
   2. cmd/powershe再起動(VSCodeのターミナルの場合はVSCode再起動)
   3. ridk install 2 3
6. 各種GEMインストール
   1. uru ruby2422
   2. gem install bunder
   3. gem install rubocop ほか
   4. gem install ruby-debug-ide
   5. gem install debase (or gem install byebug)

## gem

gemのインストールには二種類ある。PC内のRuby環境全体に適用されるものと、アプリケーション内にのみ適用されるもの。よく見るgemコマンドでインストールした場合は、Ruby環境全体に適用される。ただし、全体に適用してしまうとバージョンの整合性を取るのが難しくなるため?、あまり使わないのが一般的のよう。
代わりにBundlerというgemを利用してアプリケーションごとにgemをインストールするのが良いらしい。

## Railsアプリケーション生成

bundlerでまずrailsのみをインストールしてアプリケーションを生成し、アプリケーション内のGemfileを使用して各種gemをアプリケーション内にのみインストールする。
E:\repo\rails01に生成する場合

1. uru ruby243
2. E:\repoで bundle init
3. Gemfile変更 gem "rails", "5.1.6"
4. bundle install --path=vendor/bundle
5. bundle exec rails new rails01 --skip-bundle
6. ※オプション .bundle, vendor, gemfile, gemfile.lockを削除
7. cd rails01
8. bundle install --path=vendor/bundle
9. gitignoreに/vendor追加

