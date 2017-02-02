# Quicksort (快速排序)

<!--
Goal: Sort an array from low to high (or high to low).

Quicksort is one of the most famous algorithms in history. It was invented way back in 1959 by Tony Hoare, at a time when recursion was still a fairly nebulous concept.

Here's an implementation in Swift that should be easy to understand:
-->

目標: 將陣列做遞增或遞減排序.

快速排序是歷史上有名的演算法. 它是由 Tony Hoare 在 1959 年發明的, 在那時候遞迴還是一個模糊的概念.

這是個用 Swift 實作的程式, 應該不難了解: 

```swift
func quicksort<T: Comparable>(_ a: [T]) -> [T] {
  guard a.count > 1 else { return a }

  let pivot = a[a.count/2]
  let less = a.filter { $0 < pivot }
  let equal = a.filter { $0 == pivot }
  let greater = a.filter { $0 > pivot }
  
  return quicksort(less) + equal + quicksort(greater)
}
```

<!--
Put this code in a playground and test it like so:
-->

在 playground 中測試看看:

```swift
let list = [ 10, 0, 3, 9, 2, 14, 8, 27, 1, 5, 8, -1, 26 ]
quicksort(list)
```

<!--
Here's how it works. When given an array, `quicksort()` splits it up into three parts based on a "pivot" variable. Here, the pivot is taken to be the element in the middle of the array (later on you'll see other ways to choose the pivot).

All the elements less than the pivot go into a new array called `less`. All the elements equal to the pivot go into the `equal` array. And you guessed it, all elements greater than the pivot go into the third array, `greater`. This is why the generic type `T` must be `Comparable`, so we can compare the elements with `<`, `==`, and `>`.

Once we have these three arrays, `quicksort()` recursively sorts the `less` array and the `right` array, then glues those sorted subarrays back together with the `equal` array to get the final result. 
-->

是這樣運作的. 先給定一個陣列, `quicksort()` 依照 "樞紐" 將他分割成三份. 此例中, 樞紐取陣列中間的元素 (待會你會看另一個選取樞紐的方法)

所以比樞紐還小的元素會被分割到 `less` 的陣列中. 和樞紐相等的會分割到 `equal` 陣列. 比樞紐大的會分割到 `greater` 陣列. 這就是為什麼泛型 `T` 一定要實現 `Comparable`, 因為這樣我才可以用 `<`, `>` 和 `==` 來比對元素.

一但我們有了這三個陣列, `quicksort()` 遞迴的分割 `less` 和 `greater` 陣列, 然後把這些分割後的子陣列和 `equal` 黏起來得到最後的結果.

<!--
## An example

Let's walk through the example. The array is initially:
-->

## 範例

來個陣列:

	[ 10, 0, 3, 9, 2, 14, 8, 27, 1, 5, 8, -1, 26 ]

<!--
First, we pick the pivot element. That is `8` because it's in the middle of the array. Now we split the array into the less, equal, and greater parts:
-->

首先, 我們選一個樞紐元素. 就是 `8`, 因為它在陣列的中間. 現在我們將陣列分割成 `less`, `equal` 和 `greater` 三個部分:

	less:    [ 0, 3, 2, 1, 5, -1 ]
	equal:   [ 8, 8 ]
	greater: [ 10, 9, 14, 27, 26 ]

<!--
This is a good split because `less` and `equal` roughly contain the same number of elements. So we've picked a good pivot that chopped the array right down the middle.

Note that the `less` and `greater` arrays aren't sorted yet, so we call `quicksort()` again to sort those two subarrays. That does the exact same thing: pick a pivot and split the subarray into three even smaller parts.

Let's just take a look at the `less` array:
-->

這看起來不錯, 因為 `less` 和 `greater` 元素個數差不多. 也就是說我們選了一個不錯的樞紐.

注意到這時候 `less` 和 `greater` 尚未排序, 所以我們再次呼叫 `quicksort()` 去排序兩個子陣列. 一樣會在子陣列裡選擇一個樞紐, 然後將子陣列分為三個更小的陣列.

讓我們來看看 `less` 陣列:


	[ 0, 3, 2, 1, 5, -1 ]

<!--
The pivot element is the one in the middle, `1`. (You could also have picked `2`, it doesn't matter.) Again, we create three subarrays around the pivot:
-->


樞紐元素是中間的那個 `1`. (你也可以選 `2`, 沒差)
再一次, 我們依照樞紐分割成三個子陣列:

	less:    [ 0, -1 ]
	equal:   [ 1 ]
	greater: [ 3, 2, 5 ]

<!--
We're not done yet and `quicksort()` again is called recursively on the `less` and `greater` arrays. Let's look at `less` again:
-->
我們還沒完成, 所以再次對 `less`, `greater` 呼叫 `quicksort()`.

再來看看這次的 `less`:

	[ 0, -1 ]

<!--
As pivot we pick `-1`. Now the subarrays are:
-->

這次樞紐選 `-1`. 現在子陣列分割成這樣:


	less:    [ ]
	equal:   [ -1 ]
	greater: [ 0 ]

<!--
The `less` array is empty because there was no value smaller than `-1`; the other arrays contain a single element each. That means we're done at this level of the recursion, and we go back up to sort the previous `greater` array.

That `greater` array was:
-->

`less` 陣列是空的因為沒有比 `-1` 更小的元素; 另外兩個陣列各含有一個元素. 這表示在這層遞迴我們完成了, 所以回去排序 `greater` 陣列.

`greater` 是這樣:

	[ 3, 2, 5 ]

<!--	
This works just the same way as before: we pick the middle element `2` as the pivot and fill up the subarrays:
-->
這和之前一樣: 我們選了中間元素 `2` 來當樞紐來切割子陣列:

	less:    [ ]
	equal:   [ 2 ]
	greater: [ 3, 5 ]

<!--
Note that here it would have been better to pick `3` as the pivot -- we would have been done sooner. But now we have to recurse into the `greater` array again to make sure it is sorted. This is why picking a good pivot is important. When you pick too many "bad" pivots, quicksort actually becomes really slow. More on that below.

When we partition the `greater` subarray, we find:
-->

注意到這邊其實選 `3` 當樞紐會比較好 -- 我們很快會做到這點. 所以我們需要再次遞迴 `greater` 陣列來排序. 這就是為什麼選一個好的樞紐很重要. 如果你選了一個 "糟糕" 的樞紐, 快速排序實際上會變得非常慢. 下面會再提到.

當我們再次分割 `greater` 陣列, 得到:

	less:    [ 3 ]
	equal:   [ 5 ]
	greater: [ ]

<!--
And now we're done at this level of the recursion because we can't split up the arrays any further.

This process repeats until all the subarrays have been sorted. In a picture:
-->

到這層就無法再繼續分割, 所以遞迴就結束了.

這個流程一直持續到所有的子陣列都是有序的. 如圖:


![Example](Images/Example.png)

<!--
Now if you read the colored boxes from left to right, you get the sorted array:
-->

那現在從左到右把紅色方塊集合起來, 你會得到有序陣列:

	[ -1, 0, 1, 2, 3, 5, 8, 8, 9, 10, 14, 26, 27 ]

<!--
This shows that `8` was a good initial pivot because it appears in the middle of the sorted array too.

I hope this makes the basic principle clear of how quicksort works. Unfortunately, this version of quicksort isn't very quick, because we `filter()` the same array three times. There are more clever ways to split up the array.
-->

看得出來 `8` 當第一個樞紐是很棒的, 因為他在排序後也是在陣列的中間.

我想這已經把快速排序的基礎原則交代清楚了. 可惜的是, 這個版本的快速排序並沒有很快, 因為我們用 `filter()` 對同一個陣列執行了三次. 有更聰明的方式來分割陣列.

<!--
## Partitioning

Dividing the array around the pivot is called *partitioning* and there are a few different partitioning schemes.

If the array is,
-->

## 分割

有幾種不同的分割方案.

以這個陣列為例:

	[ 10, 0, 3, 9, 2, 14, 8, 27, 1, 5, 8, -1, 26 ]

<!--
and we choose the middle element `8` as a pivot then after partitioning the array will look like this:
-->

我們選擇中間元素 `8` 當作樞紐, 分割後的陣列像這樣:


	[ 0, 3, 2, 1, 5, -1, 8, 8, 10, 9, 14, 27, 26 ]
	  -----------------        -----------------
	  all elements < 8         all elements > 8

<!--
The key thing to realize is that after partitioning the pivot element is in its final sorted place already. The rest of the numbers are not sorted yet, they are simply partitioned around the pivot value. Quicksort partitions the array many times over, until all the values are in their final places.

There is no guarantee that partitioning keeps the elements in the same relative order, so after partitioning around pivot `8` you could also end up with something like this:
-->

關鍵注意到這個樞紐元素已經在它排序後的位置了. 其他的元素還沒有被排序, 他們只是很間單的被分類.
快速搜尋會將陣列分割很多次, 直到所有的元素都在他們最後有序的位置.

分割動作並不保證子陣列中的元素會保持原本的相對順序, 所以依照 `8` 來分割可能結果是這樣:

	[ 3, 0, 5, 2, -1, 1, 8, 8, 14, 26, 10, 27, 9 ]

<!--
The only guarantee is that to the left of the pivot are all the smaller elements and to the right are all the larger elements. Because partitioning can change the original order of equal elements, quicksort does not produce a "stable" sort (unlike [merge sort](../Merge Sort/), for example). Most of the time that's not a big deal.
-->

唯一保證的是樞紐的左邊元素一定都小於他, 而右邊一定都大於它. 因為分割會改變原本的陣列的順序, 快速排序不是個 "穩定" 排序 (不像 [合併排序](../Merge Sort/) 一樣). 不過大部分的時候這不重要.

<!--
## Lomuto's partitioning scheme

In the first example of quicksort I showed you, partitioning was done by calling Swift's `filter()` function three times. That is not very efficient. So let's look at a smarter partitioning algorithm that works *in place*, i.e. by modifying the original array.

Here's an implementation of Lomuto's partitioning scheme in Swift:
-->

## Lomuto 分割方案

在一開始的範例中, 是用 Swift 原生的 `filter()` 函式三次來做分割. 那並不算很有效率. 所以來看看更聰明的分割演算法. 即, 調整原始陣列.

這是 Lomuto 分割方案在 Swift 中的實現:


```swift
func partitionLomuto<T: Comparable>(_ a: inout [T], low: Int, high: Int) -> Int {
  let pivot = a[high]
  
  var i = low
  for j in low..<high {
    if a[j] <= pivot {
      (a[i], a[j]) = (a[j], a[i])
      i += 1
    }
  }
  
  (a[i], a[high]) = (a[high], a[i])
  return i
}
```

<!--
To test this in a playground, do:
-->

在 playground 中測試:

```swift
var list = [ 10, 0, 3, 9, 2, 14, 26, 27, 1, 5, 8, -1, 8 ]
let p = partitionLomuto(&list, low: 0, high: list.count - 1)
list  // show the results
```

<!--
Note that `list` needs to be a `var` because `partitionLomuto()` directly changes the contents of the array (it is passed as an `inout` parameter). That is much more efficient than allocating a new array object.

The `low` and `high` parameters are necessary because when this is used inside quicksort, you don't always want to (re)partition the entire array, only a limited range that becomes smaller and smaller.

Previously we used the middle array element as the pivot but it's important to realize that the Lomuto algorithm always uses the *last* element, `a[high]`, for the pivot. Because we've been pivoting around `8` all this time, I swapped the positions of `8` and `26` in the example so that `8` is at the end of the array and is used as the pivot value here too.

After partitioning, the array looks like this:
-->

注意到 `list` 必須是個 `var`, 因為 `partitionLomuto()` 會直接改變這個陣列的內容 (它是以 `inout` 參數傳送). 這筆配置一個新的陣列物件還要有效率多了.

`low` 跟 `high` 參數是必要的, 因為當使用內部快速排序時, 你不希望總是要重新分割整個陣列, 只需要限制某個範圍然後讓他越來越小就好.

先前我們用陣列中間的元素來當樞紐, 但是在 Lomuto 演算法中總是使用 *最後* 的元素 `a[high]` 當樞紐. 因為我們都用 `8` 來當樞紐, 在這邊我將 `8` 和 `26` 調換位置, 所以讓 `8` 在陣列的最後位置, 讓它在這次也是當樞紐.

再分割後, 陣列像這樣:

	[ 0, 3, 2, 1, 5, 8, -1, 8, 9, 10, 14, 26, 27 ]
	                        *

<!--
The variable `p` contains the return value of the call to `partitionLomuto()` and is 7. This is the index of the pivot element in the new array (marked with a star).

The left partition goes from 0 to `p-1` and is `[ 0, 3, 2, 1, 5, 8, -1 ]`. The right partition goes from `p+1` to the end, and is `[ 9, 10, 14, 26, 27 ]` (the fact that the right partition is already sorted is a coincidence). 

You may notice something interesting... The value `8` occurs more than once in the array. One of those `8`s did not end up neatly in the middle but somewhere in the left partition. That's a small downside of the Lomuto algorithm as it makes quicksort slower if there are a lot of duplicate elements.

So how does the Lomuto algorithm actually work? The magic happens in the `for` loop. This loop divides the array into four regions:

1. `a[low...i]` contains all values <= pivot
2. `a[i+1...j-1]` contains all values > pivot
3. `a[j...high-1]` are values we haven't looked at yet
4. `a[high]` is the pivot value

In ASCII art the array is divided up like this:
-->

變數 `p` 紀錄 `partitionLomuto()` 的回傳值, 7. 這是新的陣列中的樞紐元素索引 (星號標記).

左邊的分割索引從 0 到 `p-1` 是 `[ 0, 3, 2, 1, 5, 8, -1 ]`. 右邊的分割索引從 `p+1` 到結尾, 是 `[ 9, 10, 14, 26, 27 ]` (事實上這時候右邊的分割已經不小心有序了)

你可能注意到一些有趣的事... `8` 在陣列中出現不只一次. 而有一個 `8` 並沒有在中間的位置, 反而在左邊的分割. 這是 Lomuto 演算法一個小小不足的地方, 如果陣列中有很多重複的元素, 那這種分割方式會讓快速排序變得比較慢.

所以 Lomuto 演算法到底怎麼運作的? 神奇的地方在 `for` 迴圈. 這個迴圈把陣列分割成四個區塊:

1. `a[low...i]` <= 樞紐
2. `a[i+1...j-1]`  > 樞紐
3. `a[j...high-1]` 是我們還沒走訪過的元素
4. `a[high]` 是樞紐

在 ASCII 藝術中陣列可以表示成這樣:

	[ values <= pivot | values > pivot | not looked at yet | pivot ]
	  low           i   i+1        j-1   j          high-1   high

<!--
The loop looks at each element from `low` to `high-1` in turn. If the value of the current element is less than or equal to the pivot, it is moved into the first region using a swap.

> **Note:** In Swift, the notation `(x, y) = (y, x)` is a convenient way to perform a swap between the values of `x` and `y`. You can also write `swap(&x, &y)`.

After the loop is over, the pivot is still the last element in the array. So we swap it with the first element that is greater than the pivot. Now the pivot sits between the <= and > regions and the array is properly partitioned.

Let's step through the example. The array we're starting with is:
-->

迴圈依序走訪每個從 `low` 到 `high-1` 的元素. 如果當前元素比樞紐小或相等, 就把它交換到第一區塊.

> **注意:** 在 Swift 中, `(x, y) = (y, x)` 的表達式是方便做 `x`, `y` 交換的一種方式. 當然你也可以寫 `swap(&x, &y)`.

迴圈結束後, 樞紐還是在陣列的最尾端. 所以我們把樞紐和第一個比樞紐大的元素交換位置. 現在樞紐就在 <= 和 > 區塊之間了, 陣列也適當的分割了.


	[| 10, 0, 3, 9, 2, 14, 26, 27, 1, 5, 8, -1 | 8 ]
	   low                                       high
	   i
	   j

<!--
Initially, the "not looked at" region stretches from index 0 to 11. The pivot is at index 12. The "values <= pivot" and "values > pivot" regions are empty, because we haven't looked at any values yet.

Look at the first value, `10`. Is this smaller than the pivot? No, skip to the next element.
-->

初始化時, "not looked at yet" 的區塊是由索引 0 到 11. 樞紐位置在索引 12. "values <= pivot" 和 "values > pivot" 是空的, 因為我們還沒開始走訪.

走訪第一個元素, `10`. 比樞紐小嗎? 沒有, 下一個.
	  

	[| 10 | 0, 3, 9, 2, 14, 26, 27, 1, 5, 8, -1 | 8 ]
	   low                                        high
	   i
	       j

<!--
Now the "not looked at" region goes from index 1 to 11, the "values > pivot" region contains the number `10`, and "values <= pivot" is still empty.

Look at the second value, `0`. Is this smaller than the pivot? Yes, so swap `10` with `0` and move `i` ahead by one.
-->

現在 "not looked at" 區塊是從索引 1 到 11, "values > pivot" 區塊有元素 `10`, "values <= pivot" 還是空的.

走訪第二個元素, `0`. 比樞紐小嗎? 是的! 所以將 `10` 和 `0` 交換, 然後將 `i` 遞增.


	[ 0 | 10 | 3, 9, 2, 14, 26, 27, 1, 5, 8, -1 | 8 ]
	  low                                         high
	      i
	           j

<!--
Now "not looked at" goes from index 2 to 11, "values > pivot" still contains `10`, and "values <= pivot" contains the number `0`.

Look at the third value, `3`. This is smaller than the pivot, so swap it with `10` to get:
-->

現在 "not looked at" 區塊是從索引 2 到 11. "values > pivot" 區塊含有 `10`, 而 "values <= pivot" 有 `0`.

現在看到第三個元素, `3`. 比樞紐小, 所以和 `10` 交換:


	[ 0, 3 | 10 | 9, 2, 14, 26, 27, 1, 5, 8, -1 | 8 ]
	  low                                         high
	         i
	             j

<!--
The "values <= pivot" region is now `[ 0, 3 ]`. Let's do one more... `9` is greater than the pivot, so simply skip ahead:
-->

"values <= pivot" 區塊現在是 `[ 0, 3 ]`. 繼續走... `9` 比樞紐大, 下一步:


	[ 0, 3 | 10, 9 | 2, 14, 26, 27, 1, 5, 8, -1 | 8 ]
	  low                                         high
	         i
	                 j

<!--
Now the "values > pivot" region contains `[ 10, 9 ]`. If we keep going this way, then eventually we end up with:
-->

現在 "values > pivot" 區塊是 `[ 10, 9]`. 一直持續下去, 最後我們會得到:


	[ 0, 3, 2, 1, 5, 8, -1 | 27, 9, 10, 14, 26 | 8 ]
	  low                                        high
	                         i                   j

<!--
The final thing to do is to put the pivot into place by swapping `a[i]` with `a[high]`:
-->

最後一件事就是把樞紐位置調整好, 把 `a[i]` 和 `a[high]` 交換:

	[ 0, 3, 2, 1, 5, 8, -1 | 8 | 9, 10, 14, 26, 27 ]
	  low                                       high
	                         i                  j

<!--
And we return `i`, the index of the pivot element.

> **Note:** If you're still not entirely clear on how the algorithm works, I suggest you play with this in the playground to see exactly how the loop creates these four regions.

Let's use this partitioning scheme to build quicksort. Here's the code:
-->

回傳 `i`, 這時候表示樞紐的索引.

> **注意:** 如果你還是沒有很清楚整個演算法流程, 我建議你在 playground 中玩玩這個範例然後觀察迴圈如何創建那四個區塊.

讓我們使用這個分割方案來改進快速排序. 程式碼:

```swift
func quicksortLomuto<T: Comparable>(_ a: inout [T], low: Int, high: Int) {
  if low < high {
    let p = partitionLomuto(&a, low: low, high: high)
    quicksortLomuto(&a, low: low, high: p - 1)
    quicksortLomuto(&a, low: p + 1, high: high)
  }
}
```

<!--
This is now super simple. We first call `partitionLomuto()` to reorder the array around the pivot (which is always the last element from the array). And then we call `quicksortLomuto()` recursively to sort the left and right partitions.

Try it out:
-->

現在就很簡單了. 我們呼叫 `partitionLomuto()` 來依照樞紐 (總是陣列最尾端元素) 重新分割陣列. 然後遞迴的呼叫 `quicksortLomuto()` 來排序左分割與右分割.

試試看:


```swift
var list = [ 10, 0, 3, 9, 2, 14, 26, 27, 1, 5, 8, -1, 8 ]
quicksortLomuto(&list, low: 0, high: list.count - 1)
```

<!--
Lomuto's isn't the only partitioning scheme but it's probably the easiest to understand. It's not as efficient as Hoare's scheme, which requires fewer swap operations.
-->

Lomuto 分割不是唯一的分割方案, 但可能是最容易理解的. 不過它並沒有比 Hoare 方案還有效率, Hoare 使用了一些交換的操作.

<!--
## Hoare's partitioning scheme

This partitioning scheme is by Hoare, the inventor of quicksort.

Here is the code:
-->

## Hoare 分割方案


```Swift
func partitionHoare<T: Comparable>(_ a: inout [T], low: Int, high: Int) -> Int {
  let pivot = a[low]
  var i = low - 1
  var j = high + 1
  
  while true {
    repeat { j -= 1 } while a[j] > pivot
    repeat { i += 1 } while a[i] < pivot
    
    if i < j {
      swap(&a[i], &a[j])
    } else {
      return j
    }
  }
}
```

<!--
To test this in a playground, do:
-->

在 playground 中測試:

```swift
var list = [ 8, 0, 3, 9, 2, 14, 10, 27, 1, 5, 8, -1, 26 ]
let p = partitionHoare(&list, low: 0, high: list.count - 1)
list  // show the results
```

<!--
Note that with Hoare's scheme, the pivot is always expected to be the *first* element in the array, `a[low]`. Again, we're using `8` as the pivot element.

The result is:
-->

在 Hoare 方案中, 樞紐總是選取 *第一* 個元素, `a[low]`. 再一次, 我們使用 `8` 來當樞紐元素.

結果是:

	[ -1, 0, 3, 8, 2, 5, 1, 27, 10, 14, 9, 8, 26 ]

<!--
Note that this time the pivot isn't in the middle at all. Unlike with Lomuto's scheme, the return value is not necessarily the index of the pivot element in the new array. 

Instead, the array is partitioned into the regions `[low...p]` and `[p+1...high]`. Here, the return value `p` is 6, so the two partitions are `[ -1, 0, 3, 8, 2, 5, 1 ]` and `[ 27, 10, 14, 9, 8, 26 ]`. 

The pivot is placed somewhere inside one of the two partitions, but the algorithm doesn't tell you which one or where. If the pivot value occurs more than once, then some instances may appear in the left partition and others may appear in the right partition.

Because of these differences, the implementation of Hoare's quicksort is slightly different:
-->

注意到這次樞紐並不在中間的位置. 和 Lomuto 不同的是, 此方法回傳值並非一定是樞紐點的在新陣列中的索引.


```swift
func quicksortHoare<T: Comparable>(_ a: inout [T], low: Int, high: Int) {
  if low < high {
    let p = partitionHoare(&a, low: low, high: high)
    quicksortHoare(&a, low: low, high: p)
    quicksortHoare(&a, low: p + 1, high: high)
  }
}
```

<!--
I'll leave it as an exercise for the reader to figure out exactly how Hoare's partitioning scheme works. :-)
-->

Hoare 分割詳細流程就留給讀者當練習囉. :-)

<!--
## Picking a good pivot

Lomuto's partitioning scheme always chooses the last array element for the pivot. Hoare's scheme uses the first element. But there is no guarantee that these pivots are any good.

Here is what happens when you pick a bad value for the pivot. Let's say the array is,
-->
## 選一個好的樞紐點

Lomuto 分割總是選擇最後一個元素當作樞紐. 而 Hoare 分割總是選擇第一個. 但是都無法保證這些樞紐是好的.

這裡展示如果選了一個糟糕的樞紐會怎樣. 假這是這個陣列:

	[ 7, 6, 5, 4, 3, 2, 1 ]

<!--
and we're using Lomuto's scheme. The pivot is the last element, `1`. After pivoting, we have the following arrays:
-->

假設我們使用 Lomuto 分割. 樞紐是最後一個元素, `1`. 在分割後, 我們得到以下陣列:

	   less than pivot: [ ]
	    equal to pivot: [ 1 ]
	greater than pivot: [ 7, 6, 5, 4, 3, 2 ]

<!--
Now recursively partition the "greater than" subarray and get:
-->

現在遞迴的分割 "greater than" 的子陣列得到 ：

	   less than pivot: [ ]
	    equal to pivot: [ 2 ]
	greater than pivot: [ 7, 6, 5, 4, 3 ]

<!--
And again:
-->

再一次:

	   less than pivot: [ ]
	    equal to pivot: [ 3 ]
	greater than pivot: [ 7, 6, 5, 4 ]

<!--
And so on...

That's no good, because this pretty much reduces quicksort to the much slower insertion sort. For quicksort to be efficient, it needs to split the array into roughly two halves. 

The optimal pivot for this example would have been `4`, so we'd get:
-->

然後繼續...

這樣做真糟, 因為這幾乎使快速排序比插入排序還要慢. 為了讓快速排序保持效率, 樞紐點需要能將陣列分割成兩個大小差不多的區塊.

這個例子中最佳的樞紐是 `4`, 這樣了話我們就會分割成:

	   less than pivot: [ 3, 2, 1 ]
	    equal to pivot: [ 4 ]
	greater than pivot: [ 7, 6, 5 ]

<!--
You might think this means we should always choose the middle element rather than the first or the last, but imagine what happens in the following situation:
-->

你可能會覺得這表示我們依該選擇中間的元素, 而不是第一個或最後, 但想像一下要是發生下列狀況怎麼辦:

	[ 7, 6, 5, 1, 4, 3, 2 ]

<!--
Now the middle element is `1` and that gives the same lousy results as in the previous example.

Ideally, the pivot is the *median* element of the array that you're partitioning, i.e. the element that sits in the middle of the sorted array. Of course, you won't know what the median is until after you've sorted the array, so this is a bit of a chicken-and-egg problem. However, there are some tricks to choose good, if not ideal, pivots.

One trick is "median-of-three", where you find the median of the first, middle, and last element in this subarray. In theory that often gives a good approximation of the true median.

Another common solution is to choose the pivot randomly. Sometimes this may result in choosing a suboptimal pivot but on average this gives very good results.

Here is how you can do quicksort with a randomly chosen pivot:
-->

現在 `1` 是中間的元素, 這樣了話就跟之前那個很糟的狀況一樣了.

理想情況上, 樞紐是應該要是陣列元素中的 *中位數*, 即, 該元素在排序後會在有序陣列的中間. 當然, 在排序前你不會知道中位數是誰, 所以這有一點雞與蛋的問題. 無論如何, 還是有一些技巧來選擇比較好, 但不一定是最佳的, 樞紐.


```swift
func quicksortRandom<T: Comparable>(_ a: inout [T], low: Int, high: Int) {
  if low < high {
    let pivotIndex = random(min: low, max: high)         // 1

    (a[pivotIndex], a[high]) = (a[high], a[pivotIndex])  // 2

    let p = partitionLomuto(&a, low: low, high: high)
    quicksortRandom(&a, low: low, high: p - 1)
    quicksortRandom(&a, low: p + 1, high: high)
  }
}
```
<!--
There are two important differences with before:

1. The `random(min:max:)` function returns an integer in the range `min...max`, inclusive. This is our pivot index.

2. Because the Lomuto scheme expects `a[high]` to be the pivot entry, we swap `a[pivotIndex]` with `a[high]` to put the pivot element at the end before calling `partitionLomuto()`.

It may seem strange to use random numbers in something like a sorting function, but it is necessary to make quicksort behave efficiently under all circumstances. With bad pivots, the performance of quicksort can be quite horrible, **O(n^2)**. But if you choose good pivots on average, for example by using a random number generator, the expected running time becomes **O(n log n)**, which is as good as sorting algorithms get.
-->

和之前有兩個重要的差異:

1. `random(min:max:)` 函式回傳 `min...max` 中的某一個整數. 這個數就是我們的樞紐索引.
2. 因為 Lomuto 方案使用 `a[high]` 當作樞紐, 我們在呼叫 `partitionLomuto()` 之前將 `a[pivotIndex]` 和 `a[high]` 做交換位置.

使用隨機數字來排序看起來很奇怪, 但這是使快速排序在任何情況下都保持效率所需要的處理. 對於一個糟糕的樞紐, 快速排序的表現可能非常恐怖, **O(n^2)**. 但是如果平均在選擇樞紐上是選到好的, 例如使用隨機數字產生器, 期望的執行時間就會變成 **O(n log n)**, 就跟其他排序法一樣好.

<!--
## Dutch national flag partitioning

But there are more improvements to make! In the first example of quicksort I showed you, we ended up with an array that was partitioned like this:
-->

## 荷蘭國旗分割方案

還可以做更多的進步! 在第一個快速排序的範例中, 我們最後將陣列分割成這樣:

	[ values < pivot | values equal to pivot | values > pivot ]

<!--
But as you've seen with the Lomuto partitioning scheme, if the pivot occurs more than once the duplicates end up in the left half. And with Hoare's scheme the pivot can be all over the place. The solution to this is "Dutch national flag" partitioning, named after the fact that the [Dutch flag](https://en.wikipedia.org/wiki/Flag_of_the_Netherlands) has three bands just like we want to have three partitions.

The code for this scheme is:
-->

但你已經看過了 Lomuto 分割方案, 如果樞紐值發生不只一次, 這些重複的值就會被分割到左邊. 而依照 Hoare 分割方案了話可能會被分割到任何地方. 這個問題的解決方式是 "荷蘭國旗" 分割. 因為[荷蘭國旗](https://en.wikipedia.org/wiki/Flag_of_the_Netherlands)有三個橫條, 就像我們希望分隔成三個區塊一樣.

程式碼:



```swift
func partitionDutchFlag<T: Comparable>(_ a: inout [T], low: Int, high: Int, pivotIndex: Int) -> (Int, Int) {
  let pivot = a[pivotIndex]

  var smaller = low
  var equal = low
  var larger = high

  while equal <= larger {
    if a[equal] < pivot {
      swap(&a, smaller, equal)
      smaller += 1
      equal += 1
    } else if a[equal] == pivot {
      equal += 1
    } else {
      swap(&a, equal, larger)
      larger -= 1
    }
  }
  return (smaller, larger)
}
```

<!--
This works very similarly to the Lomuto scheme, except that the loop partitions the array into four (possibly empty) regions:
-->

這跟 Lomuto 滿像的, 除了把陣列分成四個區塊 (可能是空的):

- `[low ... smaller-1]` contains all values < pivot
- `[smaller ... equal-1]` contains all values == pivot
- `[equal ... larger]` contains all values > pivot
- `[larger ... high]` are values we haven't looked at yet

<!--
Note that this doesn't assume the pivot is in `a[high]`. Instead, you have to pass in the index of the element you wish to use as a pivot.

An example of how to use it:
-->

注意這邊並沒有假設樞紐是 `a[high]`. 相反的, 你需要傳入一個你希望的元素索引.

使用範例:

```swift
var list = [ 10, 0, 3, 9, 2, 14, 8, 27, 1, 5, 8, -1, 26 ]
partitionDutchFlag(&list, low: 0, high: list.count - 1, pivotIndex: 10)
list  // show the results
```

<!--
Just for fun, we're giving it the index of the other `8` this time. The result is:
-->

為了好玩, 我們放了另一個 `8` 在陣列中, 這次得到:


	[ -1, 0, 3, 2, 5, 1, 8, 8, 27, 14, 9, 26, 10 ]

<!--
Notice how the two `8`s are in the middle now. The return value from `partitionDutchFlag()` is a tuple, `(6, 7)`. This is the range that contains the pivot value(s).

Here is how you would use it in quicksort:
-->

注意到現在兩個 `8` 都在中間了. `partitionDutchFlag()` 回傳的是一個元組, `(6, 7)`. 這個範圍內的就是樞紐值.

所以在快速排序中可以這樣使用:

```swift
func quicksortDutchFlag<T: Comparable>(_ a: inout [T], low: Int, high: Int) {
  if low < high {
    let pivotIndex = random(min: low, max: high)
    let (p, q) = partitionDutchFlag(&a, low: low, high: high, pivotIndex: pivotIndex)
    quicksortDutchFlag(&a, low: low, high: p - 1)
    quicksortDutchFlag(&a, low: q + 1, high: high)
  }
}
```

<!--
Using Dutch flag partitioning makes for a more efficient quicksort if the array contains many duplicate elements. (And I'm not just saying that because I'm Dutch!)

> **Note:** The above implementation of `partitionDutchFlag()` uses a custom `swap()` routine for swapping the contents of two array elements. Unlike Swift's own `swap()`, this doesn't give an error when the two indices refer to the same array element. See [Quicksort.swift](Quicksort.swift) for the code.
-->

使用荷蘭國旗分割可以在陣列中有重複元素時讓快速排序更有效率. (不是因為我是荷蘭人才這樣說!)

> **注意:** 上面 `partitionDutchFlag()` 的實作使用的是客製化的 `swap()`. 不像 Swift 原生的 `swap()`, 不會在兩個索引值相同時回傳錯誤. 程式碼請見 [Quicksort.swift](Quicksort.swift).

<!--
## See also
-->

## 相關閱讀

[Quicksort on Wikipedia](https://en.wikipedia.org/wiki/Quicksort)

*Written for Swift Algorithm Club by Matthijs Hollemans*
