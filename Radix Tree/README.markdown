# Radix Tree (基數樹)

<!--
A radix tree is a [tree](../Tree/) where a node can have any number of children. Each edge leading from a node to a child has a label (usually a string). A radix tree is often used to store strings or IP addresses. By traversing from the root to any leaf in the tree, concatenating all the labels of edges along the way, you can find any string.

This is an example of a radix tree (image from wikipedia.org):
-->

基數樹是一種 [樹](../Tree/), 其中節點可以有任意數量的子節點. 每個連結到子節點的邊都有個標籤 (通常是字串). 基數樹通常用來儲存字串或 IP 位址. 從根節點開始走訪到任何一個葉節點, 將中間經過邊的標籤串接起來, 就可以獲得一個字串.

這是基數樹的一個例子 (圖片來自 wikipedia.org):

![](Images/radixtree.png)

<!--
## Implemenation

The radix tree is represented by a `RadixTree` object. This class has one member variable, `root`, of type `Root`. The `Root` is the object at the top of every `RadixTree`. 

Every other node is of type `Edge` and is a child (or child of a child, etc.) of the root. 

The difference between `Edge` and `Root` is that edges have a label (of type `String`) and a reference to a parent node. 

This approach is a little different from the conventional way of creating tree data structures but is easier to wrap your head around (see the picture above).
-->

## 實作

基數樹用 `RadixTree` 物件來呈現. 這個類別有一個變數, `root`, 型別是 `Root`. `Root` 是在每個 `RadixTree` 中最高層的物件.

其他的節點是 `Edge` 型別的物件, 而它們都是 `root` 的子節點 (或者其他節點的子節點).

`Edge` 和 `Root` 主要差異在於邊有標籤和父節點的參考指標.

這此的實作和一般創建樹的方法有點不同, 但是比較容易理解 (參考上面的圖).

<!--
## Operations

Typical operations on a radix tree are:

- lookup
- insertion
- deletion
- find predecessor
- find successor
- find all strings with common prefix

The running time of lookup, insertion, and deletion is **O(k)** where **k** is the length of the key. This is different from most trees because these operations usually run in **O(log n)** time where **n** is the number of nodes in the tree.
-->

## 操作

典型的基數樹有這些操作:

- 查找
- 插入
- 刪除
- 尋找前驅節點
- 尋找後繼節點
- 找到有共同前綴詞的所有字串

查找, 插入, 刪除的時間是 **O(k)**, **k** 是鍵值的長度. 大部分的樹狀結構是 **O(log n)**, **n** 是節點總數.

<!--
### Lookup

The `find()` function returns true if the string you are searching for is in the tree and false if it is not. 

Every `RadixTree` contains at the very least the empty string `""` so `find("")` will always return true. 

A string is considered "in the tree" if the text you are searching for is a concatenation of `Edge` labels while traversing downwards, or a prefix of that concatenation.

For example, if you look at the example tree above, `find("roma")` will return true because it is a prefix of the traversal `"r" -> "om" -> "an"`. 

On the other hand, `find("romans")` will return false.
-->

## 查找

如果在字串中找到搜尋的鍵值, `find()` 會回傳 true, 反之 false.

每個 `RadixTree` 都擁有至少一個空字串 `""`, 所以 `find("")` 都會回傳 true.

當搜尋的字串是向下走訪樹時邊標籤的串接, 或者該串接的前綴, 就把該字串當作是 "被包含在樹中".

舉個例子, 如果對上述的樹中, `find("roma")` 將會為傳 true, 因為它是走訪 `"r" -> "om" -> "an"` 的前綴.

另一方面, `find("romans")` 將會回傳 false.


<!--
### Insertion

The `insert()` function lets you add new strings to the radix tree.

This function returns true if the string you are trying to insert was successfully inserted into the `RadixTree` and false if the string is already in the tree.
-->

### 插入

`insert()` 函式讓你可以新增字串到樹中.

當成功插入字串時此方法會回傳 true, 如果該字串已經在樹之中則回傳 false.

<!--
### Deletion

The `remove()` removes a string from the tree. When a string is removed, any other strings that have a prefix of the removed string are removed as well. 

For example, `remove("rom")` will also remove `"roman"`, `"rome"`, and `"romulus"` if those strings are in the tree as well. Calling `remove("")` will remove all strings in the tree.
-->

### 刪除

`remove()` 將字串從樹中移除. 當一個字串被刪除, 其他以該字串當前綴的字串也會一並被刪除.

例如, `remove("rom")` 將會移除 `"roman"`, `"rome"`, 和 `"romulus"`. 呼叫 `remove("")` 將會移除樹中的所有字串.



### printTree()

<!--
You can use the `printTree()` function to visualize the tree. This function is a little buggy and not perfect yet but gets the job done thus far.
-->

你可以使用 `printTree()` 函式來視覺化整個樹. 這個函式還有點小問題, 但會盡快修正.

<!--
### Helper functions

The `sharedPrefix()` function is a non-member function in the **RadixTree.swift** file. It takes in two `String` objects and returns the shared prefix between them. 

For example, `sharedPrefix("apples", "oranges")` will return `""`, and `sharedPrefix("court", "coral")` will return `"co"`. 

This function is used in the implementation of the `find()`, `insert()`, and `remove()` functions.
-->

### 幫助函式

`sharedPrefix()` 函式是一個無成員的函式. 接受兩個字串參數然後回傳兩字串共同的前綴.

例如, `sharedPrefix("apple", "oranges")` 將會回傳 `""`, 而 `sharedPrefix("court", "coral")` 會回傳 `"co"`.

這個方法在實作 `find()`, `insert()`, 和 `remove()` 函式中常用到.

<!--
## See also
-->

## 相關閱讀

[Radix Tree - Wikipedia](https://en.wikipedia.org/wiki/Radix_tree)

*Written for Swift Algorithm Club by Steven Scally*
