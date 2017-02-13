# Merge Sort (合併排序)

<!--
Goal: Sort an array from low to high (or high to low)

Invented in 1945 by John von Neumann, merge-sort is an efficient algorithm with a best, worst, and average time complexity of **O(n log n)**.

The merge-sort algorithm uses the **divide and conquer** approach which is to divide a big problem into smaller problems and solve them. I think of the merge-sort algorithm as **split first** and **merge after**. 

Assume you need to sort an array of *n* numbers in the right order. The merge-sort algorithm works as follows:


- Put the numbers in an unsorted pile.
- Split the pile into two. Now, you have **two unsorted piles** of numbers.
- Keep splitting the resulting piles until you cannot split anymore. In the end, you will have *n* piles with one number in each pile.
- Begin to **merge** the piles together by pairing them sequentially. During each merge, put the contents in sorted order. This is fairly easy because each individual pile is already sorted.

-->

目標: 將陣列做遞減或遞增排序

1945 年由 John von Neumann 發明, 合併排序在最好, 最糟, 和平均, 在時間上都是 **O(n log n)**.

合併排序背後的概念是 **分裂與征服**: 將一個大問題分割成很多個小問題, 去解決每個小問題而不是一次性的處理大問題. 我想合併排序就像是 **先分裂** 然後 **再合併**.

- 把數字放到無序管線中.
- 把管線一分為二. 現在你有 **兩個無序的管線**.
- 持續的分割管線直到你無法在分割為止. 最後, 你會有 *n* 個管線, 而每個管線只有一個元素.
- 開始將管線依照順序和另一個管線做 *合併*. 每次合併, 你會將內容排序. 因為每個做過合併的管線都是有序的, 所以每次合併滿容易的.

<!--
## An example

### Splitting

Assume you are given an array of *n* numbers as`[2, 1, 5, 4, 9]`. This is an unsorted pile. The goal is to keep splitting the pile until you cannot split anymore. 

First, split the array into two halves: `[2, 1]` and `[5, 4, 9]`. Can you keep splitting them? Yes, you can!

Focus on the left pile. Split`[2, 1]` into `[2]` and `[1]`. Can you keep splitting them? No. Time to check the other pile.

Split `[5, 4, 9]` into `[5]` and `[4, 9]`. Unsurprisingly, `[5]` cannot be split anymore, but `[4, 9]` can be split into `[4]` and `[9]`. 

The splitting process ends with the following piles: `[2]` `[1]` `[5]` `[4]` `[9]`. Notice that each pile consists of just one element.
-->

## 一個範例
### 分割

讓我們假設要排序的陣列是 `[2, 1, 5, 4, 9]`. 這是你的未排序管線. 目的是一直對半分割到不能分割為止.

首先, 先把陣列分成兩半: `[2, 1]` 和  `[5, 4, 9]`. 還可繼續分割嗎? 當然可以!

先處理左邊的管線. `[2, 1]` 會分割成 `[2]` 和 `[1]`. 還可繼續分割嗎? 不. 是時候分割另一個管線了.

`[5, 4, 9]` 分割成 `[5]` 和 `[4, 9]`. 不意外的, `[5]` 不能再分割了, 但是 `[4, 9]` 還可以分割成 `[4]` 和 `[9]`.

這個分割的程序持續到管線變成這樣: `[2]` `[1]` `[5]` `[4]` `[9]`. 注意到每個管線中只有一個元素.

<!--
### Merging

Now that you have split the array, you should **merge** the piles together **while sorting them**. Remember, the idea is to solve many small problems rather than a big one. For each merge iteration, you must be concerned at merging one pile with another.

Given the piles `[2]` `[1]` `[5]` `[4]` `[9]`, the first pass will result in `[1, 2]` and `[4, 5]` and `[9]`. Since `[9]` is the odd one out, you cannot merge it with anything during this pass. 

The next pass will merge `[1, 2]` and `[4, 5]` together. This results in `[1, 2, 4, 5]`, with the `[9]` left out again because it is the odd one out. 

You are left with only two piles and `[9]`, finally gets its chance to merge, resulting in the sorted array as `[1, 2, 4, 5, 9]`. 

## Top-down implementation

Here's what merge sort may look like in Swift:
-->

### 合併

現在你有分割後的陣列了, 你要邊 **排序** 邊 **合併** 他們. 記住, 概念是解決每個小問題, 而不是一次處理一個大問題. 每次合併迭代只會考慮將一個管線和另一個管線合併.

給定管線 `[2]` `[1]` `[5]` `[4]` `[9]`, 一開始會合併成 `[1, 2]` 和 `[4, 5]` 和 `[9]`. 因為 `[9]` 是目前唯一單一元素的.

下一步要合併 `[1, 2]` 和 `[4, 5]`. 這會得到 `[1, 2, 4, 5]` 和 `[9]`.

最後只剩下兩個管線, `[9]` 終於有機會合併了, 最後得到 `[1, 2, 4, 5, 9]`.

## 以上實作

這是合併排序在 Swift 中的實作:

```swift
func mergeSort(_ array: [Int]) -> [Int] {
  guard array.count > 1 else { return array }    // 1

  let middleIndex = array.count / 2              // 2

  let leftArray = mergeSort(Array(array[0..<middleIndex]))             // 3

  let rightArray = mergeSort(Array(array[middleIndex..<array.count]))  // 4

  return merge(leftPile: leftArray, rightPile: rightArray)             // 5
}
```

<!--
A step-by-step explanation of how the code works:

1. If the array is empty or contains a single element, there is no way to split it into smaller pieces. You must just return the array.

2. Find the middle index. 

3. Using the middle index from the previous step, recursively split the left side of the array.

4. Also, recursively split the right side of the array.

5. Finally, merge all the values together, making sure that it is always sorted.

Here's the merging algorithm:
-->

一步一步的解釋程式碼如何運作:

1. 如果陣列是空的或只包含一個元素, 那不需要做分割. 直接回傳該陣列.
2. 使用前一步得到的中間索引, 遞迴的分割左邊的陣列
3. 一樣的, 遞迴的分割右側的陣列
4. 最後, 合併所有的值, 並確保合併後的陣列必定要是有序的.

以下是合併演算法:

```swift
func merge(leftPile: [Int], rightPile: [Int]) -> [Int] {
  // 1
  var leftIndex = 0
  var rightIndex = 0

  // 2 
  var orderedPile = [Int]()

  // 3
  while leftIndex < leftPile.count && rightIndex < rightPile.count {
    if leftPile[leftIndex] < rightPile[rightIndex] {
      orderedPile.append(leftPile[leftIndex])
      leftIndex += 1
    } else if leftPile[leftIndex] > rightPile[rightIndex] {
      orderedPile.append(rightPile[rightIndex])
      rightIndex += 1
    } else {
      orderedPile.append(leftPile[leftIndex])
      leftIndex += 1
      orderedPile.append(rightPile[rightIndex])
      rightIndex += 1
    }
  }

  // 4
  while leftIndex < leftPile.count {
    orderedPile.append(leftPile[leftIndex])
    leftIndex += 1
  }

  while rightIndex < rightPile.count {
    orderedPile.append(rightPile[rightIndex])
    rightIndex += 1
  }

  return orderedPile
}
```


<!--

This method may look scary, but it is quite straightforward:


1. You need two indexes to keep track of your progress for the two arrays while merging.

2. This is the merged array. It is empty right now, but you will build it up in subsequent steps by appending elements from the other arrays.

3. This while-loop will compare the elements from the left and right sides and append them into the `orderedPile` while making sure that the result stays in order.

4. If control exits from the previous while-loop, it means that either the `leftPile` or the `rightPile` has its contents completely merged into the `orderedPile`. At this point, you no longer need to do comparisons. Just append the rest of the contents of the other array until there is no more to append.

As an example of how `merge()` works, suppose that we have the following piles: `leftPile = [1, 7, 8]` and `rightPile = [3, 6, 9]`. Note that each of these piles is individually sorted already -- that is always true with merge sort. These are merged into one larger sorted pile in the following steps:
-->

這個方法看起來挺嚇人, 但是其實滿直觀的:

1. 你需要兩個索引來追蹤合併中兩個陣列的進度.
2. 這個合併後的陣列, 目前是空的, 你會一步一步地把元素插入到此陣列中.
3. 這個 while 迴圈會兩兩比對左半和右半元素, 然後把元素插入到 `orderedPile`, 並確保插入後是有序的.
4. 如果跳出了前一個迴圈, 表示 `leftPile` 或 `rightPile` 至少有一個內的元素已經完全轉移到 `orderedPile` 中了. 此時, 你不需要再做元素比對, 只需要單純的把剩下來的元素插入到有序陣列中.

一個範例展示 `merge()` 動作流程, 假設我們有以下管線: `leftPile = [1, 7, 8]` 和 `rightPile = [3, 6, 9]`. 注意到每個管線個別都已經排序了 -- 在合併排序中這是常有的狀況. 這兩個管線合併到一個大的有序陣列依照以下步驟:

	leftPile       rightPile       orderedPile
	[ 1, 7, 8 ]    [ 3, 6, 9 ]     [ ]
      l              r

<!--
The left index, here represented as `l`, points at the first item from the left pile, `1`. The right index, `r`, points at `3`. Therefore, the first item we add to `orderedPile` is `1`. We also move the left index `l` to the next item.
-->
左邊的索引以 `l` 代表, 現在指向左管線的第一個位置, `1`. 右邊索引以 `r` 代表, 指向 `3`. 因此, 我們第一個加入到 `orderedPile` 的元素是 `1`. 然後把 `l` 往前一個位置.


	leftPile       rightPile       orderedPile
	[ 1, 7, 8 ]    [ 3, 6, 9 ]     [ 1 ]
      -->l           r


<!--
Now `l` points at `7` but `r` is still at `3`. We add the smallest item to the ordered pile, so that's `3`. The situation is now:
-->

現在 `l` 指向 `7`, 而 `r` 還是指向 `3`. 我們要將最小的元素插入有序管線中, 所以這次選 `3`. 那現在情況是這樣:


	leftPile       rightPile       orderedPile
	[ 1, 7, 8 ]    [ 3, 6, 9 ]     [ 1, 3 ]
         l           -->r


<!--
This process repeats. At each step, we pick the smallest item from either the `leftPile` or the `rightPile` and add the item into the `orderedPile`:
-->

這個流程持續重複. 在每個步驟中我們都在 `leftPile` 和 `rightPile` 中選取最小的元素然後插入到 `orderedPile`:


	leftPile       rightPile       orderedPile
	[ 1, 7, 8 ]    [ 3, 6, 9 ]     [ 1, 3, 6 ]
         l              -->r
	
	leftPile       rightPile       orderedPile
	[ 1, 7, 8 ]    [ 3, 6, 9 ]     [ 1, 3, 6, 7 ]
         -->l              r
	
	leftPile       rightPile       orderedPile
	[ 1, 7, 8 ]    [ 3, 6, 9 ]     [ 1, 3, 6, 7, 8 ]
            -->l           r


<!--
Now, there are no more items in the left pile. We simply add the remaining items from the right pile, and we are done. The merged pile is `[ 1, 3, 6, 7, 8, 9 ]`. 

Notice that, this algorithm is very simple: it moves from left-to-right through the two piles and at every step picks the smallest item. This works because we guarantee that each of the piles is already sorted.
-->

現在左邊管線已經沒東西了. 接下來簡單的把右邊管線的元素插入到有序管線中, 就完成了. 合併後的管線是 `[ 1, 3, 6, 7, 8, 9]`.

注意到這個演算法非常簡單: 兩個管線從左到右一個一個元素走訪並比對較小的. 當然這樣保證可以成功是因為兩個管線本身都已經是有序的.


<!--
## Bottom-up implementation

The implementation of the merge-sort algorithm you have seen so far is called the "top-down" approach because it first splits the array into smaller piles and then merges them. When sorting an array (as opposed to, say, a linked list) you can actually skip the splitting step and immediately start merging the individual array elements. This is called the "bottom-up" approach.

Time to step up the game a little. :-) Here is a complete bottom-up implementation in Swift:
-->

## 由下至上實作

上面看到合併排序的實作是 "由上至下" 的, 因為它是先將陣列分割成小的管線然後合併他們. 當在排序陣列的時候, 你其實可以跳過分割的步驟直接進入合併每個元素. 這稱為 "由下至上".

是時候來玩玩這個遊戲. 這是完整的 "由下至上" 在 Swift 中的實作:


```swift
func mergeSortBottomUp<T>(_ a: [T], _ isOrderedBefore: (T, T) -> Bool) -> [T] {
  let n = a.count

  var z = [a, a]      // 1
  var d = 0
    
  var width = 1
  while width < n {   // 2
  
    var i = 0
    while i < n {     // 3

      var j = i
      var l = i
      var r = i + width
      
      let lmax = min(l + width, n)
      let rmax = min(r + width, n)
      
      while l < lmax && r < rmax {                // 4
        if isOrderedBefore(z[d][l], z[d][r]) {
          z[1 - d][j] = z[d][l]
          l += 1
        } else {
          z[1 - d][j] = z[d][r]
          r += 1
        }
        j += 1
      }
      while l < lmax {
        z[1 - d][j] = z[d][l]
        j += 1
        l += 1
      }
      while r < rmax {
        z[1 - d][j] = z[d][r]
        j += 1
        r += 1
      }

      i += width*2
    }
    
    width *= 2
    d = 1 - d      // 5
  }
  return z[d]
}
```

<!--
It looks a lot more intimidating than the top-down version but notice that the main body includes the same three `while` loops from `merge()`.


Notable points:

1. The Merge-sort algorithm needs a temporary working array because you cannot merge the left and right piles and at the same time overwrite their contents. Because allocating a new array for each merge is wasteful, we are using two working arrays, and we will switch between them using the value of `d`, which is either 0 or 1. The array `z[d]` is used for reading, and `z[1 - d]` is used for writing. This is called *double-buffering*.

2. Conceptually, the bottom-up version works the same way as the top-down version. First, it merges small piles of one element each, then it merges piles of two elements each, then piles of four elements each, and so on. The size of the pile is given by `width`. Initially, `width` is `1` but at the end of each loop iteration, we multiply it by two, so this outer loop determines the size of the piles being merged, and the subarrays to merge become larger in each step.

3. The inner loop steps through the piles and merges each pair of piles into a larger one. The result is written in the array given by `z[1 - d]`.

4. This is the same logic as in the top-down version. The main difference is that we're using double-buffering, so values are read from `z[d]` and written into `z[1 - d]`. It also uses an `isOrderedBefore` function to compare the elements rather than just `<`, so this merge-sort algorithm is generic, and you can use it to sort any kind of object you want.

5. At this point, the piles of size `width` from array `z[d]` have been merged into larger piles of size `width * 2` in array `z[1 - d]`. Here, we swap the active array, so that in the next step we'll read from the new piles we have just created.

This function is generic, so you can use it to sort any type you desire, as long as you provide a proper `isOrderedBefore` closure to compare the elements.

Example of how to use it:
-->

看起來比由上至下還恐怖, 但是主體還是一樣的三個 `while` 迴圈.

值得注意的幾點:

1. 合併排序需要一個暫存的陣列, 因為你無法同時合併和覆寫左邊和右邊管線的元素. 但是每次合併都要配置新的陣列空間挺浪費. 因此, 我們使用兩個暫存的陣列, 然後用變數 `d` 來切換他們, `d` 只會是 0 或 1. 而陣列 `z[d]` 是用來讀取, `z[1 - d]` 是用來寫入. 這稱為 *雙重緩衝*.
2. 概念上, 由下至上的版本和由上至下是一樣的. 首先, 它將最小的單元素管線合併, 然後再將雙元素管線合併, 然後四元素管線, 以此類推. 管線大小以 `width` 變數來掌握. 初始時, `width` 是 `1` 但是在每個迴圈結尾我們將它乘以 2. 所以外部迴圈決定了要合併管線的大小. 每次迭代, 要合併的子陣列就越來越大.
3. 內部迴圈走訪管線然後將它們合併成更大的管線. 合併結果是寫入到 `z[1 - d]`.
4. 這部分跟由上至下版本的邏輯是一樣的. 不同的地方是我們使用了雙重緩衝, 所以是從 `z[d]` 中讀取資料, 然後寫入到 `z[1 - d]`. 它也使用了 `isOrderedBefore` 函式來比較兩個元素的大小, 這讓合併排序更泛型, 你可以排序任何型態的物件.
5. 至此, 從 `z[d]` 裡面 `width` 大小的陣列已經合併到 `z[1 - 2]` 中 `width * 2` 大小的陣列中. 在這連我們交換了進行中的陣 (變換 `d`), 讓我們下個階段可以讀取我們建立的新管線.

此函式是泛型的, 所以你可以將它用於任何型態得資料, 只要你實作適當的 `isOrderedBefore` 閉包去比較元素.

使用範例:

```swift
let array = [2, 1, 5, 4, 9]
mergeSortBottomUp(array, <)   // [1, 2, 4, 5, 9]
```

<!--
## Performance

The speed of the merge-sort algorithm is dependent on the size of the array it needs to sort. The larger the array, the more work it needs to do. 

Whether or not the initial array is sorted already doesnot affect the speed of the merge-sort algorithm since you will be doing the same amount splits and comparisons regardless of the initial order of the elements.

Therefore, the time complexity for the best, worst, and average case will always be **O(n log n)**. 

A disadvantage of the merge-sort algorithm is that it needs a temporary "working" array equal in size to the array being sorted. It is not an **in-place** sort, unlike for example [quicksort](../Quicksort/).


Most implementations of the merge-sort algorithm produce a **stable** sort. This means that array elements that have identical sort keys will stay in the same order relative to each other after sorting. This is not important for simple values such as numbers or strings, but it can be an issue when sorting more complex objects.
-->

## 表現

合併排序的速度和陣列的大小有關. 陣列越大, 那就會要做越久.

一開始的陣列有沒有排序過並沒有影響合併的速度, 因為你一樣會做分割跟比對的動作.

因此, 時間複雜度在最好, 最糟, 跟平均的狀況下都是 **O(n log n)**.

合併排序的一個缺點是它需要一個和最後有序陣列大小相同的暫存陣列. 它不是個 **內部排序**, 和 [快速排序](../Quicksort/) 不太一樣.

大部分合併排序的實作都是 **穩定** 排序. 意思是元素有唯一順序識別了話, 那排序後每個元素之間都能保持該順序. 這對簡單的值不太重要, 像是字串或數值, 但是對複雜的物件來說, 就是個課題.


<!--
## See also
-->

## 相關閱讀
[Merge sort on Wikipedia](https://en.wikipedia.org/wiki/Merge_sort)

*Written by Kelvin Lau. Additions by Matthijs Hollemans.*
