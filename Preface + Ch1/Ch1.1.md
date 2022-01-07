### Hello, World
- Golang, compiled lang
- go run/go build, like gcc
- go run 是直接執行，go build 可以產生 binary
```sh
module example.com/hello #這個 project root 的名稱

go 1.15 #使用的 go 版本

#有點像是 package-json.dependencies，只是定義使用的工具和版本
require (
	golang.org/x/text v0.3.7 // indirect
	rsc.io/sampler v1.99.99 // indirect
)
#不太確定這裡 indirect 為什麼會出現
#他是說就連你的 main module 都沒用到它
```
- 你想取得程式碼不該用 go get，應該用 git clone 之類的，因為 go get 會安裝到 ~/go 裏面 (or $GOPATH)
---

- 每個 program 開始，都要先做 package 宣告
- fmt 裏面有的 print func 數量多到其實有點煩，但總之 Println 會有 new line, 並支援 params 和 inject var
```go
func Errorf(format string, a ...interface{}) error
func Fprint(w io.Writer, a ...interface{}) (n int, err error)
func Fprintf(w io.Writer, format string, a ...interface{}) (n int, err error)
func Fprintln(w io.Writer, a ...interface{}) (n int, err error)
func Fscan(r io.Reader, a ...interface{}) (n int, err error)
func Fscanf(r io.Reader, format string, a ...interface{}) (n int, err error)
func Fscanln(r io.Reader, a ...interface{}) (n int, err error)
func Print(a ...interface{}) (n int, err error)
func Printf(format string, a ...interface{}) (n int, err error)
func Println(a ...interface{}) (n int, err error)
func Scan(a ...interface{}) (n int, err error)
func Scanf(format string, a ...interface{}) (n int, err error)
func Scanln(a ...interface{}) (n int, err error)
func Sprint(a ...interface{}) string
func Sprintf(format string, a ...interface{}) string
func Sprintln(a ...interface{}) string
func Sscan(str string, a ...interface{}) (n int, err error)
func Sscanf(str string, format string, a ...interface{}) (n int, err error)
func Sscanln(str string, a ...interface{}) (n int, err error)
```
- 有碰到再來探討，不確定哪些比較常用 https://pkg.go.dev/fmt#pkg-index
- package main & func main 都是特別的，是 golang 保留來給執行檔呼叫的機制
- 有多餘的 deps 會 build fail
- import 必須緊接在 package 後面
---
- 只有 func fName () 這段是必須的，param list, return type 都是 optional
- golang 的分號，只有在 inline 寫兩個 statement 才會用到
- newline 在 golang 是有意義的（js 幾乎無）
- gofmt 就是 yyds，不存在專案客製化的 coding style
	- 讓自動產生 code 的工具比較好開發一點，像是假設升級某個 package, 要把使用該 package 的所有參數都重命名/轉型別，越 static 的語法越容易做到這件事情
	- 但其實也有點難說這件事具體來說到底好不好做，畢竟所有語言都有他動態的部分
- 通常你的編輯器都該內建 gofmt 和 goimports

---

1. 使用 go run, go build 和 makefile 的具體差別？
	1. 看起來 Makefile 會設定比較多 OS/build instruction 類的考量
	2. 以及基本的 setup 和 deps debug entry
	3. 不同類型的 test, 像是 CI, dry, cold
	4. 也可能會有 livereload 模式的 server, 或是包好 docker 運行的版本
	5. 安裝/部分安裝 deps 可能也有指令
	6. 安裝 git hooks
	7. 分別的 linter 指令
	8. 產生 mock data/db migration/seed/doc
	9. build 和 build race
	10. 測試全跑/只跑單個測試
	11. clean 環境 (docker, artifacts) 類的指令
2. go lang 的 runtime 和 binaries 怎麼編譯？以及真的有 std lib/custom lib 要用到底層操作怎麼辦
	1. 編譯成中介語言，會有對應平台的 instruction set
	2. 沒有類似 vm 的 runtime, 基本上就是 binary code
	3. golang 語言/語法本身把底層(mem 之類的)和平台專屬的操作藏得很死，沒有多少 syntax 可以直接調用到
	4. 但 cgo, 和用組語 or C 來實作 empty go func 是有支援的，std lib 通常會這樣做