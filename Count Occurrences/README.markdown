# Count Occurrences (發生次數)

<!--
Goal: Count how often a certain value appears in an array.

The obvious way to do this is with a [linear search](../Linear Search/) from the beginning of the array until the end, keeping count of how often you come across the value. That is an **O(n)** algorithm.

However, if the array is sorted you can do it much faster, in **O(log n)** time, by using a modification of [binary search](../Binary Search/).

Let's say we have the following array:
-->

目標: 計算某個值出現在陣列裡的次數.

最明顯的方法是使用 [線性搜尋](../Linear Search/) 從陣列的開始走訪到結尾, 並記錄遇到該值的次數. 這樣是 **O(n)** 時間.

那如果陣列已經排序過, 那使用 [二元搜尋](../Binary Search/) 時間是 **O(log n)**, 

讓我們看看以下陣列:

	[ 0, 1, 1, 3, 3, 3, 3, 6, 8, 10, 11, 11 ]

<!--
If we want to know how often the value `3` occurs, we can do a regular binary search for `3`. That could give us any of these four indices:
-->

如果我們要知道 `3` 出現的次數, 我們可以對 `3` 做一般的二元搜尋. 我們可能獲得以下其中某個索引:

	[ 0, 1, 1, 3, 3, 3, 3, 6, 8, 10, 11, 11 ]
	           *  *  *  *

<!--
But that still doesn't tell you how many other `3`s there are. To find those other `3`s, you'd still have to do a linear search to the left and a linear search to the right. That will be fast enough in most cases, but in the worst case -- when the array consists of nothing but `3`s -- it still takes **O(n)** time.

The trick is to use two binary searches, one to find where the `3`s start (the left boundary), and one to find where they end (the right boundary).

In code this looks as follows:
-->

但是這樣並沒有告訴我們有多少個 `3`. 為了要找到其他的 `3`, 還需要對找到索引的 `3` 前後使用線性搜尋. 這樣做比一般情況還要快得多, 但是在最糟糕的情形下 -- 陣列裡通通都是 `3` -- 那還是 **O(n)** 時間.

有個技巧是使用兩個二元搜尋, 一個找最左端 `3` 的索引, 一個找最右端 `3` 的索引.

程式碼:

```swift
func countOccurrencesOfKey(_ key: Int, inArray a: [Int]) -> Int {
  func leftBoundary() -> Int {
    var low = 0
    var high = a.count
    while low < high {
      let midIndex = low + (high - low)/2
      if a[midIndex] < key {
        low = midIndex + 1
      } else {
        high = midIndex
      }
    }
    return low
  }
  
  func rightBoundary() -> Int {
    var low = 0
    var high = a.count
    while low < high {
      let midIndex = low + (high - low)/2
      if a[midIndex] > key {
        high = midIndex
      } else {
        low = midIndex + 1
      }
    }
    return low
  }
  
  return rightBoundary() - leftBoundary()
}
```

<!--
Notice that the helper functions `leftBoundary()` and `rightBoundary()` are very similar to the [binary search](../Binary Search/) algorithm. The big difference is that they don't stop when they find the search key, but keep going.

To test this algorithm, copy the code to a playground and then do:
-->

注意 `leftBoundary()` 和 `rightBoundary()` 和 [二元搜尋](../Binary Search/) 很像, 差異在於它們並沒有再找到值後就停下來, 而是繼續搜尋到最後.

你可以在 playground 中測試這個演算法:


```swift
let a = [ 0, 1, 1, 3, 3, 3, 3, 6, 8, 10, 11, 11 ]

countOccurrencesOfKey(3, inArray: a)  // returns 4
```

<!--
> **Remember:** If you use your own array, make sure it is sorted first!

Let's walk through the example. The array is:
-->

> **提醒:** 如果你自定義陣列, 記得要先排序喔!

讓我們看看這個範例:

	[ 0, 1, 1, 3, 3, 3, 3, 6, 8, 10, 11, 11 ]

<!--
To find the left boundary, we start with `low = 0` and `high = 12`. The first mid index is `6`:
-->

為了找到左界線, 我們從 `low = 0` 和 `high = 12` 開始. 第一個 mid 索引是 `6`:


	[ 0, 1, 1, 3, 3, 3, 3, 6, 8, 10, 11, 11 ]
	                    *

<!--
With a regular binary search you'd be done now, but here we're not just looking whether the value `3` occurs or not -- instead, we want to find where it occurs *first*.

Since this algorithm follows the same principle as binary search, we now ignore the right half of the array and calculate the new mid index:
-->

正常的二元搜尋下, 已經結束了, 但是我們不是只要找 `3` 存不存在或索引多少 -- 相反的, 我們想要找到它 *第一次* 出現的地方.

依照二元搜尋的規則繼續做下去, 我們會忽略右半邊的值並且再次計算新的 mid 索引:


	[ 0, 1, 1, 3, 3, 3 | x, x, x, x, x, x ]
	           *

<!--
Again, we've landed on a `3`, and it's the very first one. But the algorithm doesn't know that, so we split the array again:
-->
這一次我們 mid 索引在 `3` 的位置, 這的確是第一個發生的位置. 但是演算法並不知道, 並且繼續做下去:


	[ 0, 1, 1 | x, x, x | x, x, x, x, x, x ]
	     *

<!--
Still not done. Split again, but this time use the right half:
-->

還沒完, 這次選擇右邊區塊:

	[ x, x | 1 | x, x, x | x, x, x, x, x, x ]
	         *

<!--
The array cannot be split up any further, which means we've found the left boundary, at index 3.

Now let's start over and try to find the right boundary. This is very similar, so I'll just show you the different steps:
-->

現在無法再分割陣列了, 表示找到了左界線, 落在索引 3 的位置.

現在用同樣的方式來找右界線:

	[ 0, 1, 1, 3, 3, 3, 3, 6, 8, 10, 11, 11 ]
	                    *

	[ x, x, x, x, x, x, x | 6, 8, 10, 11, 11 ]
	                              *

	[ x, x, x, x, x, x, x | 6, 8, | x, x, x ]
	                           *

	[ x, x, x, x, x, x, x | 6 | x | x, x, x ]
	                        *

<!--
The right boundary is at index 7. The difference between the two boundaries is 7 - 3 = 4, so the number `3` occurs four times in this array.

Each binary search took 4 steps, so in total this algorithm took 8 steps. Not a big gain on an array of only 12 items, but the bigger the array, the more efficient this algorithm becomes. For a sorted array with 1,000,000 items, it only takes 2 x 20 = 40 steps to count the number of occurrences for any particular value.

By the way, if the value you're looking for is not in the array, then `rightBoundary()` and `leftBoundary()` return the same value and so the difference between them is 0.

This is an example of how you can modify the basic binary search to solve other algorithmic problems as well. Of course, it does require that the array is sorted.
-->

右界線會落在索引 7 的位置. 所以兩個界限的差為 7 - 3 = 4, 表示 `3` 在這個陣列中發生的次數是 4 次.

每個二元搜尋使用了 4 個步驟, 所以此演算法總共走了 8 個步驟. 對於只有 12 個元素的陣列來看, 並沒有很大的進步, 但隨著陣列越大, 這個演算法優化的差異也會越來越明顯. 對於一個有序 1,000,000 元素的陣列, 只需要 2 x 20 = 40 個步驟就可以找到任何一個值得發生次數.

順帶一提, 如果陣列中沒有該搜尋的元素, 則 `rightBoundary()` 和 `leftBoundary()` 會回傳一樣的值, 界限差會是 0.

這個例子顯示你如何使用基礎的二元搜尋來解決其他演算法的問題. 當然, 這個陣列是需要排序過的.


*Written for Swift Algorithm Club by Matthijs Hollemans*
