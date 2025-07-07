# typstにおける数式のhack集

## `underbrace`の位置を自分で調整する関数

`underbrace`の代わりに`ubmanual`を使用する

### 関数

以下をコピーして貼り付ける．
cetzのimportが必要

```
#import "@preview/cetz:0.4.0"

#let ubmanual(it, body, width: auto, height: auto, dx: 0pt, block: true, contents-dx: 0pt, contents-dy: 2em) = context {
  let size = measure(math.equation(it, block: block))

  let xout = width
  let yout = height

  if width == auto{
    xout = size.width
  }
  else{
    xout = size.width + width
  }

  if height == auto{
    yout = size.height / 2
  }

  it

  place(
  cetz.canvas({
   cetz.draw.rotate(x: 180deg)
   cetz.decorations.brace((0pt,yout),(xout,yout))
 }, ),

 dx: - xout - dx,
 dy: yout
 )

 let body-width = measure(body)

 place(
  body,
  dx: - xout/2 - dx - body-width.width / 2 + contents-dx,
  dy: contents-dy
 )

}
```

### 使い方

```
$
   ubmanual(integral sin x space　"d"x, =0, height: #(12pt)) + ubmanual(sin x, =0, height: #(12pt))
$
```

- `height`を設定することで，位置を自分で調整できる
