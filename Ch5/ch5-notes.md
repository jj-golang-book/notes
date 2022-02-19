The Go Programming Language Ch. 5.6 - 5.10 Notes
================================================

5.6 Anonymous Functions
-----------------------

- 很常見的特性，JavaScript 有，各式 FP 語言也都有
- C++ 有 Lambda Function，但本質上是 function object (透過 operator overloading 實現)
- Lexical scope
  * Closure
  * 因為 closure, 所以 function value 是 reference type
  * 承上，所以 function value 不可比較
  * https://en.wikipedia.org/wiki/Funarg_problem
- Topological sort
  * Directed Acyclic Graph (DAG)
  * http://web.ntnu.edu.tw/~algo/DirectedAcyclicGraph.html#2
- Anonymous function 無法直接 reference 自己
  * 必須先宣告一個 function value 變數再賦值
  * JavaScript 有 named function expression：`(function incr(i) { return incr(i+1); }(0))`

5.6.1 Caveat: Capturing Iteration Variables
-------------------------------------------

- Closure + iteration variables
  * 蠻經典的陷阱
  * https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures#creating_closures_in_loops_a_common_mistake

5.7 Variadic Functions
----------------------

- 可變參數函數
  * C: https://en.cppreference.com/w/c/variadic
  * C++: https://en.cppreference.com/w/cpp/language/parameter_pack
  * JS: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters
  * C 語言在 compile time 是 untyped
  * C++ 使用 variadic templates 時，在 compile time 是 typed
  * Go 同時支援 typed 與 untyped
    - 如果可變參數 type 宣告為 interface{} 就是 untyped
      ```go
      func Printf(format string, a ...interface{}) (n int, err error)
      ```
    - 除此之外可變函數都必須為同一種 type
      ```go
      func sum(vals ...int) int
      ```
- ...interface{}
  * interface{} 就是萬用插座，任何 type 都可以用
  * 但是做任何操作前都要先作 type assertion (Ch 7.10 會講到)

5.8 Deferred Function Calls
---------------------------

- defer: 在 function 即將結束執行前呼叫另一個 function
  * 通常拿來做 resource clean up
  * 當 function 有多個 execution paths 時特別好用
  * 已經發展成一種 idiom, 有 open/connect/lock 就會有下一行 defer close/disconnect/unlock
- 多個 defer statements 會以反向順序執行
- defer function call 執行時 return value 都已準備完畢，甚至可以拿來紀錄 return value
- 要注意一些 long-running function 可能會有需要提早 close 的需求
  * 例如在 for loop 裡讀檔
  * 但其實應該考慮把 function 拆更細
    ```go
    func processSingleFile(filename string) {
        f, _ := os.Open(filename)
        defer f.Close()
        // processs file
    }

    func processFiles(files []string) {
        for _, f := range files {
            processSingleFile(f)
        }
    }
    ```
- 有要檢查 function 回傳值的情況下不適用
  * 例如接 error
  * 解法：anonymous function
    ```go
    defer func() {
        err := something.Close()
        if err != nil {
            // handle error
        }
    }()
    ```

5.9 Panic
---------

- panic 也會去 call deferred function
- regexp.Compile vs MustCompile
  * 前者會回傳 error, 後者會直接 panic
  * 在各種 library 裡蠻常見到這種 idiom
- 如果有任何其他 statement 在 defer statement 之前 panic 的話，defer function 不會被執行到
  ```go
  package main
  import (
      "fmt"
  )
  func main() {
      defer fmt.Println("defer before panic")
      panic("no")
      defer fmt.Println("defer after panic")
  }
  ```

5.10 Recover
------------

- recover() 放在 deferred function 裡
  * 記得 panic 時 go 還會嘗試去執行 deferred function 嗎？
- net/http 裡的 web server 在呼叫 handler 時預設會做 recover
- 被 recover 接住的 panic value 可以用 if 判斷 type
  * recovered panic value 不符預期，可以再 panic 一次，會往上 propagate
  * 上層的 function 可以再用 recover 去接 panic
- 特定狀況下 panic 無法被 recover, 例如 out-of-memory
