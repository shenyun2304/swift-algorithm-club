# Heap (堆積)

<!--
A heap is a [binary tree](../Binary Tree/) that lives inside an array, so it doesn't use parent/child pointers. The tree is partially sorted according to something called the "heap property" that determines the order of the nodes in the tree.

Common uses for heap:

- For building [priority queues](../Priority Queue/).
- The heap is the data structure supporting [heap sort](../Heap Sort/).
- Heaps are fast for when you often need to compute the minimum (or maximum) element of a collection.
- Impressing your non-programmer friends.
-->


堆積是個活在陣列中的 [二元樹](../Binary Tree/), 它並不使用父節點或子節點. 這棵樹依據某些名為"堆積屬性"的特性來排序.

堆積的一般使用:

- 建立 [優先佇列](../Priority Queue/).
- 堆積是一個支援 [堆積排序](../Heap Sort/) 的資料結構.
- 快速的計算集合中的最大或最小值.
- 讓你的非程式員朋友印象深刻.

<!--
## The heap property

There are two kinds of heaps: a *max-heap* and a *min-heap*. They are identical, except that the order in which they store the tree nodes is opposite.

In a max-heap, parent nodes must always have a greater value than each of their children. For a min-heap it's the other way around: every parent node has a smaller value than its child nodes. This is called the "heap property" and it is true for every single node in the tree.

An example:
-->

## 堆積屬性

有兩種堆積: *最大堆積* 和 *最小堆積*. 它們除了儲存節點的順序以外都很相像.

在最大堆積中, 父節點總是比他的任何一個子節點還要大, 而最小堆積則相反: 每個父節點會被他所有子節點還要小. 這個規則就是所謂的 "堆積屬性", 對於每個樹中的節點者屬性都有效.

舉個例子:

![A max-heap](Images/Heap1.png)

<!--
This is a max-heap because every parent node is greater than its children. `(10)` is greater than `(7)` and `(2)`. `(7)` is greater than `(5)` and `(1)`.

As a result of this heap property, a max-heap always stores its largest item at the root of the tree. For a min-heap, the root is always the smallest item in the tree. That is very useful because heaps are often used as a [priority queue](../Priority Queue/) where you want to quickly access the "most important" element.

> **Note:** You can't really say anything else about the sort order of the heap. For example, in a max-heap the maximum element is always at index 0 but the minimum element isn’t necessarily the last one -- the only guarantee you have is that it is one of the leaf nodes, but not which one.
-->

這是一個最大堆積的例子, 因為每個父節點都比它的子節點還要大. `(10)` 大於 `(7)` 和 `(2)`. 而 `(7)` 大於 `(5)` 和 `(1)`.

根據最大堆積屬性, 最大的元素一定在樹的根節點. 如果是最小堆積, 那根節點就會是最小的元素. 因為堆積通常拿來當 [優先佇列](../Priority Queue/), 用來快速獲得"最重要"的元素.

> **注意:** 你無法真的知道堆積中元素的順序. 舉例來說, 在最大堆積中, 最大的元素一定在索引 0 的位置, 但是最小的元素不一定是在最後的位置 -- 你只能保證最小元素是在葉節點上, 但是也無法知道是哪個葉節點.

<!--
## How does heap compare to regular trees?

A heap isn't intended to be a replacement for a binary search tree. But there are many similarities between the two and also some differences. Here are some of the bigger differences:

**Order of the nodes.** In a [binary search tree (BST)](../Binary Search Tree/), the left child must always be smaller than its parent and the right child must be greater. This is not true for a heap. In max-heap both children must be smaller; in a min-heap they both must be greater.

**Memory.** Traditional trees take up more memory than just the data they store. You need to allocate additional storage for the node objects and pointers to the left/right child nodes. A heap only uses a plain array for storage and uses no pointers.

**Balancing.** A binary search tree must be "balanced" so that most operations have **O(log n)** performance. You can either insert and delete your data in a random order or use something like an [AVL tree](../AVL Tree/) or [red-black tree](../Red-Black Tree/). But with heaps we don't actually need the entire tree to be sorted. We just want the heap property to be fulfilled, and so balancing isn't an issue. Because of the way the heap is structured, heaps can guarantee **O(log n)** performance.

**Searching.** Searching a binary tree is really fast -- that's its whole purpose. In a heap, searching is slow. The purpose of a heap is to always put the largest (or smallest) node at the front, and to allow relatively fast inserts and deletes. Searching isn't a top priority.
-->

## 堆積跟一些樹狀結構比較?

堆積並不是拿來取代二元搜尋樹的. 但是這兩個有很多相似處, 也有些差異. 以下是幾個主要的差異:

**節點的順序** 在 [二元搜尋樹 (BST)](../Binary Search Tree/) 中, 左邊的子節點一定小於其父節點, 而右子節點一定大於其父節點. 在堆積中沒有這個限制. 在最大堆積中, 左右兩個子節點必小於其父節點; 而最小堆積中, 左右兩子節點必大於其父節點.

**記憶體.** 傳統的樹狀結構佔用比他料本身更多的記憶體資源. 你需要配置父節點, 左右子節點的參考給每個節點. 堆積只需要單純的陣列而不需要參考.

**平衡.** 一個二元搜尋樹在"平衡"的狀態下, 大部分的操作是 **O(log n)** 時間. 你可以隨機的插入或刪除資料或者使用 [AVL 樹](../AVL Tree/) 或 [紅黑樹](../Red-Black Tree/). 但是在堆積中, 我們並不是很需要整棵樹都是有序的. 我們只希望符合堆積屬性就好, 所以平衡並不是很重要. 所以堆積就出現啦, 堆積保證所有操作都是 **O(log n)**.


**搜尋.** 在二元樹中搜尋是非常快的 -- 這就是二元樹的目的啊! 在堆積中, 搜尋是很慢的. 堆積的目的是保持最大 (或最小) 的元素在最前端, 然後提供相對快速的插入和刪除操作. 搜尋並不是堆積的想解決的目標.

<!--
## The tree that lived in an array

An array may seem like an odd way to implement a tree-like structure but it is very efficient in both time and space.

This is how we're going to store the tree from the above example:
-->
## 活在陣列中的樹

用陣列來實現樹狀結構是有點奇怪啦, 但這結構在時間和空間上非常有效率.

這是我們拿上述例子來產生的陣列:

	[ 10, 7, 2, 5, 1 ]


<!--
That's all there is to it! We don't need any more storage than just this simple array.

So how do we know which nodes are the parents and which are the children if we're not allowed to use any pointers? Good question! There is a well-defined relationship between the array index of a tree node and the array indices of its parent and children.

If `i` is the index of a node, then the following formulas give the array indices of its parent and child nodes:
-->
這就是全部! 我們不需要比一個單純的陣列更多的空間.

所以我們如何在不使用任何節點參考的情況下知道哪些節點是父節點而哪些是子節點? 好問題! 在樹的節點和陣列的索引之間有個非常好的關係.

如果 `i` 是節點的索引, 那以下的公式可以獲得該節點的父節點及子節點:

    parent(i) = floor((i - 1)/2)
    left(i)   = 2i + 1
    right(i)  = 2i + 2
    
<!--
Note that `right(i)` is simply `left(i) + 1`. The left and right nodes are always stored right next to each other.

Let's use these formulas on the example. Fill in the array index and we should get the positions of the parent and child nodes in the array:
-->
注意到 `right(i)` 其實就是 `left(i) + 1`. 左子節點和右子節點總是緊連在一起.

讓我們把公式套用到例子中. 把陣列的索引填滿我們應該可以得到父節點和子節點的位置:


| Node | Array index (`i`) | Parent index | Left child | Right child |
|------|-------------|--------------|------------|-------------|
| 10 | 0 | -1 | 1 | 2 |
| 7 | 1 | 0 | 3 | 4 |
| 2 | 2 | 0 | 5 | 6 | 
| 5 | 3 | 1 | 7 | 8 |
| 1 | 4 | 1 | 9 | 10 |

<!--
Verify for yourself that these array indices indeed correspond to the picture of the tree.

> **Note:** The root node `(10)` doesn't have a parent because `-1` is not a valid array index. Likewise, nodes `(2)`, `(5)`, and `(1)` don't have children because those indices are greater than the array size. So we always have to make sure the indices we calculate are actually valid before we use them.

Recall that in a max-heap, the parent's value is always greater than (or equal to) the values of its children. This means the following must be true for all array indices `i`:
-->

自行驗證一下這些陣列的索引的確有對應到上面的樹狀圖.

> **注意:** 根節點 `(10)` 沒有父節點, 因為 `-1` 不是個正確的陣列索引. 同樣的, `(2)`, `(5)` 和 `(1)` 沒有子節點, 因為它們的子節點索引都大於陣列的長度. 所以在使用這個公式後我們總是要去驗證算出來的索引是否為正確的.

回想一下最大堆積, 父節點的值總是大於其所有子節點. 這表示以下這個判斷式對任何一個 `i` 一定是 `true`:

```swift
array[parent(i)] >= array[i]
```
	
<!--
Verify that this heap property holds for the array from the example heap.

As you can see, these equations let us find the parent or child index for any node without the need for pointers. True, it's slightly more complicated than just dereferencing a pointer but that's the tradeoff: we save memory space but pay with extra computations. Fortunately, the computations are fast and only take **O(1)** time.

It's important to understand this relationship between array index and position in the tree. Here's a slightly larger heap, this tree has 15 nodes divided over four levels:
-->

驗證一下最大堆積屬性在上例的堆積結構中成立.

可以看到, 這些公式可以讓我們找到父節點和子節點, 而不需要參考指標.  這的確是只比使用指標複雜一點點 (需要計算), 而這樣做法我的得到: 節省了記憶體空間, 多一點計算. 幸運的是, 那些計算非常快速, 而只佔用 **O(1)** 時間.

了解索引與節點位置的關係公式是很重要的. 這裡有個大一點的堆積, 有 15 個節點並共有 4 層:

![Large heap](Images/LargeHeap.png)

<!--
The numbers in this picture aren't the values of the nodes but the array indices that store the nodes! Those array indices correspond to the different levels of the tree like this:
-->

圖中節點內的數字表示該節點再轉換成陣列後的索引值, 不是節點本身的值. 這些索引對應層數就像這樣:

![The heap array](Images/Array.png)

<!--
For the formulas to work, parent nodes must always appear before child nodes in the array. You can see that in the above picture.

Note that this scheme has limitations. You can do the following with a regular binary tree but not with a heap:
-->

為了讓公式正確轉換, 父節點一定要比子節在陣列中點更早出現. 在上面圖片可以看出這點.

![Impossible with a heap](Images/RegularTree.png)

<!--
You can’t start a new level unless the current lowest level is completely full. So heaps always have this kind of shape:
-->

如果低階層及還沒有填滿, 不能新建下一個層級. 所以推機的圖形都類似這樣:

![The shape of a heap](Images/HeapShape.png)

<!--
> **Note:** Technically speaking you *could* emulate a regular binary tree with a heap, but it would waste a lot of space and you’d need some way to mark array indices as being empty.

Pop quiz! Let's say we have the array:
-->
> **注意:** 技術上你是 *可以* 用二元樹去模擬推機, 但是那樣就會浪費一堆空間, 而且還需要一些方法讓某些索引指向空值.

來個小測試! 看看下面的陣列:

	[ 10, 14, 25, 33, 81, 82, 99 ]
	
<!--
Is this a valid heap? The answer is yes! A sorted array from low-to-high is a valid min-heap. We can draw this heap as follows:
-->

這是一個正確的堆積嗎? 答案是 是! 一個有序的遞增陣列是個最小堆積. 示意圖在此:

![A sorted array is a valid heap](Images/SortedArray.png)

<!--
The heap property holds for each node because a parent is always smaller than its children. (Verify for yourself that an array sorted from high-to-low is always a valid max-heap.)

> **Note:** But not every min-heap is necessarily a sorted array! It only works one way... To turn a heap back into a sorted array, you need to use [heap sort](../Heap Sort/).
-->

因為每個節點都小於其子節點, 所以最小堆積的規則是成立的. (自行驗證看看是否遞減陣列總是最大堆積.)

> **注意:** 並不是每個最小堆積都是有序陣列! 這只有單向邏輯... 如果要讓堆積轉換成有序陣列, 你需要用 [堆積排序](../Heap Sort/).

<!--
## More math!

In case you are curious, here are a few more formulas that describe certain properties of a heap. You don't need to know these by heart but they come in handy sometimes. Feel free to skip this section!

The *height* of a tree is defined as the number of steps it takes to go from the root node to the lowest leaf node. Or more formally: the height is the maximum number of edges between the nodes. A heap of height *h* has *h + 1* levels.

This heap has height 3 and therefore has 4 levels:
-->

## 更多數學!

如果你有興趣, 這裡有幾個表示某些堆積屬性的公式. 你不需要從頭到尾了解這些, 但是它們有時候挺好用的. 沒興趣跳過這節也沒關係.

樹的 *高度* 定義是從根節點走到最末端葉節點的層數. 或更具體一點: 高度是兩個節點之間最大的邊數. 一個堆積的高度是 *h* 有 *h+1*  層.

這個堆積高度是 *3* 所以有 4 層:

![Large heap](Images/LargeHeap.png)

<!--
A heap with *n* nodes has height *h = floor(log_2(n))*. This is because we always fill up the lowest level completely before we add a new level. The example has 15 nodes, so the height is `floor(log_2(15)) = floor(3.91) = 3`.

If the lowest level is completely full, then that level contains *2^h* nodes. The rest of the tree above it contains *2^h - 1* nodes. Fill in the numbers from the example: the lowest level has 8 nodes, which indeed is `2^3 = 8`. The first three levels contain a total of 7 nodes, i.e. `2^3 - 1 = 8 - 1 = 7`.

The total number of nodes *n* in the entire heap is therefore *2^(h+1) - 1*. In the example, `2^4 - 1 = 16 - 1 = 15`.

There are at most *ceil(n/2^(h+1))* nodes of height *h* in an *n*-element heap. 

The leaf nodes are always located at array indices *floor(n/2)* to *n-1*. We will make use of this fact to quickly build up the heap from an array. Verify this for the example if you don't believe it. ;-)

Just a few math facts to brighten your day.
-->

一個有 *n* 個節點的堆積高度是 *h = floor(log_2(n))*. 這是因為堆積總是先把低層的填滿才去增加新的層數. 這個例子有 15 個節點, 所以高度是 `floor(log_2(15)) = floor(3.91) = 3`.

如果低階層都滿了, 則該階層含有 *2^h* 個節點. 而剩下的節點共有 *2^h - 1* 個. 讓我們把樹填滿: 最低層有 8 個節點, 的確是 `2^3 = 8`. 而其他層總共有 7 個節點, 即 `2^3 - 1 = 8 - 1 = 7`.

而整棵樹的總節點數 *n* 是 *2^(h+1) - 1*. 在此例中, `2^4 -1 = 16 - 1 = 15`.

在一個 *n* 個元素, 高度是 *h* 的堆積中最多有 *ceil(n/2^(h+1))* 個節點.

葉節點總是落在 *floor(n/2)* 到 *n-1* 的索引之間. 我們可以利用這點來快速將一個陣列做成堆積. 如果不相信自己證明看看囉. ;-)

<!--
## What can you do with a heap?

There are two primitive operations necessary to make sure the heap is a valid max-heap or min-heap after you insert or remove an element:

- `shiftUp()`: If the element is greater (max-heap) or smaller (min-heap) than its parent, it needs to be swapped with the parent. This makes it move up the tree.

- `shiftDown()`. If the element is smaller (max-heap) or greater (min-heap) than its children, it needs to move down the tree. This operation is also called "heapify". 

Shifting up or down is a recursive procedure that takes **O(log n)** time.

The other operations are built on these primitives. They are:

- `insert(value)`: Adds the new element to the end of the heap and then uses `shiftUp()` to fix the heap.

- `remove()`: Removes and returns the maximum value (max-heap) or the minimum value (min-heap). To fill up the hole that's left by removing the element, the very last element is moved to the root position and then `shiftDown()` fixes up the heap. (This is sometimes called "extract min" or "extract max".)

- `removeAtIndex(index)`: Just like `remove()` except it lets you remove any item from the heap, not just the root. This calls both `shiftDown()`, in case the new element is out-of-order with its children, and `shiftUp()`, in case the element is out-of-order with its parents.

- `replace(index, value)`: Assigns a smaller (min-heap) or larger (max-heap)  value to a node. Because this invalidates the heap property, it uses `shiftUp()` to patch things up. (Also called "decrease key" and "increase key".)

All of the above take time **O(log n)** because shifting up or down is the most expensive thing they do. There are also a few operations that take more time:

- `search(value)`. Heaps aren't built for efficient searches but the `replace()` and `removeAtIndex()` operations require the array index of the node, so you need to find that index somehow. Time: **O(n)**.

- `buildHeap(array)`: Converts an (unsorted) array into a heap by repeatedly calling `insert()`. If you’re smart about this, it can be done in **O(n)** time.

- [Heap sort](../Heap Sort/). Since the heap is really an array, we can use its unique properties to sort the array from low to high. Time: **O(n lg n).**

The heap also has a `peek()` function that returns the maximum (max-heap) or minimum (min-heap) element, without removing it from the heap. Time: **O(1)**.

> **Note:** By far the most common things you'll do with a heap are inserting new values with `insert()` and removing the maximum or minimum value with `remove()`. Both take **O(log n)** time. The other operations exist to support more advanced usage, such as building a priority queue where the "importance" of items can change after they've been added to the queue.

-->

## 你可以拿堆積來做什麼?

有兩個主要的操作, 可以確保堆積在插入或移除元素後還保持正確的最大堆積或者是最小堆積:

- `shiftUp()`: 如果該元素比其父節點大 (最大堆積) 或小 (最小堆積), 那這個元素要跟其父節點交換位置. 使元素往上移.

- `shiftDown()`: 如果該元素比其父節點小 (最大堆積) 或大 (最小堆積), 那這個元素要跟其父節點交換位置. 使元素往下移. 這個動作也稱為 "堆積化".

上下移動是一個遞迴流程, 佔用 **O(log n)** 時間.

其他的操作是以它們為基礎:

- `insert(value)`: 在堆積的最尾端插入一個元素並使用 `shiftUp()` 來調整它.

- `remove()`: 回傳並移除最大值 (最大堆積) 或 最小值 (最小堆積).  那要用哪個節點來替代被移除的節點呢? 我們先將最尾端的節點放到被移除節點的位置, 然後使用 `shiftDown()` 來調整堆積. (有時候這操作也稱為 "提取最小" 或 "提取最大".)

- `removeAtIndex(index)`:  跟 `remove()` 很像但是讓你可以移除指定的位置, 不只是根節點. 這方法會同時呼叫 `shiftDown()` 和 `shiftUp()` 來調整堆積.

- `replace(index, value)`: 覆寫一個節點值. 在最小堆積屬性中覆寫一個比該節點小的值或在最大堆積中覆寫比該節點大的值, 都會造成堆積屬性失效, 使用 `shiftUp()` 來調整. (此動作也被稱為 "減少鍵值" 或 "增加鍵值")

上述的操作都佔用 **O(log n)** 時間, 因為上移或下移是最佔用資源的操作.

還有一些佔更多時間的操作:

- `search(value)`: 堆積對於搜尋不算有效率, 但是因為 `replace()` 和 `removeAtIndex()` 操作需要知道節點的索引, 所以有時候還是得去搜尋. 時間: **O(n)**.

- `buildHeap(array)`: 要把一個未排序的陣列轉換成堆積需要一直重複的呼叫 `insert()`. 如果你更聰明, 這操作佔用 **O(n)** 時間.

- [堆積排序](../Heap Sort/). 因為堆積其實上是個陣列, 我們可以使用它的唯一屬性來把陣列從低到高排序. 時間: **O(n lg n).**

堆積同樣也有 `peek()` 函式用來回傳最大元素(最大堆積) 或 最小元素(最小堆積) 並不移除它們. 時間是 **O(1)**.

> **注意:** 到目前為止會最常使用的是使用 `insert()` 來插入一個元素, 和用 `remove()` 來移除最大或最小的元素. 這兩個操作都是 **O(log n)** 時間. 其他的操作支援了更進階的使用, 像是建立一個優先佇列並且 "最重要的元素" 可以被改變就算該元素已經存在於佇列中.

<!--
## Inserting into the heap

Let's go through an example insertion to see in more detail how this works. We'll insert the value `16` into this heap:

![The heap before insertion](Images/Heap1.png)

The array for this heap is `[ 10, 7, 2, 5, 1 ]`.

The first step when inserting a new item is to append it to the end of the array. The array becomes:

	[ 10, 7, 2, 5, 1, 16 ]

This corresponds to the following tree:

![The heap before insertion](Images/Insert1.png)

The `(16)` was added to the first available space on the last row.

Unfortunately, the heap property is no longer satisfied because `(2)` is above `(16)` and we want higher numbers above lower numbers. (This is a max-heap.)

To restore the heap property, we're going to swap `(16)` and `(2)`. 

![The heap before insertion](Images/Insert2.png)

We're not done yet because `(10)` is also smaller than `(16)`. We keep swapping our inserted value with its parent, until the parent is larger or we reach the top of the tree. This is called **shift-up** or **sifting** and is done after every insertion. It makes a number that is too large or too small "float up" the tree.

Finally, we get:

![The heap before insertion](Images/Insert3.png)

And now every parent is greater than its children again.

The time required for shifting up is proportional to the height of the tree so it takes **O(log n)** time. (The time it takes to append the node to the end of the array is only **O(1)**, so that doesn't slow it down.)
-->

## 堆積的插入元素

讓我們仔細來看看插入的動作流程是怎樣的. 如果我們要插入 `16` 到這個堆積:


![The heap before insertion](Images/Heap1.png)

這個堆積的陣列是: `[ 10, 7, 2, 5, 1 ]`.

插入元素的第一步是插入到陣列的最尾端. 變成這樣:

	[ 10, 7, 2, 5, 1, 16 ]

對應的樹狀結構是這樣:

![The heap before insertion](Images/Insert1.png)

`(16)` 插入到最下層第一個空位.

可惜的是, 堆積屬性失效了, 因為 `(2)` 在 `(16)` 的上方, 而我們希望大值在小值的上方. (這是一個最大堆積)

為了保持堆積屬性, 我們將 `(16)` 和 `(2)` 互換.

![The heap before insertion](Images/Insert2.png)

我們還沒完成喔, 因為 `(10)` 還是比 `(16)` 小. 我們不斷的將新插入的元素和它的父節點交換直到比父節點還小或已經是根節點. 這個流程稱為 **上移** 或 **sifting**. 每次插入新元素都會執行這個流程. 可以讓太大或大小的值 "向上移動".

最後, 我們得到:

![The heap before insertion](Images/Insert3.png)

現在每個父節點都比任何子節點還要大, 最大堆積的屬性再次成立.

上移所需的時間是 **O(log n)**. (因為直接在最尾端插入元素, 所以插入元素的時間是 **O(1)**, 並不影響整體流程的時間.)


<!--
## Removing the root

Let's remove `(10)` from this tree:

![The heap before removal](Images/Heap1.png)

What happens to the empty spot at the top?

![The root is gone](Images/Remove1.png)

When inserting, we put the new value at the end of the array. Here, we'll do the opposite: we're going to take the last object we have, stick it up on top of the tree, and restore the heap property. 

![The last node goes to the root](Images/Remove2.png)

Let's look at how to **shift-down** `(1)`. To maintain the heap property for this max-heap, we want to the highest number of top. We have two candidates for swapping places with: `(7)` and `(2)`. We choose the highest number between these three nodes to be on top. That is `(7)`, so swapping `(1)` and `(7)` gives us the following tree.

![The last node goes to the root](Images/Remove3.png)

Keep shifting down until the node doesn't have any children or it is larger than both its children. For our heap we only need one more swap to restore the heap property:

![The last node goes to the root](Images/Remove4.png)

The time required for shifting all the way down is proportional to the height of the tree so it takes **O(log n)** time. 

> **Note:** `shiftUp()` and `shiftDown()` can only fix one out-of-place element at a time. If there are multiple elements in the wrong place, you need to call these functions once for each of those elements.
-->

## 移除根節點

這次讓我們來移除 `(10)` 看看:

![The heap before removal](Images/Heap1.png)

這時候最上方的空位怎麼辦?

![The root is gone](Images/Remove1.png)

當我們插入元素時, 我們把新的元素放到陣列的最尾端. 這次我們反過來做: 我們將最尾端的元素移動到根節點的位置, 然後重新復原堆積的屬性.

![The last node goes to the root](Images/Remove2.png)

讓我們來看看如何 **下移** `(1)`. 為了保持最大堆積的屬性, 我們希望最大的數值會在最上方位置. 我們有兩個交換的候選 `(7)` 和 `(2)`. 選擇比較大的值來當交換的對象, 那就是 `(7)`, 所以把 `(1)` 跟  `(7)` 交換會得到:


![The last node goes to the root](Images/Remove3.png)

持續的交換節點直到 `(1)` 沒有任何子節點或者大於任何子節點. 在例子中我們只需要在交換一次就可以重新使得最大堆積屬性成立.

![The last node goes to the root](Images/Remove4.png)

<!--
The time required for shifting all the way down is proportional to the height of the tree so it takes **O(log n)** time. 

> **Note:** `shiftUp()` and `shiftDown()` can only fix one out-of-place element at a time. If there are multiple elements in the wrong place, you need to call these functions once for each of those elements.
-->

從頭開始向下移動的時間是 **O(log n)**.

> **注意:** `shiftUp()` 和 `shiftDown()` 每一次只能調整一個交換. 如果有很多個元素都在錯誤的地方. 你需要讓所有的元素都呼叫這些函式一次.

<!--
## Removing any node

The vast majority of the time you'll be removing the object at the root of the heap because that's what heaps are designed for.

However, it can be useful to remove an arbitrary element. This is a more general version of `remove()` and may involve either `shiftDown()` or `shiftUp()`.

Let's take the example tree again and remove `(7)`:

![The heap before removal](Images/Heap1.png)

As a reminder, the array is:

	[ 10, 7, 2, 5, 1 ]

As you know, removing an element could potentially invalidate the max-heap or min-heap property. To fix this, we swap the node that we're removing with the last element:

	[ 10, 1, 2, 5, 7 ]

The last element is the one that we'll return; we'll call `removeLast()` to remove it from the heap. The `(1)` is now out-of-order because it's smaller than its child, `(5)`, but sits higher in the tree. We call `shiftDown()` to repair this.

However, shifting down is not the only situation we need to handle -- it may also happen that the new element must be shifted up. Consider what happens if you remove `(5)` from the following heap:

![We need to shift up](Images/Remove5.png)

Now `(5)` gets swapped with `(8)`. Because `(8)` is larger than its parent, we need to call `shiftUp()`.
-->

## 刪除節點

大部分時候你會刪除根節點, 因為這就是堆積的設計目的.

但如果可以移除任意位置的元素, 那就更好啦. 我們需要一個更一般化的 `remove()`, 而這個方法可能會使用到 `shiftDown()` 或 `shiftUp()` 來調整堆積.

讓我們回顧一下範例中的樹, 而這此我們要移除 `(7)`:

![The heap before removal](Images/Heap1.png)

目前的堆積陣列是:

	[ 10, 7, 2, 5, 1 ]

你知道的, 移除元素可能會造成堆積屬性失效. 為了解決這個問題, 我們將要移除的元素和最尾端的元素作交換.

	[ 10, 1, 2, 5, 7 ]

現在最後一個元素就是我們要移除的, 呼叫 `removeLast()` 可以得到該元素並且移除它. 可是現在 `(1)` 比它的子節點 `(5)` 還要小, 所以我呼叫 `shiftDown()` 來調整.

無論如何, 往下移動並不是唯一的處理方式 -- 也是有可能需要的調整是向上移. 試試看如果要移除 `(5)` 會怎樣:

![We need to shift up](Images/Remove5.png)


這情況下 `(5)` 要跟 `(8)` 交換並移除. 但是 `(8)` 會比 `(7)` 還大, 所以需要 `shiftUp()`.

<!--
## Creating a heap from an array

It can be convenient to convert an array into a heap. This just shuffles the array elements around until the heap property is satisfied.

In code it would look like this:

```swift
  private mutating func buildHeap(fromArray array: [T]) {
    for value in array {
      insert(value)
    }
  }
```

We simply call `insert()` for each of the values in the array. Simple enough, but not very efficient. This takes **O(n log n)** time in total because there are **n** elements and each insertion takes **log n** time.

If you didn't gloss over the math section, you'd have seen that for any heap the elements at array indices *n/2* to *n-1* are the leaves of the tree. We can simply skip those leaves. We only have to process the other nodes, since they are parents with one or more children and therefore may be in the wrong order. 

In code:

```swift
  private mutating func buildHeap(fromArray array: [T]) {
    elements = array
    for i in (elements.count/2 - 1).stride(through: 0, by: -1) {
      shiftDown(index: i, heapSize: elements.count)
    }
  }
```

Here, `elements` is the heap's own array. We walk backwards through this array, starting at the first non-leaf node, and call `shiftDown()`. This simple loop puts these nodes, as well as the leaves that we skipped, in the correct order. This is known as Floyd's algorithm and only takes **O(n)** time. Win!
-->

## 從陣列獲得堆積

如果可以把陣列轉換成堆積了話, 那真方便. 要做的就是調整陣列元素的順序直到符合堆積屬性的條件.

程式碼像這樣:

```swift
  private mutating func buildHeap(fromArray array: [T]) {
    for value in array {
      insert(value)
    }
  }
```

簡單的對陣列中每個元素呼叫 `insert()` 方法. 非常輕鬆, 但是效能不好. 這方法共使用 **O(n log n)** 時間, 因為有 **n** 個元素而每一個佔用 **log n** 時間.

如果你有唸到"更多數學!"的部分, 那你已經知道陣列中索引介於 *n/2* 到 *n-1* 之間的都是葉節點, 我們可以直接跳過這些節點只處理其他的節點, 因為剩下來的都是父節點, 而可能在錯誤的位置.

程式碼:

```swift
  private mutating func buildHeap(fromArray array: [T]) {
    elements = array
    for i in (elements.count/2 - 1).stride(through: 0, by: -1) {
      shiftDown(index: i, heapSize: elements.count)
    }
  }
```

在這裡, `element` 是堆積本身的陣列. 我們從第一個非葉節點索引開始往回迭代, 然後對索引 `i` 呼叫 `shiftDown()`. 這個簡單的迴圈可以把元素放到正確的位置. 就正是所謂的 "Floyd 演算法", 而且只花 **O(n)** 時間. 贏了!

<!--
## Searching the heap

Heaps aren't made for fast searches, but if you want to remove an arbitrary element using `removeAtIndex()` or change the value of an element with `replace()`, then you need to obtain the index of that element somehow. Searching is one way to do this but it's kind of slow.

In a [binary search tree](../Binary Search Tree/) you can depend on the order of the nodes to guarantee a fast search. A heap orders its nodes differently and so a binary search won't work. You'll potentially have to look at every node in the tree.

Let's take our example heap again:

![The heap](Images/Heap1.png)

If we want to search for the index of node `(1)`, we could just step through the array `[ 10, 7, 2, 5, 1 ]` with a linear search.

But even though the heap property wasn't conceived with searching in mind, we can still take advantage of it. We know that in a max-heap a parent node is always larger than its children, so we can ignore those children (and their children, and so on...) if the parent is already smaller than the value we're looking for.

Let's say we want to see if the heap contains the value `8` (it doesn't). We start at the root `(10)`. This is obviously not what we're looking for, so we recursively look at its left and right child. The left child is `(7)`. That is also not what we want, but since this is a max-heap, we know there's no point in looking at the children of `(7)`. They will always be smaller than `7` and are therefore never equal to `8`. Likewise for the right child, `(2)`.

Despite this small optimization, searching is still an **O(n)** operation.

> **Note:** There is away to turn lookups into a **O(1)** operation by keeping an additional dictionary that maps node values to indices. This may be worth doing if you often need to call `replace()` to change the "priority" of objects in a [priority queue](../Priority Queue/) that's built on a heap.
-->

## 在堆積中搜尋

堆積並不是為了快速搜尋而設計的, 但如果你想要使用 `removeAtIndex()` 移除某個特別的元素, 或使用 `replace()` 來取代某個元素, 那你就需要先拿到該元素的索引. 只有一個方法可以做搜尋不過挺慢的.

在 [二元搜尋樹](../Binary Search Tree/) 中因為節點有序的特性可以保證快速搜尋. 但是堆積中的節點沒有這特性, 所以二元搜尋在這邊派不上用場. 你可能真的要一個一個元素去找.

再次看看我們的例子:

![The heap](Images/Heap1.png)

如果想找 `(1)` 的索引, 可以對 `[ 10, 7, 2, 5, 1 ]` 做線性搜尋.

雖然說堆積結構在設計時並沒有考慮搜尋, 但是我們還是有一點便宜可以占. 我們知道最大堆積的父節點永遠比子節點大, 所以如果當父節點已經比我們要搜尋的值還要小時, 那它的子節點也就不用走訪了.

讓我們來搜尋 `8`. 我們從根節點 `(10)` 開始. 這很明顯不是我們要的, 所以開始往子節點走, 左子節點是 `7`. 這也不是我們要的, 但這時候的堆積屬性是最大堆積, 所以我們知道這條子樹往下走不會找到我們要的元素. 因為再往下走的節點都小於 `7`. 對於右節點的 `2` 也是一樣道理. 所以很快就知道 `8` 不在陣列中.

儘管有這個小小的優化, 搜尋依然是 **O(n)** 的操作.

> **注意:** 如果利用一個字典來儲存每個節點的值和索引, 的確是可以把搜尋變為 **O(1)** 時間. 如果你時常呼叫 `replace()` 去改變在[優先佇列](../Priority Queue/)中元素的 "優先順序", 那這個處理方式是值得的. 

<!--
## The code

See [Heap.swift](Heap.swift) for the implementation of these concepts in Swift. Most of the code is quite straightforward. The only tricky bits are in `shiftUp()` and `shiftDown()`.

You've seen that there are two types of heaps: a max-heap and a min-heap. The only difference between them is in how they order their nodes: largest value first or smallest value first.

Rather than create two different versions, `MaxHeap` and `MinHeap`, there is just one `Heap` object and it takes an `isOrderedBefore` closure. This closure contains the logic that determines the order of two values. You've probably seen this before because it's also how Swift's `sort()` works.

To make a max-heap of integers, you write:
-->

## 程式碼

可以從 [Heap.swift](Heap.swift) 看到這些概念在 Swift 上的實作. 大部分的程式都挺直觀的. 只有 `shiftUp()` 和 `shiftDown()` 需要一點技巧.

你已經知道有兩種堆積屬性: 最大堆積和最小堆積. 這兩個屬性的差異在於節點的排列規則: 最大的在上還是最小的在上.

相較於創造兩個堆積型態, `MaxHeap` 和 `MinHeap`, 檔案中只有一個 `Heap` 型態帶有 `isOrderedBefore` 閉包. 這個閉包決定了兩個元素的先後順序. 你可以已經早就看過了, 因為這就是 Swift 中 `sort()` 得做法.


要製作一個整數的最大堆積, 你可以這樣撰寫:


```swift
var maxHeap = Heap<Int>(sort: >)
```

<!--
And to create a min-heap you write:
-->

而製作一個整數的最小堆積, 你可以這樣撰寫:

```swift
var minHeap = Heap<Int>(sort: <)
```

<!--
I just wanted to point this out, because where most heap implementations use the `<` and `>` operators to compare values, this one uses the `isOrderedBefore()` closure.
## See also
-->

我會特別提這點, 因為大部分的堆積實作都使用 `<` 或 `>` 運算子來比較元素值, 而在這檔案中使用 `isOrderedBefore()` 閉包.

## 相關閱讀


[維基百科上的堆積](https://en.wikipedia.org/wiki/Heap_%28data_structure%29)

*Written for the Swift Algorithm Club by [Kevin Randrup](http://www.github.com/kevinrandrup) and Matthijs Hollemans*
