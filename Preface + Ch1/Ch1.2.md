### Cli Arguments
- programs 基本上都有 input/output
	- 最經典的範例，command line arguments
- os package，基本 OS 操作，有些平台專屬的東西
- os.Agrs, type slice
	- slice 可以被 `slice[i]` 來取值
	- 有點像是 python, 取一段的語法是 `slice[m:n]`
	- `len(slice)` 是內建 func, 取長度
	- m & n 是 half-open interval 會取到從 m 開始（包含 m），但 n 之前（不包含 n）的所有物品
	- 另一個記法，`slice[n:len(slice)]` 可以直接取得 n 到最後一項，因為最後一項的 index 應該是 `len(slice) -1`
	- os.Agrs[0] 是指令名稱
	- m 預設值是 0（開頭），n 預設值是 len(s) （結尾）
- ![[Pasted image 20220107040506.png]]
	- 通常會用 import () 括弧版本
	- gofmt 會自動 sort import to abc order
	- golang 我稍微沒很喜歡的地方是，變數宣告的方式有點太多了
	- `var s, sep string`
		- 宣告兩個變數，只寫最後一個 type 代表同時幫兩者定義型別 (& 因為有型別，所以會有初始值)
		- 如果想 inline 宣告兩個不同 type 的 var, `var s number, sep string` 卻是違法的
			- 但你可以使用 `:=` `c, d, e := 1, 2, "whatever"`
			- 或是 `var i, s = 2, "hi"`
			- 歪魔邪道 `var (i int = 2; s string = "hi")`
			- 只是想要單個 expression，也可以
```go
var (
    n []int
    m string
    v reflect.Value
)

var (
   a = 12
   b = 3
   enableFeatureA = false

   foo = "bar"
   myvar float64
   anothervar float64 = 2.4
)
```

---
- + 可以 concat string, 是內建操作，不是 type conversion
- 有 (+-*/)=

---

### for loop
- golang 裏面只有 for loop
- 沒有 while, for while 之類的鬼東西
- 也沒有 forEach()
	- 但不是不給你用，而是都內建在 for 的語法裡

- 沒有 parentheses
- for **init**; **cond**; **post** {
}
- 三個都是 optional 的
- init 通常都只是用來宣告和初始化 var, 也可以是 func call
- 只有一個參數會被當成 **cond**，是正常的 while loop，不用分號
- 完全沒參數，是 infi loop
- 只有兩個參數，那就是 init + cond, 兩個 param 都要寫分號
	- 我覺得沒什麼邏輯，為什麼三個參數就只需要寫兩個分號
	- 但也許是兩個參數基本上大家不會這樣寫，但總之這樣是會動的
```go
for i := 1; i < len(os.Args); {
	s += sep + os.Args[i]
	sep = " "
	i = i + 1
}
```

---

- 還有所謂的 for range loop，不過基本上是不同東西了，只需要一個 statement 就能運行
```go
strings := []string{"hello", "world"}
for index, str := range strings {
    fmt.Println(index, str)
}
```

- 有所謂的 blank identifier，就是底線參數
	- 各個語言的用途不同，但通常是用來跨過 linter, 或是 compiler 不允許 unused var, golang 就是這樣
- 在有 function overloading 的語言裡，以及用 arity 來決定是否是同 func，像是 Elixir，除了拿來被忽略，還會用來填充成某個 arity

---

有點煩人的變數宣告方式

```go
s := ""
var s string
var s = ""
var s string = ""
```

- 總之核心概念是，golang 通常不用 redundant 的寫法，所以 (4.) 不會去寫。
- (1.) 先不考慮，畢竟他是 func 內特殊寫法
- (2.) 會比 (3.) 常用，但他說除非 define 多個 var 才會特別這樣寫，沒什麼道理。不過也許是假設要用初始值，都會單純寫 type > init value 吧 
- strings.Join() 可以被 slice join 成 str
- 