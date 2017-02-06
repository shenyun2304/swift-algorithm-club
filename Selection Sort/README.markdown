# Selection Sort (選擇排序)

<!--
Goal: Sort an array from low to high (or high to low).

You are given an array of numbers and need to put them in the right order. The selection sort algorithm divides the array into two parts: the beginning of the array is sorted, while the rest of the array consists of the numbers that still remain to be sorted. 
-->

目標: 將陣列做遞增或遞減排序.

選擇排序會將陣列分為兩個部分: 陣列前端是有序的, 而剩下來的部分是等待被排序的.

	[ ...sorted numbers... | ...unsorted numbers... ]

<!--
This is similar to [insertion sort](../Insertion Sort/), but the difference is in how new numbers are added to the sorted portion.

It works as follows:

- Find the lowest number in the array. You start at index 0, loop through all the numbers in the array, and keep track of what the lowest number is.
- Swap the lowest number you've found with the number at index 0. Now the sorted portion consists of just the number at index 0.
- Go to index 1.
- Find the lowest number in the rest of the array. This time you start looking from index 1. Again you loop until the end of the array and keep track of the lowest number you come across.
- Swap it with the number at index 1. Now the sorted portion contains two numbers and extends from index 0 to index 1.
- Go to index 2.
- Find the lowest number in the rest of the array, starting from index 2, and swap it with the one at index 2. Now the array is sorted from index 0 to 2; this range contains the three lowest numbers in the array.
- And so on... until no numbers remain to be sorted.

It's called a "selection" sort, because at every step you search through the rest of the array to select the next lowest number.
-->

這和 [插入排序](../Insertion Sort/) 很像, 差異在於插入新元素到有序陣列時的動作不同.

流程如下:

- 找到陣列中最小的元素. 從索引 0 開始, 走訪整個陣列的每個元素, 追蹤當下走訪過的最小元素.
- 將找到的最小元素和索引 0 的元素交換位置. 現在有序的部分只有索引 0.
- 移動到索引 1.
- 找到剩下陣列中最小的元素. 這次從索引 1 開始. 再一次迭代走訪整個陣列, 一樣紀錄最小的元素.
- 將找到的最小元素和索引 1 的元素交換位置. 現在有序的部分到了索引 1.
- 移動到索引 2.
- 找到剩下陣列中最小的元素. 這次從索引 2 開始. 再一次迭代走訪整個陣列, 一樣紀錄最小的元素, 將找到的最小元素和索引 2 的元素交換位置. 現在有序的部分到了索引 2.
- 一直持續到沒有元素需要排序.

這個方法稱作 "選擇排序", 因為在每個步驟中都會走訪剩下陣列中的每個元素來找到最小元素.

<!--
## An example

Let's say the numbers to sort are `[ 5, 8, 3, 4, 6 ]`. We also keep track of where the sorted portion of the array ends, denoted by the `|` symbol. 

Initially, the sorted portion is empty:
-->

## 範例

假設陣列是 `[ 5, 8, 3, 4, 6]`. 我們使用 `|` 符號來表示有序及無序部分的分隔.

初始時, 有序的部分是空的:

	[| 5, 8, 3, 4, 6 ]

<!--
Now we find the lowest number in the array. We do that by scanning through the array from left to right, starting at the `|` bar. We find the number `3`.

To put this number into the sorted position, we swap it with the number next to the `|`, which is `5`:
-->


現在我們要找到陣列中的最小元素. 從左邊到右邊掃描陣列, 我們找到 `3`.

要把這個元素放到有序的部分, 我們把它和 `|` 右邊的元素交換, 也就是 `5`:

	[ 3 | 8, 5, 4, 6 ]
	  *      *

<!--
The sorted portion is now `[ 3 ]` and the rest is `[ 8, 5, 4, 6 ]`.

Again, we look for the lowest number, starting from the `|` bar. We find `4` and swap it with `8` to get:
-->

現在有序的部分是 `[ 3 ]`, 而剩下的是 `[ 8, 5, 4, 6 ]`.

再一次, 我們搜尋最小的元素, 從 `|` 開始. 我們找到 `4` 並把它和 `8` 交換:

	[ 3, 4 | 5, 8, 6 ]
	     *      *

<!--
With every step, the `|` bar moves one position to the right. We again look through the rest of the array and find `5` as the lowest number. There's no need to swap `5` with itself and we simply move forward:
-->

每個步驟結束後, `|` 都會向右移動一個位置. 再一次走訪剩下的陣列, 我們得到 `5` 是最小元素. 這時候不需要把 `5` 和自己交換, 簡單的移動一個位置就好:

	[ 3, 4, 5 | 8, 6 ]
	        *

<!--
This process repeats until the array is sorted. Note that everything to the left of the `|` bar is always in sorted order and always contains the lowest numbers in the array. Finally, we end up with:
-->

這個流程持續重複直到整個陣列都排序完. 注意到只要是在 `|` 左邊的元素都是有序的, 而且一定包含整個陣列最小的元素. 最後我們得到:

	[ 3, 4, 5, 6, 8 |]

<!--
As you can see, selection sort is an *in-place* sort because everything happens in the same array; no additional memory is necessary. You can also implement this as a *stable* sort so that identical elements do not get swapped around relative to each other (note that the version given below isn't stable).
-->

可以看到, 選擇排序是個 *內部排序*, 因為所有事情在原陣列中進行; 不需要用到更多的記憶體. 也可以用 *穩定排序* 來實作, 讓每個元素之間的相對順序不會因為交換而改變 (注意到下面的版本並不是穩定的).

<!--
## The code

Here is an implementation of selection sort in Swift:
-->


## 程式碼

在 Swift 中的實作 (非穩定):

```swift
func selectionSort(_ array: [Int]) -> [Int] {
  guard array.count > 1 else { return array }  // 1

  var a = array                    // 2

  for x in 0 ..< a.count - 1 {     // 3
    
    var lowest = x
    for y in x + 1 ..< a.count {   // 4
      if a[y] < a[lowest] {
        lowest = y
      }
    }
    
    if x != lowest {               // 5
      swap(&a[x], &a[lowest])
    }
  }
  return a
}
```

<!--
Put this code in a playground and test it like so:
-->

把這程式碼放到 playground 中測試看看:

```swift
let list = [ 10, -1, 3, 9, 2, 27, 8, 5, 1, 3, 0, 26 ]
selectionSort(list)
```

<!--
A step-by-step explanation of how the code works:

1. If the array is empty or only contains a single element, then there's not much point to sorting it.

2. Make a copy of the array. This is necessary because we cannot modify the contents of the `array` parameter directly in Swift. Like Swift's own `sort()`, the `selectionSort()` function will return a sorted *copy* of the original array.

3. There are two loops inside this function. The outer loop looks at each of the elements in the array in turn; this is what moves the `|` bar forward.

4. This is the inner loop. It finds the lowest number in the rest of the array.

5. Swap the lowest number with the current array index. The `if` check is necessary because you can't `swap()` an element with itself in Swift.

In summary: For each element of the array, selection sort swaps positions with the lowest value from the rest of the array. As a result, the array gets sorted from the left to the right. (You can also do it right-to-left, in which case you always look for the largest number in the array. Give that a try!)

> **Note:** The outer loop ends at index `a.count - 2`. The very last element will automatically always be in the correct position because at that point there are no other smaller elements left.

The source file [SelectionSort.swift](SelectionSort.swift) has a version of this function that uses generics, so you can also use it to sort strings and other types.
-->

一步一步的解釋:

1. 如果陣列是空的或者只有一個元素, 不需要做什麼排序.
2. 建立陣列的副本. 會做這個是因為在 Swift 中無法直接改變傳入的參數 `array`. 就像 Swift 原生的 `sort()`, `selectionSort()` 回傳的是原陣列的 *副本*.
3. 這邊有兩個迴圈. 外部迴圈依序地走訪每個元素; 這層就是移動 `|`.
4. 這裡是內部迴圈, 這層會找到剩下陣列中最小的元素.
5. 將最小的元素和目前的索引交換位置. `if` 是必須的, 因為不能將元素和自己 `swap()`.

小結: 對於每個陣列中的元素, 選擇排序從剩下的陣列中把最小元素換出來. 結果是, 陣列會形成遞增排序. (你也可以做成遞減排序, 在搜尋時改成搜尋最大元素. 試試看!)

> **注意:** 外部迴圈最後停在 `a.count - 2`. 最後一個元素總是會自動地在正確的位置, 因為在那個時候不會有其他更小的元素了.

原始檔案 [SelectionSort.swift](Selection.swift) 中有此方法的泛型版本, 所以可以拿來做字串或其他型別的排序.

<!--
## Performance

Selection sort is easy to understand but it performs quite badly, **O(n^2)**. It's worse than [insertion sort](../Insertion Sort/) but better than [bubble sort](../Bubble Sort/). The killer is  finding the lowest element in the rest of the array. This takes up a lot of time, especially since the inner loop will be performed over and over.

[Heap sort](../Heap Sort/) uses the same principle as selection sort but has a really fast method for finding the minimum value in the rest of the array. Its performance is **O(n log n)**.
-->

## 表現

選擇排序很好理解但是表現上滿糟的, **O(n^2)**. 比 [插入排序](../Insertion Sort/) 還遭不過比 [氣泡排序](../Bubble Sort/) 還好. 關鍵在於從剩下的陣列中找到最小的元素. 這消耗大量的時間, 尤其是內部迴圈會一直不斷的重複.

[堆積排序](../Heap Sort/) 使用相同的原則, 但是在查找最小元素時快很多. 它的時間複雜度是 **O(n log n)**.

<!--
## See also
-->
## 相關閱讀

[Selection sort on Wikipedia](https://en.wikipedia.org/wiki/Selection_sort)

*Written for Swift Algorithm Club by Matthijs Hollemans*
