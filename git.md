# GIT

## github SSH接続

1. 鍵作成
   1. ssh-keygen -t rsa -b 4096 -C "メールアドレス" -f "xxx_id_rsa"
2. 鍵をgithubに登録
   1. type ~\.ssh\xxx_id_rsa.pub | clip
   2. githubの設定に貼り付け
3. 環境変数 GIT_SSH=C:\Windows\system32\OpenSSH\ssh.exe
4. ssh config作成
   1. ~\.ssh\config 作成
      ```
      Host github-xxx
        User git
        Port 22
        HostName github.com
        IdentityFile ~/.ssh/xxx_id_rsa
        TCPKeepAlive yes
        IdentitiesOnly yes
      ```
5. SSH認証サービス有効化
   1. OpenSSH Authentication Agent
6. 接続確認
   1. ssh -T github-xxx
7. git コマンドで確認

## トピックブランチ統合

1. master最新化
   1. git checkout master
   2. git pull origin master
2. topicにmaster取り込み
   1. git checkout topic
   2. git rebase master
   3. コンフリクト修正後 git add [file_name]
   4. git rebase --continue
3. トピックブランチPUSH
   1. git push -f origin topic
4. masterにマージ
   1. git checkout master
   2. git merge --no-ff tuto2

## 小技

- コミットメッセージ修正
  $ git commit --amend -m "new message"
- herokuにブランチをmasterとしてpush
  $ git push heroku ブランチ名:master --force