# Linear Search (線性搜尋)

<!--
Goal: Find a particular value in an array.

We have an array of generic objects. With linear search, we iterate over all the objects in the array and compare each one to the object we're looking for. If the two objects are equal, we stop and return the current array index. If not, we continue to look for the next object as long as we have objects in the array.
-->

目標: 在陣列中找到某個元素.

我們有個含有泛型元素的陣列. 在線性搜尋中, 我們對所有陣列中的元素迭代並比對我們想要找的元素. 如果走訪的物件跟搜尋的相等, 我們就停止迭代並回傳該元素在陣列中的索引. 如果比對不同, 就繼續走訪下一個物件直到找到或者已經走訪全部的元素.

<!--
## An example

Let's say we have an array of numbers `[5, 2, 4, 7]` and we want to check if the array contains the number `2`.

We start by comparing the first number in the array, `5`, to the number we're looking for, `2`. They are obviously not the same, and so we continue to the next array element.

We compare the number `2` from the array to our number `2` and notice they are equal. Now we can stop our iteration and return 1, which is the index of the number `2` in the array.
-->

## 範例

讓我們假設陣列為 `[5, 2, 4, 7]`, 而我們想要檢查陣列是否有包含 `2`.

我們從比對第一個元素開始, `5` 和我們要找的 `2`. 他們明顯不同, 所以我們往下一個元素走訪.

我們走訪到 `2`, 比對我們要找的 `2`. 他們相同. 現在我們可以停止迭代並回傳索引 `2` 在陣列中的索引 1.

<!--
## The code

Here is a simple implementation of linear search in Swift:
-->

## 程式碼

在 Swift 中簡單的實現線性搜尋:

```swift
func linearSearch<T: Equatable>(_ array: [T], _ object: T) -> Int? {
  for (index, obj) in array.enumerated() where obj == object {
    return index
  }
  return nil
}
```

<!--
Put this code in a playground and test it like so:
-->

在 playground 中測試看看:

```swift
let array = [5, 2, 4, 7]
linearSearch(array, 2) 	// This will return 1
```

<!--
## Performance

Linear search runs at **O(n)**. It compares the object we are looking for with each object in the array and so the time it takes is proportional to the array length. In the worst case, we need to look at all the elements in the array.

The best-case performance is **O(1)** but this case is rare because the object we're looking for has to be positioned at the start of the array to be immediately found. You might get lucky, but most of the time you won't. On average, linear search needs to look at half the objects in the array.
-->

## 表現

線性搜尋的時間複雜度是 **O(n)**. 他逐一得比對陣列中的元素, 所以會佔用的時間就跟陣列的長度有關. 最糟的情況, 我們會迭代所有元素.

最好的情況是 **O(1)**, 但這種情況很少, 因為你要找的元素必須是在陣列的最前端. 你可能有時候會很幸運, 但大部分的時間你不會. 平均而言, 線性搜尋還是要走訪陣列中一半的元素.

<!--
## See also
-->
## 相關閱讀

[Linear search on Wikipedia](https://en.wikipedia.org/wiki/Linear_search)

*Written by [Patrick Balestra](http://www.github.com/BalestraPatrick)*
