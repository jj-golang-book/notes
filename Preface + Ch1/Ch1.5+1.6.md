### Fetching a URL
- 對幾乎所有 program 來說，連上網都是滿重要的應用
- Go std pkg, net 提供了許多這領域的功能
- 基本上就是實作 curl

---

用 goroutine 實作 concurrent fetch

- goroutine -> 並行執行的機制
- channel -> 同時控制 routine 的運行先後順序，和 batch 傳遞的時機。同時理論上應該還會負責 routine 間的溝通
	- 雖然後來看起來，不同的 routine 是可以 share 一些 var 來用的，但只會導致 race cond 來產 bug
	- 可以用 channel 來在 GR 間傳遞 val
	- main 其實也是一個 GR, 所以我們用 go 產生的 GR, 會傳遞值給 main GR
	- GR 這樣寫也是 non-blocking 的，的確會用並行的方式來執行
	- 滿有趣的, fmt.Println(<-ch) 這個才是所有 go routine 的觸發點