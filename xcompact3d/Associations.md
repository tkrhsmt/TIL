# Associations.jlの使い方

Associations.jlは，活発に開発が行われているため，内容が変わる可能性あり(2024/8/21)

利用するには，次の宣言をする．

```julia
using Associations
```

## 確率分布を求める

### binを切る

```julia
binning = FixedRectangularBinning(r, dimension(x), precise)
```

ビンを切るサイズを定義する．

- `r` : binの範囲． ex. `r = range(-2, 3; length = 100)` ... -2〜3の範囲で100個切る
- `dimension(x)` : データxの次元
- `precise` : エッジの検出．デフォルトは`false`

### エンコードする

```julia
encoding_x = RectangularBinEncoding(binning, x)
```

### 離散化する

```julia
discretization = CodifyPoints(encoding_x)
```

### 確率分布を求める

```julia
probabilities(discretization, x)
```

これらの関数をまとめると，次のように書ける．

```julia
function prob(x,r)
       binning = FixedRectangularBinning(r, dimension(x), true)
       encoding_x = RectangularBinEncoding(binning, x)
       discretization = CodifyPoints(encoding_x)
       return  probabilities(discretization, x)
end
```


## 確率分布からヒストグラムを作成する

probabilitiesで作成した変数には，`outcomes`，`p`，`dimlabels`が含まれる

- `outcomes` : ビンの位置
- `p` : 確率の値
- `dimlabels` : ??

```julia
function prob_to_hist(r, probabilities)
	n1 = length(r)
	n2 = length(probabilities)
	hist = zeros(n1)
	for i in 1:n2
		j = probabilities.outcomes[1][i]
		if 0 < j && j < n1+1
			hist[j] = probabilities.p[i]
		end
	end

	return hist
end
```

## KL情報量を求める

```julia
est = KLDivergence()
association(est, p1, p2)
```

- `p1, p2` : 上で求めた確率分布を利用する

## 相互情報量を求める

### 確率分布から計算する方法

```julia
est = JointProbabilities(MIShannon(), discretization)
association(est, x, y)
```

- `discretization` : 上の離散化処理を参照

### Gaussian分布類推から求める方法

```julia
est = GaussianMI(MIShannon())
association(est, x, y)
```

### KSG estimatorを使用する方法

```julia
est = KSG1(MIShannon(); k = 5)
association(est, x, y)
```
