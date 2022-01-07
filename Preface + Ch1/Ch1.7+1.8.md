### A Web Server
- & 應該是用來跑在 background 的
- http.HandleFunc("/", handler), http.HandleFunc("/count", counter)
- 彼此之間只有一個會被執行到
- 總之 handle func 有些可能是用 GR 或其他方式執行的，並且每個 handler call 可能都會執行一段時間，這時候會有其他 handler 在其他人執行過程中被呼叫。
> Ref: https://ithelp.ithome.com.tw/articles/10203923?sc=rss.iron 這個人文章其實滿有趣的，雖然看起來都是入門操作，卻講概念跟發現問題很順暢。
- 這時候就有可能，會有一些 value snapshot 被產生，導致有些變數被更新了比較少次。
- mu.Lock (# Mutual exclusion lock (mutex)) 這個在其他語言有點少見，滿有趣的
- nbytes, err := io.Copy(ioutil.Discard, resp.Body) 這個其實我沒有到很看得懂為何一定要這樣寫，沒有直接 parse 

### Loose Ends
- control flow
	- switch-case
		- no fallback, 不需要使用 break statement (跟其他語言都不太一樣)
		- 有個 `fallthrough` 可以覆蓋這個行為
		- tagless switch, 其他語言應該有看過了
	- goto
		- 通常用在自動生成的程式碼比較多
	- type, 用來定義型別
	- pointers
		- golang 是有 pointer 的
		- 在 C 裏面，pointer 能控制的東西很多，但也很常把整個 program 寫壞
		- 其他語言可能會用 reference 來包裝 pointer 本身的功能，但也完全侷限了他的用途，只能用來傳遞
		- go 站在一個中間的位置，有具體的 pointer 這個 type, & 可以取得 value type 的 pointer，\* 可以取的 reference type/pointer 的 var value
		- 但沒有 pointer 運算
		- 跟前面討論的類似，那這樣提供 pointer 還真的有很大的用處嗎？
	- method/interface
		- method 是一個被一個特殊型別標記的 func
		- method 可以和幾乎任何型別嵌合
		- interface 就比較像是 duck typing 的東西，會比較像是 fields or methods
	- packages
		- 自稱有很棒的 std lib 和 pkgs（可能跟 C 比
		- std lib 還不錯，生態系目前還無感
	- comments
		- // & /\*\*/ style 都有
		- go doc/godoc 會自動去抓註解變成文件