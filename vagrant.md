# 環境構築

## VirtualBox

仮想環境

ダウンロードしてインストールするだけ。
操作はVagrantから行うので直接触ることはない(?)

## Vagrant

仮想環境を設定ファイル(vagrantfile)に記述した設定通りに起動する。

### 手順

1. ダウンロードしてインストール
2. データ用フォルダ作成 以下 %DATA%
3. Boxを検索 <https://app.vagrantup.com/boxes/search>
4. $ mkdir %DATA%\ubuntu
5. $ cd %DATA%\ubuntu
6. $ vagrant init ubuntu/xenial64
7. %DATA%\vagrantfile を編集 config.vm.network "private_network", ip: "192.168.33.10" コメントアウト解除
8. vagrant up
9. vagrant ssh で接続

### その他

- ホストとの共有フォルダはデフォルトでvagrantfileのあるフォルダ。vagrantfile内で設定可能。

### あとで見る

- 可能な設定
  
## Ansible

環境構築時に変更する設定や実行するコマンドを設定ファイルとして記述し、それを自動実行できる。

Ansibleサーバから構築対象のサーバ(Ansibleホスト)にSSHで接続し、設定を自動実行するイメージ。
ただしバッチのようにただ実行するだけではなく、現在の状態を判断して必要な処理のみ実行される仕組み。
構築対象が多い場合はサーバホスト構成でよいが、少ない場合はVagrantでAnsible Local Provisionerを使えばAnsibleサーバが不要になり、構築対象内で完結できる。

### Ansible Local

VagrantでAnsible Local Provisionerを使えばサーバホスト構成ではなくゲストOSのみでAnsibleを動かせる。
ゲストOSにAnsibleがインストールされている必要があるが、Ansible Localをプロビジョナとして指定するとデフォルトでAnsibleをインストールする(しようとする)のでvagrantfileへの設定のみで利用可能。
また1つのAnsibleLocalで複数台に対して実行することも可能らしい。

### Role, Ansible-galaxy

Roleは再利用可能なplaybookのようなもの。playbookから呼び出して利用可能。
多くのオープンソースのRoleがAnsible-galaxyで共有されている。
よく使われるパッケージ構成などであれば、それらを組み合わせることでほとんど自前でPlaybookを記述せず構築可能。
VagrantでAnsibleを使ってプロビジョニングする場合は、Vagrantfile内でgalaxyの設定を追加する。

### 手順

1. playbookを作成
2. roleを記述したplaybook?を作成
3. VagrantfileでAnsible Localをプロビジョナとして指定
4. vagrant provisionを実行

### その他

- upgrade allするにはaptitudeのインストールが必要

## ubuntu