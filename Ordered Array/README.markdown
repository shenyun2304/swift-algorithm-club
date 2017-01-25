# Ordered Array (有序陣列)

<!--
This is an array that is always sorted from low to high. Whenever you add a new item to this array, it is inserted in its sorted position.

An ordered array is useful for when you want your data to be sorted and you're inserting new items relatively rarely. In that case, it's faster than sorting the entire array. However, if you need to change the array often, it's probably faster to use a regular array and sort it manually.

The implementation is quite basic. It's simply a wrapper around Swift's built-in array:
-->

這是一個順序是遞增的陣列. 你在任何時候插入一個元素進來, 它都會插入到有序的位置.

當你希望你的資料是排序的, 而很少會新增資料時, 有序陣列是挺好用的. 在這情況下, 會比整個陣列重新排序還快. 但是, 如果你常常改變陣列, 那使用一般陣列然後去排序可能比較快.

實作挺簡單. 包覆一個 Swift 原生的陣列:


```swift
public struct OrderedArray<T: Comparable> {
  fileprivate var array = [T]()

  public init(array: [T]) {
    self.array = array.sorted()
  }

  public var isEmpty: Bool {
    return array.isEmpty
  }

  public var count: Int {
    return array.count
  }

  public subscript(index: Int) -> T {
    return array[index]
  }

  public mutating func removeAtIndex(index: Int) -> T {
    return array.remove(at: index)
  }

  public mutating func removeAll() {
    array.removeAll()
  }
}

extension OrderedArray: CustomStringConvertible {
  public var description: String {
    return array.description
  }
}
```

<!--
As you can see, all these methods simply call the corresponding method on the internal `array` variable.

What remains is the `insert()` function. Here is an initial stab at it:
-->

可以看到, 所有的方法只是簡單的對 `array` 變數呼叫原生的方法而已.

至於 `insert()` 函式. 如以下:


```swift
  public mutating func insert(_ newElement: T) -> Int {
    let i = findInsertionPoint(newElement)
    array.insert(newElement, at: i)
    return i
  }

  private func findInsertionPoint(_ newElement: T) -> Int {
    for i in 0..<array.count {
      if newElement <= array[i] {
        return i
      }
    }
    return array.count  // insert at the end
  }
```

<!--
The helper function `findInsertionPoint()` simply iterates through the entire array, looking for the right place to insert the new element.

> **Note:** Quite conveniently, `array.insert(... atIndex: array.count)` adds the new object to the end of the array, so if no suitable insertion point was found we can simply return `array.count` as the index.

Here's how you can test it in a playground:
-->

`findInsertionPoint()` 簡單地走訪整個陣列, 直到找到正確的位置.

> **注意:** 非常方便的, `array.insert(... atIndex: array.count)` 把新元素插入到陣列的最尾端, 如果沒有合適的插入位置, 我們也可以簡單的回傳 `array.count` 來當插入的索引.

在 playground 中這樣玩玩:

```swift
var a = OrderedArray<Int>(array: [5, 1, 3, 9, 7, -1])
a              // [-1, 1, 3, 5, 7, 9]

a.insert(4)    // inserted at index 3
a              // [-1, 1, 3, 4, 5, 7, 9]

a.insert(-2)   // inserted at index 0
a.insert(10)   // inserted at index 8
a              // [-2, -1, 1, 3, 4, 5, 7, 9, 10]
```

<!--
The array's contents will always be sorted from low to high, now matter what.

Unfortunately, the current `findInsertionPoint()` function is a bit slow. In the worst case, it needs to scan through the entire array. We can speed this up by using a [binary search](../Binary Search) to find the insertion point.

Here is the new version:
-->

陣列的內容永遠會是有序的, 而且必定是遞增.

可惜的是, `findInsertionPoint()` 函式有點慢. 在最糟的情況下, 它需要走訪整個陣列. 我們可以使用 [二元搜尋](../Binary Search) 來加速找到插入索引位置.

新版本:

```swift
  private func findInsertionPoint(_ newElement: T) -> Int {
    var startIndex = 0
    var endIndex = array.count

    while startIndex < endIndex {
        let midIndex = startIndex + (endIndex - startIndex) / 2
        if array[midIndex] == newElement {
            return midIndex
        } else if array[midIndex] < newElement {
            startIndex = midIndex + 1
        } else {
            endIndex = midIndex
        }
    }
    return startIndex
  }
```

<!--
The big difference with a regular binary search is that this doesn't return `nil` when the value can't be found, but the array index where the element would have been. That's where we insert the new object.

Note that using binary search doesn't change the worst-case running time complexity of `insert()`. The binary search itself takes only **O(log n)** time, but inserting a new object in the middle of an array still involves shifting all remaining elements in memory. So overall, the time complexity is still **O(n)**. But in practice this new version definitely is a lot faster, especially on large arrays.
-->

和一般二元搜尋最大的不同是這邊的當找不到時不會回傳 `nil`, 而是回傳該元素應該插入的索引位置.

注意到使用二元搜尋並沒有改變 `insert()` 最糟情況下的時間複雜度. 二元搜尋本身是 **O(log n)**, 但是在陣列中間插入某個元素還是牽涉到移動所有之後元素的記憶體位置. 所以, 整體來說, 時間複雜度還是 **O(n)**. 但是實用上這個新版本一定快很多, 尤其是大陣列.

*Written for Swift Algorithm Club by Matthijs Hollemans*
