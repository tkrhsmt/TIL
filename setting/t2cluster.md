# t2clusterの使用方法

## Incompact3dの設定

Incompact3dを使用するには，`cmake`と`openmpi`を新たにインストールする必要がある．
`gfortran`はすでにインストールされているものでバージョンが足りていたので，それを使えば良い．

### `cmake`のインストール方法

1. `cmake`の事前ビルド済み`.sh`ファイルを取得

    ```bash
    mkdir $HOME/.cmake
    cd $HOME/.cmake
    wget https://github.com/Kitware/CMake/releases/download/v3.20.5/cmake-3.20.5-linux-x86_64.sh
    ```

2. `.sh`ファイルを実行

    ```bash
    mkdir -p $HOME/.local/cmake
    bash cmake-3.20.5-linux-x86_64.sh --skip-license --prefix=$HOME/.local/cmake
    ```

3. パスを通す

    ```bash
    echo 'export PATH=$HOME/.local/cmake/bin:$PATH' >> ~/.bashrc
    ```

### `openmpi`のインストール方法

`openmpi`の場合は，`gcc`ベースでビルドする必要がある．

1. ファイルを取得して展開

    ```bash
    mkdir -p $HOME/build_openmpi && cd $HOME/build_openmpi
    wget https://download.open-mpi.org/release/open-mpi/v4.1/openmpi-4.1.6.tar.gz
    tar -xzf openmpi-4.1.6.tar.gz
    cd openmpi-4.1.6
    ```

2. インストール

    ```bash
    ./configure --prefix=$HOME/.local/openmpi-gcc CC=gcc CXX=g++ FC=gfortran
    make -j$(nproc)
    make install
    ```

3. `bashrc`にパスを追加

    ```bash
    nano $HOME/.bashrc
    ```

    一番下に行き，以下を追加

    ```bash
    export PATH=$HOME/.local/openmpi-gcc/bin:$PATH
    export LD_LIBRARY_PATH=$HOME/.local/openmpi-gcc/lib:$HOME/.local/openmpi-gcc/lib64:$LD_LIBRARY_PATH
    ```

これで設定は完了．あとは`xcompact3d/using_guide.md`に従ってインストールすれば良い．
