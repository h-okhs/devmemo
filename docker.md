# docker

## 前準備

- Hyper-Vのイメージディレクトリ変更

## mysql

サーバーのみでいいはずなのでmysql/mysql-serverを使用

1. $ docker mysql/mysql-server:8.0
2. $ docker run --name=mysql1 -d mysql/mysql-server:8.0