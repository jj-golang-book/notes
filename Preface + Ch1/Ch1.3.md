### Finding Dup Lines
- 給一群 input 到截止，經過計算後，給出有多次出現的 str/num
```go
func main() {
	counts := make(map[string]int)
	input := bufio.NewScanner(os.Stdin)
	for input.Scan() {
		counts[input.Text()]++
	}
	// NOTE: ignoring potential errors from input.Err()
	for line, n := range counts {
		if n > 1 {
			fmt.Printf("%d\t%s\n", n, line)
		}
	}
}
```
- if 也不用括號
- map 比較是一個單純的物件，跟 struct/interface 無關，像是 dict
- key 可以是 string/num (可以用 == 來比的)
- 關鍵字 make 可以來創建不少東西，map 是其一（還有 slice, chan, ...）
- 就算 counts[k]++ 本身還沒值也沒關係，本身 `map[string]int` 
- golang 刻意把 map range access 變成隨機的，不讓你亂玩
- NewScanner 沒什麼好講的，重點是他會讀你程式跑途中的 stdin，只會在呼叫 input.Scan() 時開始聽
- 輸入一堆數字後，要 ctrl + D 才能夠 end of input（stdin 的運作方式
- %d, %f 之類的叫做 verb
	- 取名沒什麼邏輯，不用背，八成從 C 來的

---

看起來 NewScanner 也可以直接讀檔案
- 不過我看不太懂，os.File 跟 NewScanner 跟 param type 怎麼互通
- 大概顯示了 golang 的 error flow，回直接給一個 return value
- 不確定 f.Close() 確切為什麼會存在，也許是因為這是 os package 用 binary 實作的 func 之一，不會有 gc 存在，所以會需要類似這樣的操作來 free mem
- golang func declare 可以在使用的前面 or 後面（不像是 JS 一樣鳥

---
- Golang 有一些關於 pointer 的實作細節，但高層次的似乎可以把任何種類的 var，都分成 reference type or value type
- https://stackoverflow.com/questions/40680981/are-maps-passed-by-value-or-by-reference-in-go
- 總之 map 在傳進 func 的行為是 reference type，所以會改到原先的內容
- struct 不會，會被當成 value type
	- https://stackoverflow.com/questions/59041891/in-go-is-a-structure-pointer-not-a-reference-type
	- 但實際上許多 blog 會探討「There Are No Reference Types in Go」，不過都有點太仔細了，有機會再看
	- 也許跟 JS call by sharing 之類的語言設計有點像

---
- 通常不會直接使用 os.File，bufio, io/ioutil 是一些包的更好的 lib