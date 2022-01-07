### GIFs
- Lissajous figures
- Parametric curves
- Harmonic oscillation

---
- 可以用 image/color 來指定只 import 某個子 package
- const 可以宣告在檔案的最高層(其實 var 也可)
- 滿怪的是，用 var vs const 來宣告 index, var 會有 error
https://go101.org/article/constants-and-variables.html 好像跟有些 untyped 的決定機制有關，不確定為何
- 看起來 var 預設會給他 int，const 則是會預設變成 untyped int，然後 untyped int 似乎是可以自動相容 uint8 的，但 var 的 int 不行，要強制的寫成 uint8 or untyped int

---

```go
var palette = []color.Color{color.White, color.Black}
```

Palette 是一個 slice，其中每個元件都該符合 color.Color 這個 interface type, which 必須滿足至少有 RGBA() 這個 method 實作就好。

進入 color.White, 發現他是一個 Gray16 struct 的衍生物。
而 Gray16 這個 struct，除了有 Y 這個 field, 同時還有這個 method 幫它提供了對應的呼叫方式。所以他跟上述 interface 相容。

```go
func (c Gray16) RGBA() (r, g, b, a uint32) {
	y := uint32(c.Y)
	return y, y, y, 0xffff
}
```

gif.GIF 就相對好解釋多了，他是一個 struct, 可以給一些預設值來產類似 object 出來。
而 golang 裏面，給預設值可以按照 struct 定義 field 的順序依序給，也可以指定某個 key, 例子裏是指設定 LoopCount。

沒有很想多花時間去理解一些三角函數的操作，實作方面就略過。