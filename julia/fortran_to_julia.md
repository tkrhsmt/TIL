# fortranでjulia関数を利用する方法


fortranでjuliaを利用するには，「fortran -> python -> julia」でアクセスするのが簡単

## 利用方法

1. pyfortのクローン

    [pyfortのページ](https://github.com/ylikx/forpy)からクローンする．

    ```zsh
    gh repo clone ylikx/forpy
    ```

    クローンした内容のうち，利用するディレクトリに`forpy_mod.F90`をコピーする

2. pythonの経由関数を書く．引数は`arg`のタプル型，あるいは辞書型が可能だが，ほとんどタプル型で良いはず．ファイル名は`python_module.py`など．

    ```python
    import numpy as np

    def fotran_to_julia(*args):

        from julia import Main
        Main.include("test.jl")

        ret = Main.julia_func(args)

        return float(ret)
    ```

3. julia関数を書く．ここでは`test.jl`として保存．

    ```julia
    function julia_func(x)
        return 2*sum(x)
    end
    ```

4. fortranにpythonへアクセスするコードを書く

    ```fortran
    subroutine test2()
        !これを必ず記述する
        use forpy_mod
        implicit none

        !pythonのファイル名がここに入る
        type(module_py) :: python_module
        type(list) :: paths

        !例題用の変数
        integer, parameter :: NROWS = 2
        integer, parameter :: NCOLS = 3
        integer :: ierror, ii, jj
        integer :: tmp

        !タプルや戻り値を設定
        type(tuple) :: tup
        type(object) :: return_value
        real :: return_real

        !必須．初期化関数
        ierror = forpy_initialize()
        ierror = get_sys_path(paths)
        ierror = paths%append(".")

        !タプルの作成．第二引数へタプルの個数を入れる
        ierror = tuple_create(tup, NROWS * NCOLS)
        !python関数の定義
        ierror = import_py(python_module, "fotran_to_julia")

        do jj = 1, NCOLS
            do ii = 1, NROWS
                tmp = (ii - 1) + (jj - 1) * NROWS
                ierror = tup%setitem(tmp, tmp)
            enddo
        enddo

        !python関数の実行．第一引数に戻り値，第二引数にモジュール名，第三引数にpython関数名，第四引数にタプル
        ierror = call_py(return_value, python_module, "fotran_to_julia", tup)

        !cast関数で，fortranの型に変換
        ierror = cast(return_real, return_value)

        write(*,*) return_real

        !最終処理
        call forpy_finalize
    end

    program intro_to_forpy
        call test2()
    end program
    ```

5. 実行する．

    まず `forpy_mod.F90` をコンパイル
    ```zsh
    gfortran -c forpy_mod.F90
    ```

    次に，fortranの書いたコード`intro_to_forpy.F90`をコンパイル

    ```zsh
    gfortran intro_to_forpy.F90 forpy_mod.o `python3-config --ldflags --embed`
    ```

    このときの引数が重要らしい
