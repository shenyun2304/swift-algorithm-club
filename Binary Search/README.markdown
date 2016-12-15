# Binary Search 二元搜尋

<!--

Goal: Quickly find an element in an array.

Let's say you have an array of numbers and you want to determine whether a specific number is in that array, and if so, at which index.

In most cases, Swift's `indexOf()` function is good enough for that:
-->

目標: 快速的在陣列中找到元素.

想要在一個陣列中搜尋某個元素, 如果該元素在陣列之中, 獲得該元素的索引.

大多數的情況, Swift 的 `indexOf()` 方法已經夠用:

```swift
let numbers = [11, 59, 3, 2, 53, 17, 31, 7, 19, 67, 47, 13, 37, 61, 29, 43, 5, 41, 23]

numbers.indexOf(43)  // returns 15
```

<!--
The built-in `indexOf()` function performs a [linear search](../Linear Search/). In code that looks something like this:
-->

內建的 `indexOf()` 方法是使用 [線性搜尋](../Linear Search/). 程式碼類似這樣:

```swift
func linearSearch<T: Equatable>(_ a: [T], _ key: T) -> Int? {
    for i in 0 ..< a.count {
        if a[i] == key {
            return i
        }
    }
    return nil
}
```
<!--
And you'd use it like this:
-->

可以這樣使用:

```swift
linearSearch(numbers, 43)  // returns 15
```

<!--
So what's the problem? `linearSearch()` loops through the entire array from the beginning, until it finds the element you're looking for. In the worst case, the value isn't even in the array and all that work is done for nothing.

On average, the linear search algorithm needs to look at half the values in the array. If your array is large enough, this starts to become very slow!
-->

那問題在哪呢? `linearSearch()` 從頭走訪整個陣列, 直到找到指定的元素. 最糟的情況, 陣列中不包含該元素, 則會浪費資源的走訪整個陣列.

平均計算, 線性搜尋演算法會走訪陣列長度的一半. 這方法會隨著陣列的長度越大而越來越慢.

<!--
## Divide and conquer

The classic way to speed this up is to use a *binary search*. The trick is to keep splitting the array in half until the value is found.

For an array of size `n`, the performance is not **O(n)** as with linear search but only **O(log n)**. To put that in perspective, binary search on an array with 1,000,000 elements only takes about 20 steps to find what you're looking for, because `log_2(1,000,000) = 19.9`. And for an array with a billion elements it only takes 30 steps. (Then again, when was the last time you used an array with a billion items?)

Sounds great, but there is a downside to using binary search: the array must be sorted. In practice, this usually isn't a problem.

Here's how binary search works:

- Split the array in half and determine whether the thing you're looking for, known as the *search key*, is in the left half or in the right half.
- How do you determine in which half the search key is? This is why you sorted the array first, so you can do a simple `<` or `>` comparison.
- If the search key is in the left half, you repeat the process there: split the left half into two even smaller pieces and look in which piece the search key must lie. (Likewise for when it's the right half.)
- This repeats until the search key is found. If the array cannot be split up any further, you must regrettably conclude that the search key is not present in the array.

Now you know why it's called a "binary" search: in every step it splits the array into two halves. This process of *divide-and-conquer* is what allows it to quickly narrow down where the search key must be.
-->

## 解決問題

經典處理這問題的方法是使用 *二元搜尋*. 技巧是不停的對半分割陣列直到找到目標值.

對於長度為 `n` 的陣列, 這個搜尋方法的時間複雜度是 **O(log n)**, 不像線性搜尋是 **O(n)**. 一般來說, 對於一個包含 1,000,000 個元素的陣列, 使用二元搜尋找到特定的某個元素大概只會花 20 次 (`log_2(1,000,000) = 19.9`) 走訪. 而對於有百萬元素的陣列也只需要 30 次左右. (小提醒, 你上一次需要一個長度超過百萬的陣列是什麼時候?)

聽起來不錯, 但是這方法還是有個缺點: 陣列必須排序過. 實際上, 這點通常不是問題.

二元搜尋的流程:

- 將陣列一分為二並且判斷所尋找的元素值 (稱做 *搜尋鍵值*) 是落在左半還是右半陣列.
- 如何判斷搜尋鍵值是落在左半還是右半? 這就是為什麼要先把陣列做排序, 這樣就可以簡單的做 `<` 或 `>` 的判斷.
- 如果搜尋鍵值是落在左半邊, 則將左半邊的陣列重複執行對半的分割, 判定落點的動作. (如果是落在右半邊也是同樣道理)
- 一直重複這個動作直到找到搜尋鍵值. 如果陣列已經分割到無法在分割但還是沒有找到搜尋鍵值, 則判定該元素不在陣列中.

現在你可以了解為何這個方法稱做"二元": 每一步都是將陣列一分為二. 

<!--
## The code

Here is a recursive implementation of binary search in Swift:
-->

## 程式碼

這裡使用遞迴來完成二元搜尋:

```swift
func binarySearch<T: Comparable>(_ a: [T], key: T, range: Range<Int>) -> Int? {
    if range.lowerBound >= range.upperBound {
        // If we get here, then the search key is not present in the array.
        return nil

    } else {
        // Calculate where to split the array.
        let midIndex = range.lowerBound + (range.upperBound - range.lowerBound) / 2

        // Is the search key in the left half?
        if a[midIndex] > key {
            return binarySearch(a, key: key, range: range.lowerBound ..< midIndex)

        // Is the search key in the right half?
        } else if a[midIndex] < key {
            return binarySearch(a, key: key, range: midIndex + 1 ..< range.upperBound)

        // If we get here, then we've found the search key!
        } else {
            return midIndex
        }
    }
}
```

<!--
To try this out, copy the code to a playground and do:
-->
試試看, 將程式碼複製貼上到 playground 然後這樣做:


```swift
let numbers = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67]

binarySearch(numbers, key: 43, range: 0 ..< numbers.count)  // gives 13
```

<!--
Note that the `numbers` array is sorted. The binary search algorithm does not work otherwise!

I said that binary search works by splitting the array in half, but we don't actually create two new arrays. Instead, we keep track of these splits using a Swift `Range` object. Initially, this range covers the entire array, `0 ..< numbers.count`.  As we split the array, the range becomes smaller and smaller.

> **Note:** One thing to be aware of is that `range.upperBound` always points one beyond the last element. In the example, the range is `0..<19` because there are 19 numbers in the array, and so `range.lowerBound = 0` and `range.upperBound = 19`. But in our array the last element is at index 18, not 19, since we start counting from 0. Just keep this in mind when working with ranges: the `upperBound` is always one more than the index of the last element.
-->

注意 `number` 陣列是已經排序過了. 二元搜尋無法正常運作在沒排序的陣列上.

雖然說二元搜尋是將陣列一分為二個概念, 但是我們並沒有真的去建立左右兩個新陣列. 相對的, 我們使用 Swift `Range` 範圍物件來持續追蹤這些分割. 一開始, 這個範圍包含整個陣列, `0 ..< numbers.count`. 每當我們切割陣列, 這個範圍會越來越小.

> **注意:** 這邊要留意 `range.upperBound` 會獲得最後元素索引 + 1 的值.
在範例中, 因為陣列總共有 19 個元素, 範圍是 `0..<19`, 所以 `range.lowerBound = 0` 而且 `range.upperBound = 19`. 但是因為陣列索引是從 0 開始, 所以陣列的最後一個元素的索引是 18, 並非 19. 特別提醒一下當使用範圍物件的 `upperBound` 時, 會獲得的是陣列最後元素索引 + 1 的值.

<!--
## Stepping through the example

It might be useful to look at how the algorithm works in detail.

The array from the above example consists of 19 numbers and looks like this when sorted:

	[ 2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67 ]

We're trying to determine if the number `43` is in this array.

To split the array in half, we need to know the index of the object in the middle. That's determined by this line:
-->

## 一步一步來看範例

在上例中的陣列內有 19 個數字元素, 當排序過後像這樣:
	
	[ 2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67 ]
	
我們想知道 43 是否在陣列中, 如果存在那該索引在哪?

為了將陣列一分為二, 我們需要知道中間索引在哪, 可以透過這個方法來得到它:

```swift
let midIndex = range.lowerBound + (range.upperBound - range.lowerBound) / 2
```

一開始時, 範圍物件屬性有 `lowerBound = 0` 和 `upperBound = 19`. 我們得到 `midIndex` 是 `0 + (19 - 0)/2 = 19/2 = 9`. 當然正確的值是 `9.5` 但是我們使用整數, 所以小數部分會被截去.

這裡以 `*` 顯示中間物件, 可以看到左右兩邊元素的數量是相同的, 所以我們從中間分割陣列.

	[ 2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67 ]
                                      *
<!--
Now binary search will determine which half to use. The relevant section from the code is:
-->

分割後二元搜尋法要決定下一個是要分跟左邊還是右邊. 這個判斷的程式碼是這樣:


```swift
if a[midIndex] > key {
    // use left half
} else if a[midIndex] < key {
    // use right half
} else {
    return midIndex
}
```

<!--
In this case, `a[midIndex] = 29`. That's less than the search key, so we can safely conclude that the search key will never be in the left half of the array. After all, the left half only contains numbers smaller than `29`. Hence, the search key must be in the right half somewhere (or not in the array at all).

Now we can simply repeat the binary search, but on the array interval from `midIndex + 1` to `range.upperBound`:
-->

在這階段, `a[midIndex] = 29`. 這個數比搜尋鍵值 (43) 還要小, 所以我們可以知道搜尋鍵值絕對不會在左半邊的範圍中. 至此, 如果搜尋鍵值存在此陣列, 則一定是在右半邊.

現在我們可以簡單的重複二元搜尋的動作, 但這次的範圍是從 `midIndex + 1` 到 `range.upperBound`:

	[ x, x, x, x, x, x, x, x, x, x | 31, 37, 41, 43, 47, 53, 59, 61, 67 ]

<!--
Since we no longer need to concern ourselves with the left half of the array, I've marked that with `x`'s. From now on we'll only look at the right half, which starts at array index 10.

We calculate the index of the new middle element: `midIndex = 10 + (19 - 10)/2 = 14`, and split the array down the middle again.
-->

因為我們不需要再考慮左半邊的元素, 所以這裡將那些元素以 `x` 來表示, 從現在開始我們只考慮右邊的元素, 索引值從 10 開始.

我們再次計算中間值的索引: `midIndex = 10 + (19 - 10)/2 = 14`, 然後再次將陣列一分為二.

	[ x, x, x, x, x, x, x, x, x, x | 31, 37, 41, 43, 47, 53, 59, 61, 67 ]
	                                                 *

<!--
As you can see, `a[14]` is indeed the middle element of the array's right half.

Is the search key greater or smaller than `a[14]`? It's smaller because `43 < 47`. This time we're taking the left half and ignore the larger numbers on the right:
-->

可以看到, `a[14]` 的確是右半邊陣列中的中間值.

那搜尋值 (43) 跟 `a[14]` 比較起來如何? 因為 `43 < 47`, 所以這次我們選擇左半邊並且忽略右半邊的元素:

	[ x, x, x, x, x, x, x, x, x, x | 31, 37, 41, 43 | x, x, x, x, x ]

<!--
The new `midIndex` is here:
-->
新的 `midIndex` 在這:

	[ x, x, x, x, x, x, x, x, x, x | 31, 37, 41, 43 | x, x, x, x, x ]
	                                     *
<!--
The search key is greater than `37`, so continue with the right side:
-->
因為搜尋鍵值 (43) 大於 `37`, 所以用採用右半邊, 並繼續:

	[ x, x, x, x, x, x, x, x, x, x | x, x | 41, 43 | x, x, x, x, x ]
	                                        *
<!--
Again, the search key is greater, so split once more and take the right side:
-->
再一次, 搜尋鍵值還是比較大, 所以在切割一次並採用右半邊.

	[ x, x, x, x, x, x, x, x, x, x | x, x | x | 43 | x, x, x, x, x ]
	                                            *

<!--
And now we're done. The search key equals the array element we're looking at, so we've finally found what we were searching for: number `43` is at array index `13`. w00t!

It may have seemed like a lot of work, but in reality it only took four steps to find the search key in the array, which sounds about right because `log_2(19) = 4.23`. With a linear search, it would have taken 14 steps.

What would happen if we were to search for `42` instead of `43`? In that case, we can't split up the array any further. The `range.upperBound` becomes smaller than `range.lowerBound`. That tells the algorithm the search key is not in the array and it returns `nil`.

> **Note:** Many implementations of binary search calculate `midIndex = (lowerBound + upperBound) / 2`. This contains a subtle bug that only appears with very large arrays, because `lowerBound + upperBound` may overflow the maximum number an integer can hold. This situation is unlikely to happen on a 64-bit CPU, but it definitely can on 32-bit machines.
-->

終於! 我們完成了. 這個中間值和我們的搜尋鍵值相等, 所以我們終於找到我們的索引了: 數字 `43` 在索引 `13` 的地方. w00t!

看起來好像做了很多工, 但實際上只做了 4 次走訪就找到搜尋鍵值了, 看起來好像是正確的 (因為 `log_2(19) = 4.23`). 如果用線性搜尋, 那將需要走訪 14 次.

如果我們搜尋的是 `42` 而不是 `43` 呢? 我們最後已經無法再切割陣列了, 因為 `range.upperBound` 變得比 `range.lowerBound` 還小. 這表示該搜尋鍵值並不在陣列中, 便回傳 `nil`.

> **注意:** 有許多二元搜尋法的實現在計算中間值的時候是使用 `midIndex = (lowerBound + upperBound) / 2`. 這種算法在當陣列長度非常大的時候有潛在的 bug. 因為 `lowerBound + upperBound` 有可能造成整數溢位. 這種情況在 64 位元的 CPU 不太容易發生, 但是在 32 位元的機器上是決定有可能發生的.

<!--
## Iterative vs recursive

Binary search is recursive in nature because you apply the same logic over and over again to smaller and smaller subarrays. However, that does not mean you must implement `binarySearch()` as a recursive function. It's often more efficient to convert a recursive algorithm into an iterative version, using a simple loop instead of lots of recursive function calls.

Here is an iterative implementation of binary search in Swift:
-->
## 迭代與遞迴

二元搜尋法很自然的使用遞迴是因為只是同一套邏輯操作不斷重複而已. 但是這並不表示一定要使用遞迴來實作 `binarySearch()`. 通常將遞迴運算轉換成迭代運算都有更好的效率, 使用一個簡單的迴圈而非一連串的遞迴呼叫.

這裡是使用 Swift 來實現迭代運算的二元搜尋法:

```swift
func binarySearch<T: Comparable>(_ a: [T], key: T) -> Int? {
    var lowerBound = 0
    var upperBound = a.count
    while lowerBound < upperBound {
        let midIndex = lowerBound + (upperBound - lowerBound) / 2
        if a[midIndex] == key {
            return midIndex
        } else if a[midIndex] < key {
            lowerBound = midIndex + 1
        } else {
            upperBound = midIndex
        }
    }
    return nil
}
```
<!--
As you can see, the code is very similar to the recursive version. The main difference is in the use of the `while` loop.

Use it like this:
-->

可以看到, 這個程式碼非常類似遞迴的版本, 主要的差異在於使用 `while` 迴圈.

可以像這樣使用:

```swift
let numbers = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67]

binarySearch(numbers, key: 43)  // gives 13
```
<!--
## The end

Is it a problem that the array must be sorted first? It depends. Keep in mind that sorting takes time -- the combination of binary search plus sorting may be slower than doing a simple linear search. Binary search shines in situations where you sort just once and then do many searches.

See also [Wikipedia](https://en.wikipedia.org/wiki/Binary_search_algorithm).
-->

## 結尾
陣列一定要先排序過會造成問題嗎? 這要看情況. 記住, 排序是需要時間的 -- 排序加上二元搜尋可能會比直接單純的線性搜尋還慢. 不過二元搜尋在一次排序多次搜尋的情況下非常優質.

相關閱讀 [Wikipedia](https://en.wikipedia.org/wiki/Binary_search_algorithm).

*Written for Swift Algorithm Club by Matthijs Hollemans*
