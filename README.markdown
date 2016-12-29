![Swift Algorithm Club](./Images/SwiftAlgorithm-410-transp.png)

<!-- 
# Welcome to the Swift Algorithm Club!
-->

# 歡迎來到 Swift 演算法社團

<!-- 
Here you'll find implementations of popular algorithms and data structures in everyone's favorite new language Swift, with detailed explanations of how they work.

If you're a computer science student who needs to learn this stuff for exams -- or if you're a self-taught programmer who wants to brush up on the theory behind your craft -- you've come to the right place!

The goal of this project is to **explain how algorithms work**. The focus is on clarity and readability of the code, not on making a reusable library that you can drop into your own projects. That said, most of the code should be ready for production use but you may need to tweak it to fit into your own codebase.

All code is compatible with **Xcode 7.3** and **Swift 2.2**. We'll keep this updated with the latest version of Swift.

This is a work in progress. More algorithms will be added soon. :-)

:heart_eyes: **Suggestions and contributions are welcome!** :heart_eyes:
-->
在這裡你將找到一些普及的演算法和資料結構在 Swift 上的實作，附帶詳細的解釋.

如果你是需要這些知識來準備考試的學生 -- 或者 你是個想要自我學習的程式設計人員 -- 那你來對地方了!

這個專案的目的是 **解釋演算法是如何運作**.重點放在程式碼的清晰和可讀性，並非製作可重複使用的函式庫能讓你直接放到你的專案中.就算這樣說，大部分的程式碼應該都可以正式使用，但是你可能要調整一下.

所有的程式碼皆可以在 **Xcode 7.3** 和 **Swift 2.2** 上執行.我們將依據最新的 Swift 版本做更新.

這是個正在進行的專案，近期將會新增更多演算法. :-)

:heart_eyes: **歡迎任何建議和貢獻** :heart_eyes:

<!-- 
## Important links 

[What are algorithms and data structures?](What are Algorithms.markdown) Pancakes!

[Why learn algorithms?](Why Algorithms.markdown) Worried this isn't your cup of tea? Then read this.

[Big-O notation](Big-O Notation.markdown). We often say things like, "This algorithm is **O(n)**." If you don't know what that means, read this first.

[Algorithm design techniques](Algorithm Design.markdown). How do you create your own algorithms?

[How to contribute](How to Contribute.markdown). Report an issue to leave feedback, or submit a pull request.

[Under construction](Under Construction.markdown). Algorithms that are under construction.
-->

## 重要連結

[什麼是演算法和資料結構?](What are Algorithms.markdown) 蛋糕!

[為什麼要學演算法?](Why Algorithms.markdown) 煩惱吸收不良? 看看這個.

[Big-O 表示法](Big-O Notation.markdown). 我們常說 "這個演算法複雜度是 **O(n)**." 如果你不知道是什麼意思, 先看看個.

[演算法設計技巧](Algorithm Design.markdown). 如何創造自己的演算法?

[如何貢獻](How to Contribute.markdown). 給我們反饋, 或來一個PR.

[施工中](Under Construction.markdown). 還在建構中的演算法.

<!--
## Where to start? 

 
If you're new to algorithms and data structures, here are a few good ones to start out with:

- [Stack](Stack/)
- [Queue](Queue/)
- [Insertion Sort](Insertion Sort/)
- [Binary Search](Binary Search/) and [Binary Search Tree](Binary Search Tree/)
- [Merge Sort](Merge Sort/)
- [Boyer-Moore string search](Boyer-Moore/)
-->

## 從何下手?

如果你對演算法和資料結構非常陌生，這有幾個不錯的起點:

- [堆疊](Stack/)
- [佇列](Queue/)
- [插入排序](Insertion Sort/)
- [二元搜尋](Binary Search/) 和 [二元搜尋樹](Binary Search Tree/)
- [合併排序](Merge Sort/)
- [Boyer-Moore 字串搜尋](Boyer-Moore/)

<!-- 
## The algorithms (演算法)
-->

## 演算法

<!--
### Searching 

- [Linear Search](Linear Search/). Find an element in an array.
- [Binary Search](Binary Search/). Quickly find elements in a sorted array.
- [Count Occurrences](Count Occurrences/). Count how often a value appears in an array.
- [Select Minimum / Maximum](Select Minimum Maximum). Find the minimum/maximum value in an array.
- [k-th Largest Element](Kth Largest Element/). Find the *k*-th largest element in an array, such as the median.
- [Selection Sampling](Selection Sampling/). Randomly choose a bunch of items from a collection.
- [Union-Find](Union-Find/). Keeps track of disjoint sets and lets you quickly merge them.
-->

### 搜尋

- [線性搜尋](Linear Search/). 在陣列中找到元素.
- [二元搜尋](Binary Search/). 在排序過的陣列中快速找到元素.
- [*發生次數](Count Occurrences/). 計算某個值在陣列中出現幾次.
- [選取 最小值 / 最大值](Select Minimum Maximum). 找到陣列中的最小值/最大值.
- [第K大的值](Kth Largest Element/). 在陣列中，找到第 *k* 大的元素，例如中位數.
- [*選擇採樣](Selection Sampling/). 隨機在集合中選取一堆元素.
- [*聯集查找](Union-Find/). 追蹤沒交集的集合，並快速合併它們.

<!--
### String Search 

- [Brute-Force String Search](Brute-Force String Search/). A naive method.
- [Boyer-Moore](Boyer-Moore/). A fast method to search for substrings. It skips ahead based on a look-up table, to avoid looking at every character in the text.
- Knuth-Morris-Pratt
- Rabin-Karp
- [Longest Common Subsequence](Longest Common Subsequence/). Find the longest sequence of characters that appear in the same order in both strings.
-->

### 字串搜尋

- [Brute-Force 字串搜尋](Brute-Force String Search/). 一個簡單暴力的方法.
- [Boyer-Moore](Boyer-Moore/). 一個快速搜尋子字串的方法. 為了避免走訪每個字元，根據查表來跳過某些的部分. 
- Knuth-Morris-Pratt
- Rabin-Karp
- [*最長共同子字串](Longest Common Subsequence/). 找到兩個字串中相同部分最長的子字串.

<!--
### Sorting

It's fun to see how sorting algorithms work, but in practice you'll almost never have to provide your own sorting routines. Swift's own `sort()` is more than up to the job. But if you're curious, read on...

-->

### 排序

研究排序演算法的原理是非常有趣的，但是在實做中，你幾乎不會需要去碰到這部分.Swift 自帶的 `sort()` 就已經很夠用了，但是如果你有興趣，請繼續閱讀...

<!--
Basic sorts:

- [Insertion Sort](Insertion Sort/)
- [Selection Sort](Selection Sort/)
- [Shell Sort](Shell Sort/)
-->

基本排序演算法:

- [插入排序](Insertion Sort/)
- [選擇排序](Selection Sort/)
- [Shell 排序](Shell Sort/)

<!--
Fast sorts:

- [Quicksort](Quicksort/)
- [Merge Sort](Merge Sort/)
- [Heap Sort](Heap Sort/)
-->

快速排序演算法:

- [快速排序](Quicksort/)
- [合併排序](Merge Sort/)
- [堆積排序](Heap Sort/)

Special-purpose sorts:

特殊目的排序演算法:

- [Counting Sort](Counting Sort/)
- Radix Sort
- [Topological Sort](Topological Sort/)

- [計數排序](Counting Sort/)
- Radix Sort
- [*拓樸排序](Topological Sort/)

<!--
Bad sorting algorithms (don't use these!):

- [Bubble Sort](Bubble Sort/)
- [Slow Sort](Slow Sort/)
-->

糟糕的排序演算法 (知道就好，不要使用!):

- [氣泡排序](Bubble Sort/)
- [*緩慢排序](Slow Sort/)

<!--
### Compression

- [Run-Length Encoding (RLE)](Run-Length Encoding/). Store repeated values as a single byte and a count.
- [Huffman Coding](Huffman Coding/). Store more common elements using a smaller number of bits.
-->

### 壓縮

- [運行長度編碼 (RLE)](Run-Length Encoding/). 將重複的值儲存為一個 byte 並計數.
- [Huffman 編碼](Huffman Coding/). 將常出現的值用較小的值來儲存.

<!--
### Miscellaneous

- [Shuffle](Shuffle/). Randomly rearranges the contents of an array.
- [Comb Sort](Comb Sort/). An improve upon the Bubble Sort algorithm.
-->

### 雜項

- [*洗牌](Shuffle/). 隨機重排陣列中的元素.
- [*梳排序](Comb Sort/). 氣泡排序法進化.


<!--
### Mathematics

- [Greatest Common Divisor (GCD)](GCD/). Special bonus: the least common multiple.
- [Permutations and Combinations](Combinatorics/). Get your combinatorics on!
- [Shunting Yard Algorithm](Shunting Yard/). Convert infix expressions to postfix.
- Statistics
- [Karatsuba Multiplication](Karatsuba Multiplication/). Another take on elementary multiplication.
- [Haversine Distance](HaversineDistance/). Calculating the distance between 2 points from a sphere.
-->

### *數學導向

- [最大公因數 (GCD)](GCD/). 加碼: 最小公倍數.
- [排列組合](Combinatorics/). 回顧學過的排列組合吧!
- [*分流場](Shunting Yard/). 將中序式表示法轉換為後序式表示法.
- 統計
- [Karatsuba 快速乘法](Karatsuba Multiplication/). 另一種基礎乘法.
- [Haversine 距離](HaversineDistance/). 計算球體上兩點的距離.


<!--
### Machine learning

- [k-Means Clustering](K-Means/). Unsupervised classifier that partitions data into *k* clusters.
- k-Nearest Neighbors
- [Linear Regression](Linear Regression/)
- Logistic Regression
- Neural Networks
- PageRank
-->

### 機器學習

- [k-Means 叢集](K-Means/). 非監督式分類，將數據分成 *k* 群集.
- k-Nearest 鄰近
- [線性迴歸](Linear Regression/)
- 邏輯迴歸
- 神經網路
- 網頁排名

<!--
## Data structures

The choice of data structure for a particular task depends on a few things.

First, there is the shape of your data and the kinds of operations that you'll need to perform on it. If you want to look up objects by a key you need some kind of dictionary; if your data is hierarchical in nature you want a tree structure of some sort; if your data is sequential you want a stack or queue.

Second, it matters what particular operations you'll be performing most, as certain data structures are optimized for certain actions. For example, if you often need to find the most important object in a collection, then a heap or priority queue is more optimal than a plain array.

Most of the time using just the built-in `Array`, `Dictionary`, and `Set` types is sufficient, but sometimes you may want something more fancy...
-->

## 資料結構

對於資料結構和特定任務的選擇依據幾件事情.

首先，你的資料是具有某種形態，並且有一些必要的操作方法.
如果你想要依照某個 key 值去找到物件，那你需要 字典 類型的結構; 如果你的資料有分層特徵，你那會想要類似樹狀結構; 如果你的資料是序列，那你需要 堆疊 或 佇列 結構.

第二，你的資料需要一些特別的操作，不同的結構對於優化的操作都不盡相同.舉例來說，如果你常需要獲得集合中最重要的元素，那使用 堆積 或 優先佇列 就會比單純的陣列還要好.

大多數情況下，內建的 `Array`、`Dictionary` 和 `Set` 已經足夠，但有時候你會需要更特殊的結構.

<!--
### Variations on arrays

- [Array2D](Array2D/). A two-dimensional array with fixed dimensions. Useful for board games.
- [Bit Set](Bit Set/). A fixed-size sequence of *n* bits.
- [Fixed Size Array](Fixed Size Array/). When you know beforehand how large your data will be, it might be more efficient to use an old-fashioned array with a fixed size.
- [Ordered Array](Ordered Array/). An array that is always sorted.
-->

### 陣列的變化

- [二維陣列](Array2D/). 固定大小的二維陣列，可用於棋盤遊戲中.
- [位元集](Bit Set/). 含有 *n* 個位元的序列.
- [固定長度陣列](Fixed Size Array/). 如果可以確定資料量大小，那使用傳統的固定長度陣列會具有更高的效能.
- [有序陣列](Ordered Array/). 一個已經排序過的陣列.

<!--
### Queues

- [Stack](Stack/). Last-in, first-out!
- [Queue](Queue/). First-in, first-out!
- [Deque](Deque/). A double-ended queue.
- [Priority Queue](Priority Queue). A queue where the most important element is always at the front.
- [Ring Buffer](Ring Buffer/). Also known as a circular buffer. An array of a certain size that conceptually wraps around back to the beginning.
-->

### 佇列

- [堆疊](Stack/). 後進先出.
- [佇列](Queue/). 先進先出.
- [雙端佇列](Deque/). 可選擇在陣列的前端或尾端新增或取得元素.
- [優先佇列](Priority Queue). 最重要的元素永遠在最前端.
- [環狀緩衝](Ring Buffer/). 一個頭尾相接的陣列.

<!--
### Lists

- [Linked List](Linked List/). A sequence of data items connected through links. Covers both singly and doubly linked lists.
<<<<<<< HEAD
- [Skip-List](Skip-List/). Skip List is a probablistic data-structure with same logarithmic time bound and efficiency as AVL/ or Red-Black tree and provides a clever compromise to efficiently support search and update operations.
-->

### 列表

- [鏈結列表](Linked List/). 內容元素以鏈結來銜接的序列，包含單向及雙向.
- [跳躍列表](Skip-List/). 這是一種機率性的資料結構, 提供對數時間複雜度, 並且擁有 AVL 或 紅黑樹的效能, 還有支援聰明又有效率的搜尋及更新的操作.

<!--
### Trees

- [Tree](Tree/). A general-purpose tree structure.
- [Binary Tree](Binary Tree/). A tree where each node has at most two children.
- [Binary Search Tree (BST)](Binary Search Tree/). A binary tree that orders its nodes in a way that allows for fast queries.
- Red-Black Tree
- Splay Tree
- Threaded Binary Tree
- [Segment Tree](Segment Tree/). Can quickly compute a function over a portion of an array.
- kd-Tree
- [Heap](Heap/). A binary tree stored in an array, so it doesn't use pointers. Makes a great priority queue.
- Fibonacci Heap
- [Trie](Trie/). A special type of tree used to store associative data structures.
- [B-Tree](B-Tree/). A self-balancing search tree, in which nodes can have more than two children.
-->

### 樹

- [樹](Tree/). 一般性的樹狀結構.
- [二元樹](Binary Tree/). 每個節點最多只有兩個子節點的樹.
- [二元搜尋樹 (BST)](Binary Search Tree/). 節點經過排序的二元樹，優化了搜尋速度.
- 紅黑樹
- 展開樹
- 螺旋二元樹
- [分割樹](Segment Tree/). 可以快速的對陣列中的某區間進行計算.
- kd 樹
- [堆積](Heap/). 儲存在一維陣列中的二元樹，不需要指標，很適合做優先佇列.
- 費波那契堆積
- 字典樹
- [B 樹](B-Tree/). 自平衡搜尋樹，每個節點可以擁有超過兩個子節點.

<!--
### Hashing

- [Hash Table](Hash Table/). Allows you to store and retrieve objects by a key. This is how the dictionary type is usually implemented.
- Hash Functions
-->

### 雜湊

- [雜湊表](Hash Table/). 可以使用 key 值來儲存或取得物件，字典通常是使用雜湊表實現的.
- 雜湊函數


<!--
### Sets

- [Bloom Filter](Bloom Filter/). A constant-memory data structure that probabilistically tests whether an element is in a set.
- [Hash Set](Hash Set/). A set implemented using a hash table.
- Multiset
- [Ordered Set](Ordered Set/). A set where the order of items matters.
-->

### 集合

- [布隆過濾器](Bloom Filter/).可以用於檢索一個元素是否在一個集合中.它的優點是空間效率和查詢時間都遠遠超過一般的演算法，缺點是有一定的誤識別率和刪除困難.
- [雜湊集合](Hash Set/). 使用雜湊表實做.
- 多重集合
- [有序集合](Ordered Set/). 元素有順序的集合.

<!--
### Graphs

- [Graph](Graph/)
- [Breadth-First Search (BFS)](Breadth-First Search/)
- [Depth-First Search (DFS)](Depth-First Search/)
- [Shortest Path](Shortest Path %28Unweighted%29/) on an unweighted tree
- [Single-Source Shortest Paths](Single-Source Shortest Paths (Weighted)/)
- [Minimum Spanning Tree](Minimum Spanning Tree %28Unweighted%29/) on an unweighted tree
- [All-Pairs Shortest Paths](All-Pairs Shortest Paths/)
-->

### 圖

- [圖](Graph/)
- [廣度優先搜尋 (BFS)](Breadth-First Search/)
- [深度優先搜尋 (DFS)](Depth-First Search/)
- [最短路徑](Shortest Path %28Unweighted%29/) 在未加權的樹上討論
- [單源最短路徑](Single-Source Shortest Paths (Weighted)/)
- [最小生成樹](Minimum Spanning Tree %28Unweighted%29/) 在未加權的樹上討論
- [任意兩點最短路徑](All-Pairs Shortest Paths/)


<!--
## Puzzles

A lot of software developer interview questions consist of algorithmic puzzles. Here is a small selection of fun ones. For more puzzles (with answers), see [here](http://elementsofprogramminginterviews.com/) and [here](http://www.crackingthecodinginterview.com).

- [Two-Sum Problem](Two-Sum Problem/)
- [Fizz Buzz](Fizz Buzz/)
- [Monty Hall Problem](Monty Hall Problem/)
- [Finding Palindromes](Palindromes/)
-->

## 動腦時間

許多軟體開發者在面試時都會被問到一些演算法的問題，這裡只收集了一些有趣的題目，想瞭解更多這類的問題 (及答案)，請瀏覽 [這裡](http://elementsofprogramminginterviews.com/) 和 [這裡](http://www.crackingthecodinginterview.com).

- [雙和問題](Two-Sum Problem/)
- [Fizz Buzz](Fizz Buzz/)
- [蒙提霍爾問題](Monty Hall Problem/)
- [尋找迴文](Palindromes/)


<!--
## Learn more!

For more information, check out these great books:

- [Introduction to Algorithms](https://mitpress.mit.edu/books/introduction-algorithms) by Cormen, Leiserson, Rivest, Stein
- [The Algorithm Design Manual](http://www.algorist.com) by Skiena
- [Elements of Programming Interviews](http://elementsofprogramminginterviews.com) by Aziz, Lee, Prakash
- [Algorithms](http://www.cs.princeton.edu/~rs/) by Sedgewick

The following books are available for free online:

- [Algorithms](http://www.beust.com/algorithms.pdf) by Dasgupta, Papadimitriou, Vazirani
- [Algorithms, Etc.](http://jeffe.cs.illinois.edu/teaching/algorithms/) by Erickson
- [Algorithms + Data Structures = Programs](http://www.ethoberon.ethz.ch/WirthPubl/AD.pdf) by Wirth
- Algorithms and Data Structures: The Basic Toolbox by Mehlhorn and Sanders
- [Wikibooks: Algorithms and Implementations](https://en.wikibooks.org/wiki/Algorithm_Implementation)

Other algorithm repositories:

- [EKAlgorithms](https://github.com/EvgenyKarkan/EKAlgorithms). A great collection of algorithms in Objective-C.
- [@lorentey](https://github.com/lorentey/). Production-quality Swift implementations of common algorithms and data structures.
- [Rosetta Code](http://rosettacode.org). Implementations in pretty much any language you can think of.
- [AlgorithmVisualizer](http://jasonpark.me/AlgorithmVisualizer/). Visualize algorithms on your browser.
- [Swift Structures](https://github.com/waynewbishop/SwiftStructures) Data Structures with directions on how to use them [here](http://waynewbishop.com/swift)
-->

## 更多研究

請參閱以下書籍獲得更多訊息:

- [Introduction to Algorithms](https://mitpress.mit.edu/books/introduction-algorithms) by Cormen, Leiserson, Rivest, Stein
- [The Algorithm Design Manual](http://www.algorist.com) by Skiena
- [Elements of Programming Interviews](http://elementsofprogramminginterviews.com) by Aziz, Lee, Prakash
- [Algorithms](http://www.cs.princeton.edu/~rs/) by Sedgewick

以下書籍均可免費線上閱讀:

- [Algorithms](http://www.beust.com/algorithms.pdf) by Dasgupta, Papadimitriou, Vazirani
- [Algorithms, Etc.](http://jeffe.cs.illinois.edu/teaching/algorithms/) by Erickson
- [Algorithms + Data Structures = Programs](http://www.ethoberon.ethz.ch/WirthPubl/AD.pdf) by Wirth
- Algorithms and Data Structures: The Basic Toolbox by Mehlhorn and Sanders
- [Wikibooks: Algorithms and Implementations](https://en.wikibooks.org/wiki/Algorithm_Implementation)

其他演算法資源:

- [EKAlgorithms](https://github.com/EvgenyKarkan/EKAlgorithms). A great collection of algorithms in Objective-C.
- [@lorentey](https://github.com/lorentey/). Production-quality Swift implementations of common algorithms and data structures.
- [Rosetta Code](http://rosettacode.org). Implementations in pretty much any language you can think of.
- [AlgorithmVisualizer](http://jasonpark.me/AlgorithmVisualizer/). Visualize algorithms on your browser.
- [Swift Structures](https://github.com/waynewbishop/SwiftStructures) Data Structures with directions on how to use them [here](http://waynewbishop.com/swift)

<!--
## Credits

The Swift Algorithm Club was originally created by [Matthijs Hollemans](https://github.com/hollance).

It is now maintained by [Chris Pilcher](https://github.com/chris-pilcher) and [Kelvin Lau](https://github.com/kelvinlauKL).

The Swift Algorithm Club is a collaborative effort from the [most algorithmic members](https://github.com/rwenderlich/swift-algorithm-club/graphs/contributors) of the [raywenderlich.com](https://www.raywenderlich.com) community. We're always looking for help - why not [join the club](How to Contribute.markdown)? :]
-->

Swift 演算法社團是由 [Matthijs Hollemans](https://github.com/hollance) 所創立.

現在由 [Chris Pilcher](https://github.com/chris-pilcher) 和 [Kelvin Lau](https://github.com/kelvinlauKL) 維護.

Swift 演算法社團是由 [raywenderlich.com](https://www.raywenderlich.com) 的 [大部分的團員](https://github.com/rwenderlich/swift-algorithm-club/graphs/contributors) 協作而成. 我們一直歡迎任何幫助 - 為何不 [加入社團](How to Contribute.markdown)? :]

<!--
## License

All content is licensed under the terms of the MIT open source license.

-->
## 許可證

本專案包含 [原專案](https://github.com/raywenderlich/swift-algorithm-club) 都是基於 MIT 協議，請隨意使用!


[![Build Status](https://travis-ci.org/raywenderlich/swift-algorithm-club.svg?branch=master)](https://travis-ci.org/raywenderlich/swift-algorithm-club)
