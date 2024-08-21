# Pythonのインストール

mac環境におけるPythonのインストールは少し特殊なので，以下の通りに行うことを推奨

## homebrewのインストール

1. homebrewのインストールコマンドをコピー

    ブラウザでhomebrewと検索し，一番上に出てくる?サイトの「インストール」のコマンドをコピーする

2. macのアプリケーションの中から「ターミナル」を探し，起動する
3. コマンドラインに，コピーしたコマンドを貼り付けて，実行する -> homebrewがインストールされます

## pythonのインストール

macにはデフォルトでpython環境が入っていますが，最新ではないようなので，homebrewでインストールをする

1. ターミナル上で，以下のコマンドを入力して，pythonをインストールする

```zsh
    brew install python3
```

2. zshrcにpythonのエイリアスを追加する

    2.1. まず，ホームディレクトリに戻る

    ```zsh
        cd
    ```

    2.2. 次に，テキストエディタを開く

    ```zsh
        nano .zshrc
    ```

    2.3. `ctrl + v`を押して，一番下の行までいく

    2.4. 以下のコマンドを書き込んで，エイリアスを作成する

    ```zsh
        alias python=`python3`
        alias pip=`pip3`
    ```

    2.5 `xtrl + x`を押して，エディタを閉じる．この時，保存するか聞かれるので，`y`を押して保存

    2.6 zshrcを更新する

    ```zsh
        source .zshrc
    ```

3. 仮想環境を作成する

    2.1. ホームディレクトリに，`.venv`フォルダを作成する

    ```zsh
        mkdir ~/.venv
    ```

    2.2 python仮想環境をここに構築する

    ```zsh
        python3 -m venv ~/.venv
    ```

    これで，仮想環境が構築される．仮想環境に入るには，

    ```zsh
    source ~/.venv/bin/activate
    ```

    を入力する

## パッケージのインストール

パッケージをインストールするには，pipを用いる．

1. python仮想環境に入る

```zsh
    source ~/.venv/bin/activate
```

2. python対話環境に入る

```zsh
    python
```

3. pipを利用して，実行するものをインストール

```zsh
    pip ~~
```

これで，パッケージが入ります．

## juliaにPyCallをインストールする

1. python仮想環境に入る

```zsh
    source ~/.venv/bin/activate
```

2. pythonの場所を調べて，コピーしておく

```zsh
    which python
```

3. juliaのRPEL環境に入る

```zsh
    julia
```

4. pycallをインストールするため，`]`を押して，インストール環境に移行する．その後，次のコマンドを実行してインストール

```julia
    add PyCall
    add Pkg
```

5. 今利用するので，どちらもusingしておく

```julia
    using PyCall
    using Pkg
```

6. pythonの仮想環境のパスを通す．この時，先ほどコピーしたパスを貼り付ける

```julia
    ENV["PYTHON"] = "先ほどコピーしたパス"
```

7. 環境を再構築する

```julia
    Pkg.build("PyCall")
```

これで実行環境ができる
