# typstにおける日本語のhack集


## typst内での改行

### `regex`を使う方法

```typst
#let cjkre = regex("([\u3000-\u303F\u3040-\u30FF\u31F0-\u31FF\u3200-\u9FFF\uFF00-\uFFEF][　！”＃＄％＆’（）*+，−．／：；＜＝＞？＠［＼］＾＿｀｛｜｝〜、。￥・]*)[ ]+([\u3000-\u303F\u3040-\u30FF\u31F0-\u31FF\u3200-\u9FFF\uFF00-\uFFEF])[ ]*")
#show cjkre: it => it.text.match(cjkre).captures.sum()
```

- 上記を導入することで，typst内で日本語文を改行したときに，不必要な半角スペースを取り除くことができる
- 改行前の数文字を検出することで，その他の文字に影響を与えにくい
- 改行前の抽出文字が記号のみ（例えば`．`）だと，typst内で半角スペースよりも小さい謎の間隔が生まれるので，記号を含む場合は記号以外の文字から検出する

例
```typst
こんにちは
世界
Hello
World
```

出力
```pdf
導入前：    こんにちは 世界 Hello World
導入後：    こんにちは世界 Hello World
```

### パッケージを使う方法

```typst
#import "@preview/cjk-unbreak:0.1.0": remove-cjk-break-space
#show: remove-cjk-break-space
```


---

## noindentの処理

```typst
#let noindent = context {
  //現在の位置を取得
  let now = here().position().x

  //marginを取得
  let page-set = page.margin
  if page-set == auto{//marginが自動で設定されている場合
    let small-length = calc.min(page.width, page.height)
    page-set = small-length * 2.5/21
  }
  else{//marginが手動設定されている場合は，左側の余白を取得
    page-set = page-set.left
  }

  //first-line-indentの量を取得
  let par-first-indent = par.first-line-indent.amount

  //位置が一番左のとき　かつ　インデント量が0でないとき
  if now == page-set and par-first-indent != 0cm{
    h(- par-first-indent)
  }
}
```

- `first-line-indent`にはいくつかの不具合が含まれているが，これを回避するための手段として「インデントされる前にインデントされる量を下げておく」というものである
- インデント以外の箇所に置かれて下げられないように，左のmarginと現在の位置が同じ箇所でない限り下げられないようにした
- LaTeXにおける`\noindent`と同等の役割を果たす

---

## 数式の空きの問題

```typst
#import "@preview/cjk-unbreak:0.1.0": *
#import "@preview/touying:0.6.1": utils

#let cn-char = "\p{Han}；：！？…—"
#let jp-char = "\p{Hiragana}\p{Katakana}"
#let cjk-char-regex = regex("[" + cn-char + jp-char + "]")

#let ends-with-cjk(it) = (
  it != none
    and (
      (it.has("text") and it.text.ends-with(cjk-char-regex)) or (it.has("body") and ends-with-cjk(it.body))
    )
)

#let start-with-cjk(it) = (
  it != none
    and (
      (it.has("text") and it.text.starts-with(cjk-char-regex)) or (it.has("body") and start-with-cjk(it.body))
    )
)

#let is-text(it) = (
  it != none and it.func() == text
)

#let insert-cjk-math-space(rest) = {
  rest = transform-childs(rest, insert-cjk-math-space)
  if utils.is-sequence(rest) {
    let first = none
    for mid in rest.children {
      // first, mid, third
      if mid.func() == math.equation and is-text(first) and (ends-with-cjk(first)) {
        first
        h(0.25em, weak: true)

      }
      else if (first != none and first.func() == math.equation) and is-text(mid) and (start-with-cjk(mid)) {
        first
        h(0.25em, weak: true)
      }
      else{
        first
      }
      first = mid
    }
    first
  } else {
    rest
  }
}
```

- [Qiitaの記事](https://qiita.com/zr_tex8r/items/a9d82669881d8442b574)の方法ではAdobe Blankを導入する必要があった
- `cjk-unbreak`パッケージの`transform-childs`関数を利用

例

```typst
#show: insert-cjk-math-space

お客様の中に$T_y (p_s dot tau)$芸人はおられませんか．これは

// 四分空きが入らないパターンも試してみる
アレ（$T_y (p_s dot tau)$）、つまり$T_y (p_s dot tau)$。
```

[Qiitaの記事](https://qiita.com/zr_tex8r/items/a9d82669881d8442b574)の例文でうまくいく．
