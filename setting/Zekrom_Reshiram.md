# ZekromとReshiramの設定方法

> macから設定する場合の方法であるが，他のものでもほぼ同じように設定できるはず

## 最初に行う設定

1. 手元のPCに，`Remote Explorer`を入れる
1. SSHの横の歯車マークをクリックし，SSH構成ファイルを開く
1. 以下を貼り付ける
    ```config
    Host Zekrom
        User hashimoto
        Hostname 192.168.100.27
        ForwardX11 yes
        HostKeyAlgorithms +ssh-rsa
        PubkeyAcceptedAlgorithms +ssh-rsa
        ServerAliveInterval 60
        ServerAliveCountMax 10

    Host Reshiram
        User hashimoto
        Hostname 192.168.100.20
        ForwardX11 yes
        HostKeyAlgorithms +ssh-rsa
        PubkeyAcceptedAlgorithms +ssh-rsa
        ServerAliveInterval 60
        ServerAliveCountMax 10
    ```
1. 上記の`User`名を自分の名前に変える
1. `terminal`を開いて，以下を実行しログインする（Zekromの場合）
    ```zsh
    ssh Zekrom
    ```
1. `yes`と打ち，その後パスワードを入れる
1. 同様にReshiramも行う

## パスワードの設定

1. 手元のPCで，`~/.ssh`に移動する．階層`~`に`.ssh`フォルダがなければ，`mkdir .ssh`で作ること
1. 次のコマンドを打って，公開鍵を作る
    ```
    ssh-keygen -t rsa -b 4096
    ```
    いろいろ聞かれるが，ずっとEnterを押していれば勝手に作られる
1. `ssh-copy-id`でサーバ側に持っていく
    ```
    ssh-copy-id Zekrom
    ```
    これはReshiramの方も同様に．これで設定完了

## GitHubの設定

1. `gh`コマンドをインストールする
    ```bash
    sudo apt install gh
    ```
1. 自分のアカウントにログインする
    ```bash
    gh auth login
    ```
    1. `GitHub.com`を選択
    1. `SSH`を選択
    1. `id_ecdsa.pub`を選択
    1. `paste an authentication token`を選択
1. 手元のPCでブラウザを開き，自分のgithubページアカウントを開く
    1. `Settings`を開く
    1. 一番下の項目`Developer Settings`を開く
    1. Personal access tokensの中の`Tokens(classic)`を開く
    1. 右上の`Generate new token`を開き，`Generate new token(classic)`を選択
    1. Noteに`zekrom/Reshiram`と入れる
    1. Expirationを`90 days`にする
    1. Select scopesのチェックボックスは，terminal上に必要最低限の項目が出ているはずなので，これらを選択する
    1. Generate tokenを押して，tokenを作る
1. 出てきたtokenをコピーして，terminal上に貼り付ける
1. 設定完了（もう一方のReshiramも同じようにやるが，3の項目は飛ばして同じtokenを使えば良い


## Juliaの設定

1. 手元のPCで[juliaのダウンロードページ](https://julialang.org/downloads/)にアクセスする
1. ページの一番上のコマンドをコピーして，貼り付け，実行
1. `.bashrc`を更新
    ```bash
    source .bashrc
    ```
1. 完了．`julia`と打ち，使えることを確認する
