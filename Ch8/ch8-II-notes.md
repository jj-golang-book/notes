CH 8.5 ~ 8.10 Notes
===================

Ch 8.5
------

- embarrassingly parallel (perfectly parallel)
  * 不需要太多功夫就可以切分的問題
  * 幾乎沒有相依性，也不需要同步
- 直接用 goroutine 跑每個 task，main goroutine 會直接結束，不等其他 goroutine
  * 同步問題
  * 解法：用 channel 去通知 main goroutine 其他 task 跑完了沒
- goroutine leak - 如果遇到第一個 error 就 return，造成其他正在跑的 goroutine 在
  send value to channel 時卡住，造成 goroutine leak
  * 用等同 task 數量的 buffered channel 解
- 無法提前得知 task 數量時
  * 使用 wait group
  * 範例
    - 在 for range 裡會從 channel 一直接資料過來，在 filenames channel 被關閉前
      closer 都不會跑
    - wait group 會自動追蹤 add 與 done 的數量，呼叫 wait 時會等到 add 與 done 數量
      相等時才能繼續執行下去
    - closer 必須是獨立的 goroutine, 否則會發生 deadlock
    - 因為下面的 for range sizes 必須等 sizes channel close 才會結束迴圈

Ch 8.6
------

- throttling
  * 用 buffered channel 實作 counting semaphore
  * 用額外的 counter 追蹤 worklist 數量 (backlog)，歸零時結束程式
  * 另一個方法：建立 20 個 goroutine 去收 tasks (worker model)
    - 避免 deadlock, crawl 回傳的結果要再創建新的 goroutine 送給 worklist
    - 這個版本程式永遠不會結束

Ch 8.7
------

- multiplexing
- 多個 channel 需要 receive 的情況下可以用 select
- select statement 可以 send 也可以 receive
- 同時有多個 case ready 的情況下會隨機選擇其中一個 case
  * equal chance vs fair
- 如果沒有其他 case 準備好可以處理，就會進入 default case
  * non-blocking
  * 重複 select channel with default case 稱為 polling
- 對 nil channel send 或 receive 會永遠 block
  * 可當作簡單的機制來啟用或停用 timeout 或 cancellation
    - timeout 用 channel 實作時，可以是真的 channel 或是 nil
    - 當 timeout channel 是 nil 時代表沒有 timeout

Ch 8.8
------

- 500ms 印一次 file size，故有一個 tick channel
- 同時 walkDir 在另一個 goroutine 執行，透過 fileSizes channel 回傳結果
- 使用 select 選擇上述兩個 channels, 並重複等待 receive
- 如果 verbose flag 沒有設立，則 tick channel 為 nil, 故該 case 永遠不會執行
- 外層用 for 建立無窮迴圈，因此結束條件為 fileSizes channel close
- 用 wait group 去等待每個 go 出去的 walkDir
- 避免過大的 concurrent goroutine, 使用 counting semaphore 作 throttling

Ch 8.9
------

- cancellation 需要 broadcast 到每個相關 goroutine
- channel 收過一次其他 goroutine 就收不到了
- 關閉 channel 可以製造 broadcast 效果
  * 嘗試對 closed channel receive 會直接回傳 zero value
- cancel 時記得清空其他 channel，避免有 goroutine 持續在等 send value
- worker goroutine 一開始就先測試 cancel 成立了沒，如果已經 cancel 就直接 return
- main function 結束後程式就結束了，無法判斷 goroutine 是否已清乾淨
  * 改用 panic 讓每個 goroutine stack 印出來

Ch 8.10
-------

很長的範例...