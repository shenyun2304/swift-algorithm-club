# k-th Largest Element Problem (第 K 大元素問題)

<!--
You're given an integer array `a`. Write an algorithm that finds the *k*-th largest element in the array.

For example, the 1-st largest element is the maximum value that occurs in the array. If the array has *n* elements, the *n*-th largest element is the minimum. The median is the *n/2*-th largest element.
-->

給定一個整數的陣列 `a`. 寫一個演算法來找到第 *k* 大的元素.

舉例來說, 第 1 大的元素就是陣列中的最大值. 如果陣列有 *n* 個元素, 那第 *n* 大的元素就是最小值. 而中位數會是第 *n/2* 大的元素.

<!--
## The naive solution

The following solution is semi-naive. Its time complexity is **O(n log n)** since it first sorts the array, and therefore also uses additional **O(n)** space.
-->

## 天真的解法

以下這個是半天真的解法. 時間複雜度是 **O(n log n)**, 因為它先對陣列排序, 而空間複雜度是 **O(n)**.

```swift
func kthLargest(a: [Int], k: Int) -> Int? {
  let len = a.count
  if k > 0 && k <= len {
    let sorted = a.sort()
    return sorted[len - k]
  } else {
    return nil
  }
}
```

<!--
The `kthLargest()` function takes two parameters: the array `a` consisting of integers, and `k`. It returns the *k*-th largest element.

Let's take a look at an example and run through the algorithm to see how it works. Given `k = 4` and the array:
-->

`kthLargest()` 函式接收兩個參數: `a` 整數陣列, 和 `k`. 回傳第 *k* 大的元素.

讓我來看一下這個演算法. 給定 `k = 4` 和以下這個陣列:

```swift
[ 7, 92, 23, 9, -1, 0, 11, 6 ]
```

<!--
Initially there's no direct way to find the k-th largest element, but after sorting the array it's rather straightforward. Here's the sorted array:
-->

一開始, 沒有直接的做法可以找到第 k 大的元素, 但是在排序後, 就非常直接了. 排序後的陣列:

```swift
[ -1, 0, 6, 7, 9, 11, 23, 92 ]
```

<!--
Now, all we must do is take the value at index `a.count - k`:
-->

現在, 我們要做的就是拿到索引 `a.count - k`:

```swift
a[a.count - k] = a[8 - 4] = a[4] = 9
```

<!--
Of course, if you were looking for the k-th *smallest* element, you'd use `a[k]`.
-->

當然, 如果你要的是第 k *小* 的元素, 你應該使用 `a[k]`.

<!--
## A faster solution

There is a clever algorithm that combines the ideas of [binary search](../Binary Search/) and [quicksort](../Quicksort/) to arrive at an **O(n)** solution.

Recall that binary search splits the array in half over and over again, to quickly narrow in on the value you're searching for. That's what we'll do here too.

Quicksort also splits up arrays. It uses partitioning to move all smaller values to the left of the pivot and all greater values to the right. After partitioning around a certain pivot, that pivot value will already be in its final, sorted position. We can use that to our advantage here.

Here's how it works: We choose a random pivot, partition the array around that pivot, then act like a binary search and only continue in the left or right partition. This repeats until we've found a pivot that happens to end up in the *k*-th position.

Let's look at the original example again. We're looking for the 4-th largest element in this array:
-->

## 快速的解法

有一個很聰明的演算法, 結合了 [二元搜尋](../Binary Search/) 和 [快速排序](../Quicksort/), 達到 **O(n)** 時間.

回想按原搜尋把陣列對半對半的切割, 快速地縮小你搜尋的範圍. 我們在這邊也要這樣做.

快速排序也一樣是分割陣列. 它利用分割來把所有小的值往樞紐的左邊移動, 而大的往樞紐的右邊. 在結束之後, 這個樞紐的位置一定在他最後, 而且是有序的位置上, 我們可以在這邊利用這個優點.

原理是這樣: 我們選一個隨機的樞紐點, 然後將陣列依照該樞紐分割, 然後向二元搜尋一樣只取左半或右半邊. 一直重複直到我們發現該樞紐的索引就是我們要的第 *k* 個位置.

讓我們再看一次原始陣列. 要找到第 4 大的元素:

	[ 7, 92, 23, 9, -1, 0, 11, 6 ]

<!--
The algorithm is a bit easier to follow if we look for the k-th *smallest* item instead, so let's take `k = 4` and look for the 4-th smallest element.

Note that we don't have to sort the array first. We pick one of the elements at random to be the pivot, let's say `11`, and partition the array around that. We might end up with something like this:
-->

這個演算法去找第 k *小* 的元素會簡單一點點, 所以讓我們取 `k = 4`, 來找到第 4 小的元素.

注意到我們一開始並沒有將陣列排序. 我們隨機找一個元素當樞紐, 假設是 `11` 好了, 然後將陣列依照樞紐分割. 處理完後像這樣:


	[ 7, 9, -1, 0, 6, 11, 92, 23 ]
	 <------ smaller    larger -->

<!--
As you can see, all values smaller than `11` are on the left; all values larger are on the right. The pivot value `11` is now in its final place. The index of the pivot is 5, so the 4-th smallest element must be in the left partition somewhere. We can ignore the rest of the array from now on:
-->

可以看到, 所有小於 `11` 的元素在左邊; 大於 `11` 的元素在右邊. 而 `11` 就在它在有序化後最後的位置. 索引是 `5`, 所以第 4 小的元素一定在左邊的部分. 我們可以忽略其餘的部分.

	[ 7, 9, -1, 0, 6, x, x, x ]

<!--
Again let's pick a random pivot, let's say `6`, and partition the array around it. We might end up with something like this:
-->

我們再次隨機挑一個樞紐, 假設是 `6`, 然後一樣分割陣列, 做完後:

	[ -1, 0, 6, 9, 7, x, x, x ]

<!--
Pivot `6` ended up at index 2, so obviously the 4-th smallest item must be in the right partition. We can ignore the left partition:
-->

樞紐 `6` 最後在索引 2 的地方, 所以第 4 小的元素很明顯在右邊的部分. 我們可以忽略左邊的分割:

	[ x, x, x, 9, 7, x, x, x ]

<!--
Again we pick a pivot value at random, let's say `9`, and partition the array:
-->

我們再一次隨便選樞紐, 讓我們假設是 `9`, 然後將陣列分割:

	[ x, x, x, 7, 9, x, x, x ]

<!--
The index of pivot `9` is 4, and that's exactly the *k* we're looking for. We're done! Notice how this only took a few steps and we did not have to sort the array first.

The following function implements these ideas:
-->

這時候 `9` 的索引是 4, 而這正是我們要找的第 *k* 個元素. 我們完成了! 注意到我們只經過了很少的步驟, 然後並沒有去做排序陣列的動作.

接下來的函式實現了這個概念:

```swift
public func randomizedSelect<T: Comparable>(array: [T], order k: Int) -> T {
  var a = array
  
  func randomPivot<T: Comparable>(inout a: [T], _ low: Int, _ high: Int) -> T {
    let pivotIndex = random(min: low, max: high)
    swap(&a, pivotIndex, high)
    return a[high]
  }

  func randomizedPartition<T: Comparable>(inout a: [T], _ low: Int, _ high: Int) -> Int {
    let pivot = randomPivot(&a, low, high)
    var i = low
    for j in low..<high {
      if a[j] <= pivot {
        swap(&a, i, j)
        i += 1
      }
    }
    swap(&a, i, high)
    return i
  }

  func randomizedSelect<T: Comparable>(inout a: [T], _ low: Int, _ high: Int, _ k: Int) -> T {
    if low < high {
      let p = randomizedPartition(&a, low, high)
      if k == p {
        return a[p]
      } else if k < p {
        return randomizedSelect(&a, low, p - 1, k)
      } else {
        return randomizedSelect(&a, p + 1, high, k)
      }
    } else {
      return a[low]
    }
  }
  
  precondition(a.count > 0)
  return randomizedSelect(&a, 0, a.count - 1, k)
}
```

<!--
To keep things readable, the functionality is split into three inner functions:

- `randomPivot()` picks a random number and puts it at the end of the current partition (this is a requirement of the Lomuto partitioning scheme, see the discussion on [quicksort](../Quicksort/) for more details).

- `randomizedPartition()` is Lomuto's partitioning scheme from quicksort. When this completes, the randomly chosen pivot is in its final sorted position in the array. It returns the array index of the pivot.

- `randomizedSelect()` does all the hard work. It first calls the partitioning function and then decides what to do next. If the index of the pivot is equal to the *k*-th number we're looking for, we're done. If `k` is less than the pivot index, it must be in the left partition and we'll recursively try again there. Likewise for when the *k*-th number must be in the right partition.

Pretty cool, huh? Normally quicksort is an **O(n log n)** algorithm, but because we only partition smaller and smaller slices of the array, the running time of `randomizedSelect()` works out to **O(n)**.

> **Note:** This function calculates the *k*-th smallest item in the array, where *k* starts at 0. If you want the *k*-th largest item, call it with `a.count - k`.
-->

為了保持可讀性, 這個函式分割到幾個子函式中:

- `randomPivot()` 隨機選擇一個數字並把它放到當前分割的尾端. (查看 [快速排序](../Quicksort/) 的討論以獲得詳細)
- `randomizedPartition()` 當處理完後, 那個隨機選取的樞紐就會在它有序化後最終的位置. 回傳該樞紐的索引.
- `randomizedSelect()` 先呼叫了分割的函式並決定接下來要做什麼. 如果樞紐的索引就是我們要的第 *k* 個, 那我們就完成了. 如果 `k` 比樞紐索引還小, 那就取左邊分割並遞迴的在處理一次; 而如果 `k` 比樞紐大, 那就取右邊做一樣的動作.

很酷, 不是嗎? 正常來說快速排序是個 **O(n log n)** 的演算法, 但因為我們一直把陣列越分越小, `ramdomizedSelect()` 的執行時間是 **O(n)**.

> **注意:** 這函式計算第 *k* 小的元素, 而 *k* 是從 0 開始. 如果你想要第 *k* 大的元素, 使用 `a.count - k` 來呼叫.

*Written by Daniel Speiser. Additions by Matthijs Hollemans.*
