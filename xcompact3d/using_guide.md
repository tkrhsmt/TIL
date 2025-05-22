# Incompact3d の使い方

## 最初のコンパイル

これは，最初だけにやれば良いコンパイル．2回目以降は飛ばして良い．

1. ターミナルを開き，Incompact3dを置きたい場所に移動する．

    ```
    cd Incompact3dを置きたい場所
    ```

   cloneを行なって，Incompact3dを実行環境内に構築する．

   ```zsh
    git clone https://github.com/xcompact3d/Incompact3d
   ```

2. 次に，環境変数 FCを設定する

    ```zsh
    export FC=mpif90
    ```

    `mpif90`は設定するコンパイラ

3. cmakeで，ビルドを行う

    ```zsh
    cmake -S . -B build
    ```

    cmakeというものを利用して，自動でビルド環境を作る．ビルドとは，今回の場合fortranのファイル.f90を実行前段階にコンパイルしておくことを指す．どの順番でビルドしておくかが「CMakeList.txt」に書いてあり，これを実行すれば良い．<br><br>

    - `-S ソースディレクトリ` :「CMakeLists.txt」が置いてあるディレクトリを指定
    - `-B ビルドするディレクトリ` : ビルドするディレクトリを指定．ディレクトリが存在しない場合は自動で作ってくれる

    <br>
    上の場合では，現在のディレクトリに存在する「CMakeLists.txt」を使って，「build」というディレクトリ内にビルドする，ということを指す<br><br>

4. cmakeで，コンパイルを行う

    まずbuildディレクトリに移動

    ```zsh
    cd build
    ```

    その後，buildディレクトリ内のMakefileを利用して，コンパイルを行う．

    ```zsh
    cmake --build . -j 8
    ```

    コンパイルとは，実際に実行するファイルを作成することを指す．
    - `--build ソースディレクトリ` : 先にやったcmakeによるビルドで，自動生成された「Makefile」を利用して，実行ファイルを作成する．ソースディレクトリには，Makefileが存在するディレクトリを指定する．実行ファイルはbinフォルダ内に作られる．
    - `-j 並列数` : 実行ファイルを生成するときに，並列化する並列数を指定する．

    <br>

5. runフォルダを作っておく
   今後，実行したものを保存しておくフォルダを作っておく．まず初期位置に戻る．

   ```zsh
   cd ../../
   ```

   次に，runフォルダを作成．

   ```zsh
   mkdir run
   ```

   もしくはfinderでIncompact3dディレクトリに移動し，`command+shift+N`でファイルを作る

## テストの実行

自分で実行する環境を作りたい場合は，このセクションは飛ばして良い．

1. run環境に移動する

   上の5.で作成したrunディレクトリに移動

   ```zsh
   cd run
   ```

2. テストexampleをコピーする

    ```zsh
    cp -r ../examples/TGV-Taylor-Green-vortex ./TGV
    ```

    このとき，目的の内容に変えておく．今回は「TGV-Taylor-Green-vortex」というテスト項目を「TGV」という名前で保存した．他にも，

    - ABL-Atmospheric-Boundary-Layer
    - Cavity
    - Channel
    - Cylinder-wake
    - Gravity-current
    - Mixing-layer
    - OTV-Ozage-Tang-vortex-2D
    - Periodic-hill
    - Pipe-Flow
    - Sandbox
    - Sphere
    -
    - TBL-Turbulent-Boundary-Layer
    - Wind-Turbine

    が入っているっぽい．最後の`./TGV`の部分は，`./好きな名前`で大丈夫．

3. コピーした項目に移動

    上でコピーした項目に移動する．自分で決めた名前を入力すること．例えば，先の例と同様に，上で`./TGV`としていた場合，

    ```zsh
    cd TGV
    ```

    と入力する．

4. プログラムを実行

    ここでプログラムを実行する．

    ```zsh
    mpirun -np 8 ../../build/bin/xcompact3d > TGV.log
    ```

    - `mpirun` : OpenMPを使って，並列計算を実施しながらプログラムを実行する
    - `-np 8` : 並列数8でプログラムを実施．
    - `../../build/bin/xcompact3d` : 上のセクションで実施した`xcompact3d`の場所
    - `> TGV.log` : 出力のログは`TGV.log`に書く．プログラム実行中に随時書き換えられる．

    入力ファイルとして， `input.i3d`を使用する．(どうやら何を入力してもinput.i3dが読み込まれるらしい)

    実行したいプログラムに対して，それぞれ項目を書き換えること．

これで実行完了です．

## 実行中の操作について

実行中のログの見方は，

```zsh
less TGV.log
```

などで`less`コマンドを使用してみる．更新を確認するには，

```
shift + F
```

を押すことで，更新を常に確認できる．

## ファイルで使用される変数について

`Case-TGV.f90`でのファイルについて，以下の変数が使用されています．

- `mytype` : decomp2d (FFT) 側で決められている型 倍精度か単精度であることを示す．多くの場合，計算では倍精度，出力では単精度である．
- `nx, ny, nz` : x方向の分割数, y方向の分割数, z方向の分割数
- `ux, uy, uz` : サブルーチン内でのみ使用される3次元変数．速度場を表す
- `ux1, uy1, uz1` : 格納されている速度場変数(3次元)
- `ux2, uy2, uz2` : `ux1, uy1, uz1`の転置(x,y)
- `ux3, uy3, uz3` : `ux2, uy2, uz2`の転置(y,z)
- `ta1,td1,tg1` : 速度`ux1`の，それぞれ(x,y,z)方向1階微分 $\frac{\partial u}{\partial x},\frac{\partial u}{\partial y},\frac{\partial u}{\partial z}$
- `tb1,te1,th1` : 速度`ux2`の，それぞれ(x,y,z)方向1階微分 $\frac{\partial v}{\partial x},\frac{\partial v}{\partial y},\frac{\partial v}{\partial z}$
- `tc1,tf1,ti1` : 速度`ux3`の，それぞれ(x,y,z)方向1階微分 $\frac{\partial w}{\partial x},\frac{\partial w}{\partial y},\frac{\partial w}{\partial z}$
- `enst` : 空間平均エンストロフィー
- `eps` : 空間平均エネルギー散逸率
- `eek` : 空間平均乱流運動エネルギー
- `ta1,td1,tg1` : 速度`ux1`の，それぞれ(x,y,z)方向2階微分 $\frac{\partial^2 u}{\partial x^2},\frac{\partial^2 u}{\partial y^2},\frac{\partial^2 u}{\partial z^2}$
- `tb1,te1,th1` : 速度`ux2`の，それぞれ(x,y,z)方向2階微分 $\frac{\partial^2 v}{\partial x^2},\frac{\partial^2 v}{\partial y^2},\frac{\partial^2 v}{\partial z^2}$
- `tc1,tf1,ti1` : 速度`ux3`の，それぞれ(x,y,z)方向2階微分 $\frac{\partial^2 w}{\partial x^2},\frac{\partial^2 w}{\partial y^2},\frac{\partial^2 w}{\partial z^2}$
- `eps2` : 2次導関数から求めた空間平均エネルギー散逸率

関数について

- `derx(tx,ux,rx,sx,ffx,fsx,fwx,nx,ny,nz,npaire,lind)` : `ux`の x方向1階微分をtxに格納する
- `derxx(tx,ux,rx,sx,sfx,ssx,swx,nx,ny,nz,npaire,lind)` : `ux`の x方向2階微分をtxに格納する
- 同様にderyやderyy，derz，derzzもある
- `momentum_rhs_eq(dux1,duy1,duz1,rho1,ux1,uy1,uz1,ep1,phi1,divu3)` : 最初の予測因子の計算を表す項．さまざまな追加条件を入れられるが，最も単純な場合は，それぞれ以下の式になる．
  - `dux1` : $ \nu\frac{\partial^2u}{\partial x_jx_j} - u_j\frac{\partial u}{\partial x_j} $
  - `duy1` : $ \nu\frac{\partial^2v}{\partial x_jx_j} - u_j\frac{\partial v}{\partial x_j} $
  - `duz1` : $ \nu\frac{\partial^2w}{\partial x_jx_j} - u_j\frac{\partial w}{\partial x_j} $
- `momentum_forcing(dux1, duy1, duz1, rho1, ux1, uy1, uz1, phi1)` : 外力項

## 微分操作について

6次精度compact差分を使用している．

### 周期境界のとき

$$
  \alpha\frac{\partial f_{i-1}}{\partial x} + \frac{\partial f_i}{\partial x} + \alpha \frac{\partial f_{i+1}}{\partial x} = a \frac{f_{i+1} - f_{i-1}}{2\Delta x} + b \frac{f_{i+2} - f_{i-2}}{4\Delta x}
$$

6次精度compact差分のとき
- $\alpha = \frac{1}{3}$
- $a = \frac{14}{9}$
- $b = \frac{1}{9}$

境界部も同様の式を用いることができる．（$i=1$のときは，$i-1$に$n$を入れる）

### 壁境界のとき

境界部以外は上記と同じ式が用いれる．
境界最外層部では
$$
  \frac{\partial f_{1}}{\partial x} + 2\frac{\partial f_2}{\partial x} = \frac{-5 f_1 + 4 f_2 + f_3}{2\Delta x}
$$
$$
  \frac{\partial f_{n}}{\partial x} + 2\frac{\partial f_{n-1}}{\partial x} = \frac{5 f_n - 4 f_{n-1} - f_{n-2}}{2\Delta x}
$$
を用いる．
もう一つ内側では，もう少し精度よく
$$
  \frac{1}{4}\frac{\partial f_{1}}{\partial x} + \frac{\partial f_{2}}{\partial x} + \frac{1}{4}\frac{\partial f_{3}}{\partial x} = \frac{3}{2}\frac{f_3 - f_1}{2\Delta x}
$$
$$
  \frac{1}{4}\frac{\partial f_{n-2}}{\partial x} + \frac{\partial f_{n-1}}{\partial x} + \frac{1}{4}\frac{\partial f_{n}}{\partial x} = \frac{3}{2}\frac{f_n - f_{n-1}}{2\Delta x}
$$
を用いる．

## ファイル中身について

それぞれ`.f90`で記述されているファイルについて，以下の内容が書かれているようです．

- `~.i3d`
  - 初期条件が記述されたファイル．物体境界以外の情報はここから得られる．

- `xcompact3d.f90`
  - メイン処理を行うファイル．Navier-Stokes方程式の時間進行，および後処理・チェックポイントの手順が実行される．
  - コードの初期化・コードの終了に関するサブルーチンも含まれる

- `Case-TGV.f90`
  - テイラーグリーン渦の設定が書かれている
  - 出力される値は，順に`eek,eps,eps2,enst`の4つ．

- `les_models.f90`
  - `jles`の値で，LESのモードを切り替える
    - 1 : 標準Smagorinskyモデル
    - 2 : WALE SGSモデル
    - 3 : Dynamic Smagorinskyモデル
    - 4 : Implicit LES (数値粘性)モデル

## 新しいcaseの追加方法

caseを新しく作るには，`**.f90`のファイルを作成し，記述する必要がある．

1. caseファイルの中には，必ず次の事項を入れる

- `init_**`
- `boundary_conditions_**`
- `postprocess_**`
- `visu_**_init`
- `visu_**`

  さらに，外力を加える場合には`momentum_forcing_**`などを加えれば良い．

2. 次のファイルを全て書き加える(境界埋め込み法を使わない場合のみ)

- `navier.f90` : inflowやoutflowの設定
- `case.f90` :次の事項を記述する
  - use **
  - momentum_forcing 外部強制力がある場合
  - init initの追加
  - boundary_conditions boundary_conditionsの追加
  - postprocess postprocessの追加
  - visu_case_init visu_**_initの追加
  - visu_case visu_**の追加
- `module_param.f90` : itype_** の追加
- `parameters` : print文の追加
- `CMakeLists.txt` : **.f90を追加(コンパイル時に必要)

この他，さらに追加の設定が必要な場合には書き加える箇所も多くなる．

## Incompact3d_toolboxを利用したポスト処理

Incompact3d toolboxを利用するには，以下の手順を踏む．
まずpythonがインストールされている前提とする．
インストールしていない場合は，調べればすぐに出てくるので，そのコマンドを打ってインストールする．
以下では，pythonをインストールした後の手順である．

1. Incompact3d_toolboxを置きたい場所へターミナルで移動する
2. Incompact3d_toolboxをクローンする

  ```zsh
  git clone https://github.com/fschuch/xcompact3d_toolbox.git
  ```

3. xcompact3d_toolboxのフォルダへ移動

  ```zsh
  cd xcompact3d_toolbox
  ```

4. xcompact3d_toolboxをインストール

  ```zsh
  pip install -e .
  ```

インストールが終了すれば，使えるようになる．

---

このパッケージをjuliaで使用する場合には，以下の方法を用いる．

1. ターミナルで，pythonの位置を確認する．

```zsh
  which python
```

これをコピーしておくこと．

2. ターミナルでjuliaの対話モードに入る

```zsh
  julia
```

とターミナルに入力．

3. PyCallをインストールする．

```julia
  ]
```

でパッケージのモードに入り，

```julia
  add PyCall
```

を実行し，インストールする．

4. 環境を設定する．

パッケージモードを抜けて，次のコマンドを入力する．

```julia
  using PyCall
  EENV["PYTHON"] = "コピーした内容を貼り付け"
```

コピーした内容を貼り付け，の部分に`which python` で取得したものを入れる．
この後，

```julia
  using Pkg
  Pkg.build("PyCall")
```

を入力して，再度PyCallを更新する．
これで使用可能となるはずである．

---

実際にコマンドを利用するには，まずターミナルでデータが置いてあるフォルダに行き，次の手順で取得する．

1. まず，パッケージを取得する．

```julia
  using PyCall
  x3d = pyimport("xcompact3d_toolbox")
```

2. パラメータを取得する

```julia
  prm = x3d.Parameters()
  prm = x3d.Parameters(filename="input.i3d")
  prm.load(raise_warning=true)
```

`input.i3d`の中に，パッケージが想定していない定数が定義されているときにエラーを吐くので，`raise_warning=true`が必要．
2行目と3行目を一発で処理するコマンドもあるが，なぜか読み込めないので上記のように書く．

3. 物理パラメータを読み込む

```julia
  ux = prm.dataset.load_array("data/ux-1.bin")
```

これでPyObject型のデータが受け取れる．もし，速度の配列だけが欲しい時は，

```julia
  ux = prm.dataset.load_array("data/ux-1.bin").data
```

とすれば良い．

4. 物理パラメータの出力

物理パラメータの出力は，入力形式と同じxdmf形式とできる．
出力には，追加でxarrayパッケージが必要．

```julia
  xr = pyimport("xarray")
```

現在持っている配列がjulia形式の場合，変換が必要．
もしxyzの3次元配列であれば，時間tを含めた4次元配列に変換する必要がある．

```julia
  ux_b = zeros(prm.nx, prm.ny, prm.nz, 1)
  ux_b[:, :, :, 1] = ux
```

さらに，これをxarray形式へ変換する．

```julia
  ux_out = xr.DataArray(ux_b, dims=PyObject(("x", "y", "z", "t")))
```

prmとの重複を避けるため，新たに，パラメータを設定する．

```julia
  prm2 = x3d.Parameters(
        nx=prm.nx,
        ny=prm.ny,
        nz=prm.nz,
        nclx1 = prm.nclx1,
        nclxn = prm.nclxn,
        ncly1 = prm.ncly1,
        nclyn = prm.nclyn,
        nclz1 = prm.nclz1,
        nclzn = prm.nclzn
        )
```

出力するものを，datasetへ渡す．

```julia
  prm2.dataset.write(data=ux_out, file_prefix="ux")
```

この時，`file_prefix`がparaview上での変数名となる．

最後に，これを書き出す．

```julia
  prm2.dataset.write_xdmf("output.xdmf")
```
