### The origins of Golang
- 有時候設計語言時，語言特性會互相被參考
	- 也有時候會有無中生有
---
![[Pasted image 20220107011020.png]]
- Go 很常會被當作是 C 的繼承者
	- control flow 語法
	- data types
	- call by value params (特別)
	- pointers (特別)	
		- 在當今語言其實很多會把他簡化掉了
			- 或是實作的比較 safe, 像是 C++ smart pointer https://stackoverflow.com/questions/106508/what-is-a-smart-pointer-and-when-should-i-use-one (不確定 go pointer 跟這個一不一樣)
			- > go has garbage-collected smart pointers, and calls them “pointers". They are absolutely identical in every aspect of how they operate
			- https://www.quora.com/Why-does-Golang-have-pointers-that-make-it-complex-to-learn-unlike-JS-when-both-are-garbage-collected?top_ans=317418702 看起來 golang 的確是類似 smart pointer，但其實是拿來當 reference 用
			- > The only mainstream language that has actual pointers — as in explicit memory addresses that you can do pointer arithmetic on, and aren't managed by any garbage collector — is C/C++
			- > In Go, if you just pretend that structs can only be accessed via pointers, then congratulations, you're doing JS or Java. Pointer-to-struct = object reference.
			- > In Go, there are so-called value types and reference types. Almost every type in Go is a reference type, the only value types being primitives, structs, and constant-length arrays.
			- > That means that Go gives you the option of using a struct as a value type with copy semantics.
			- ![[Pasted image 20220107012533.png]]
			- Golang 有所謂的 reference type 和 value type，不過就算這樣其實操作起來也很像是 smart pointer
			- 這樣 golang 當初幹嘛還要設計 pointer 呢？Hmm，會不會乾脆當初從語言移除/換個替代設計就好了（其實有道理，但似乎沒有那麼多爭論）
				- 有些論點似乎跟當年的時代需求有關 https://www.reddit.com/r/golang/comments/1swme6/naive_question_why_use_pointers/
				- https://medium.com/@annapeterson89/whats-the-point-of-golang-pointers-everything-you-need-to-know-ac5e40581d4d#:~:text=%E2%80%9C%20Pointers%20are%20used%20for%20efficiency,we%20want%20to%20mutate%20it%20.%E2%80%9D
				- C 時代的 value copy 效能，以及實作更有 mem/binary 效率的演算法
				- https://stackoverflow.com/questions/1863460/whats-the-point-of-having-pointers-in-go
				- 還有某種程度的 pool allocate/custom mem management
				- 不過我滿少手寫 mem 等級的 binary tree，所以其實不太好懂
	- compile to machine code (without VM)
		- 跟上一節講到的編譯有關
---
- 影響者：Pascal, Modula-2
	- 移除了標頭檔定義跟實作分開 (還記得 .h & .hpp 嗎？)
	- OBR 也有給些影響，大部分跟 lib 有關，不過無感
- 影響者：CSP
	- 比較少為人知，貝爾實驗室(created C) 設計的小語言
	- 用來做類似 thread/process management 的 + channel
	- 其實算是一個正則語言，不太算是程式語言
	- 關於傳奇的貝爾實驗室 https://zh.wikipedia.org/wiki/%E8%B4%9D%E5%B0%94%E5%AE%9E%E9%AA%8C%E5%AE%A4
	- 後來 RP 有基於 CSP 來設計一些跟底層 + 多線程應用很有關的 PL，也很常去借 C & Pascal 的語言來用 (Squeak)
	- Alef/Plan9, 野心更大的試著設計出一個 system language（也許拿來寫 OS 的？）
		- https://en.wikipedia.org/wiki/System_programming_language
	- 其他方面
		- iota (APL)
		- lexical scope, nested funcs (scheme)
		- slice (new, go 的陣列)
			- `var a [4]int` -> array
			- `letters := []string{"a", "b", "c", "d"}` -> 類 linked list
		- defer (new, 我原本以為他跟 go routine 有關)