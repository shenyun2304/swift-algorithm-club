# Heap Sort (堆積排序)

<!--
Sorts an array from low to high using a heap.

A [heap](../Heap/) is a partially sorted binary tree that is stored inside an array. The heap sort algorithm takes advantage of the structure of the heap to perform a fast sort.

To sort from lowest to highest, heap sort first converts the unsorted array to a max-heap, so that the first element in the array is the largest.

Let's say the array to sort is:
-->

使用堆積來將陣列做遞增排序.

[堆積](../Heap/) 是個部分排序的二元樹, 並且藏在陣列之中. 堆積排序使用了堆積結構的優點, 讓排序更加快速.

要做遞增排序, 堆積排序先將無序的陣列轉換成最大堆積, 這樣陣列的第一個元素就會是最大的元素.


	[ 5, 13, 2, 25, 7, 17, 20, 8, 4 ]

<!--
This is first turned into a max-heap that looks like this:
-->

先把這個陣列轉成最大堆積.

![The max-heap](Images/MaxHeap.png)

<!--
The heap's internal array is then:
-->

這個最大堆積轉換成陣列是這樣:

	[ 25, 13, 20, 8, 7, 17, 2, 5, 4 ]

<!--
That's hardly what you'd call sorted! But now the sorting process starts: we swap the first element (index *0*) with the last one at index *n-1*, to get:
-->

這跟我們所想的排序還差滿多的! 但現在排序流程才正要開始: 我們將第一 (索引 *0*) 和最後 (索引 *n-1*) 的元素做交換:

	[ 4, 13, 20, 8, 7, 17, 2, 5, 25 ]
	  *                          *

<!--
Now the new root node, `4`, will be smaller than its children, so we fix up the max-heap up to element *n-2* using the *shift down* or "heapify" procedure. After repairing the heap, the new root is now the second-largest item in the array:
-->

現在根節點是 `4`, 但這樣就會讓根節點小於它的子節點 (違反最大堆積), 所以我們從 *0* 到 *n-2* 索引中的元素以 *shift down* 或 *heapify* 操作來重新構成最大堆積. 在重構之後, 新的根節點就是陣列中第二大的元素:

	[20, 13, 17, 8, 7, 4, 2, 5 | 25]

<!--
Important: When we fix the heap, we ignore the last item at index *n-1*. That now contains the array's maximum value, so it is in its final sorted place already. The `|` bar indicates where the sorted portion of the array begins. We'll leave that part of the array alone from now on.

Again, we swap the first element with the last one (this time at index *n-2*):
-->

重要: 當我們在重構堆積時, 我們忽略了最後第 *n-1* 的元素. 因為那已經是陣列中最大的元素, 所以該元素已經在它該在的位置了. `|` 表示無序與有序的分隔. 我們將不再處理 `l` 之後的元素.

再一次, 我們把第一和最後 (這時候是索引 *n-2*) 的元素交換:

	[5, 13, 17, 8, 7, 4, 2, 20 | 25]
	 *                      *

<!--
And fix up the heap to make it valid max-heap again:
-->

然後再重構最大堆積:

	[17, 13, 5, 8, 7, 4, 2 | 20, 25]

<!--
As you can see, the largest items are making their way to the back. We repeat this process until we arrive at the root node and then the whole array is sorted.

> **Note:** This process is very similar to [selection sort](../Selection Sort/), which repeatedly looks for the minimum item in the remainder of the array. Extracting the minimum or maximum value is what heaps are good at.

Performance of heap sort is **O(n lg n)** in best, worst, and average case. Because we modify the array directly, heap sort can be performed in-place. But it is not a stable sort: the relative order of identical elements is not preserved.

Here's how you can implement heap sort in Swift:
-->

可以看到, 大的元素一直被推到後面. 我們一直重複這個流程直到最後一個元素, 屆時可以保整陣列已經遞增排序完成.

> **注意:** 這個過程和 [選擇排序](../Selection Sort/) -- 會重複的查找剩餘陣列中最小的元素 -- 滿像的. 要提取最大或最小值是堆積結構最擅長的.

堆積排序在任何情況下, 複雜度是 **O(n lg n)**. 因為我們可以直接修改陣列, 堆積排序可以在陣列中執行. 但它並不是個穩定的排序: 相等元素之間的相對順序並不會被保留.

在 Swift 中實現堆積排序:

```swift
extension Heap {
  public mutating func sort() -> [T] {
    for i in (elements.count - 1).stride(through: 1, by: -1) {
      swap(&elements[0], &elements[i])
      shiftDown(index: 0, heapSize: i)
    }
    return elements
  }
}
```

<!--
This adds a `sort()` function to our [heap](../Heap/) implementation. To use it, you would do something like this:
-->

在我們的 [堆積](../Heap/) 加上 `sort()` 實現. 使用上, 會像這樣用:

```swift
var h1 = Heap(array: [5, 13, 2, 25, 7, 17, 20, 8, 4], sort: >)
let a1 = h1.sort()
```

<!--
Because we need a max-heap to sort from low-to-high, you need to give `Heap` the reverse of the sort function. To sort `<`, the `Heap` object must be created with `>` as the sort function. In other words, sorting from low-to-high creates a max-heap and turns it into a min-heap.

We can write a handy helper function for that:
-->

依照這個 `sort()` 函式的寫法, 我們要創建一個最大堆積來獲得遞增排序, 相對的, 我們需要最小堆積來獲得遞減排序. 換句話說, 我們需要先將陣列依照我們需要的排序來創建對應的堆積結構, 然後再做排序.

我們可以寫個好用的函式來辦到:

```swift
public func heapsort<T>(a: [T], _ sort: (T, T) -> Bool) -> [T] {
  let reverseOrder = { i1, i2 in sort(i2, i1) }
  var h = Heap(array: a, sort: reverseOrder)
  return h.sort()
}
```

*Written for Swift Algorithm Club by Matthijs Hollemans*
