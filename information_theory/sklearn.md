# sklearnを利用した情報量の計算

## julia上でsklearnを利用する方法

julia上でsklearnを利用するには，pycallのパスが通っているpython環境でsklearnをインストールすれば良い．

```zsh
source ~/.venv/bin/activate
pip install scikit-learn
```

これで使用可能になる．
julia上で利用するには，

```julia
using PyCall
sk = pyimport("sklearn")
```

とすれば良い．

## カーネル近傍法

sklearnでは，カーネル近傍法を利用して，確率密度関数を推定することができる．

1. まず，パッケージを読み込む

    ```julia
    using PyCall
    sk = pyimport("sklearn.neighbors")
    ```

2. データセットを用意する．今回は正規分布にフィッティングされた乱数を用いる

    ```julia
    data = randn(1000)
    ```

3. データセットは次元を持っていて良い．この次元を2次元配列へ格納する

    ```julia
    dataset = zeros(length(data), 1)
    dataset[:, 1] = data
    ```
    この時，列側の数は，データセットの次元である．
    今回は1次元のデータセットなので，ここは1になっている．
    同時確率分布を求める際には，2以上の値となるはず．

4. カーネルを作成する．

    ```julia
    kde = sk.KernelDensity(kernel="gaussian", bandwidth=0.5).fit(dataset)
    ```

5. プロット幅を定義する．今回は，-2から2の間で0.01刻みとした

    ```julia
    x1 = [-2:0.01:2;]
    ```

6. これも次元を2次元配列へ揃える

    ```julia
    x = zeros(length(x1), 1)
    x[:, 1] = x1
    ```

7. サンプルを取得する

    ```julia
    log_density = kde.score_samples(x)
    ```

8. 取得したサンプルは対数スケールになっているので，これを戻す

    ```julia
    density = exp.(log_density)
    ```

この手順によって，確率密度関数をゲットできる．

## 相互情報量の計算

カーネル密度推定を利用して，計算する

1. まず，パッケージを読み込む

    ```julia
    using PyCall
    sk = pyimport("sklearn.feature_selection")
    ```

2. データセットを用意する．今回は乱数を与える．

    ```julia
    x = rand(1000)
    y = rand(1000)
    ```

3. データセット`x`は，2次元の必要があるので，変更する．

    ```julia
    X = zeros(length(x), 1)
    X[:,1] = x
    ```

4. 相互情報量の推定を行う

    ```julia
    mi = sk.mutual_info_regression(X,y)
    ```

この手順によって，相互情報量を計算できる．
sklearnでは，KSG estimetorを利用することはできないらしい．
