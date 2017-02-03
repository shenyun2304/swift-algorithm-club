# Select Minimum / Maximum (選取最小 / 最大)

<!--
Goal: Find the minimum/maximum object in an unsorted array.
-->

目標: 找到無序陣列中最小 / 最大的物件.

<!--
## Maximum or minimum

We have an array of generic objects and we iterate over all the objects keeping track of the minimum/maximum element so far.
-->

## 最大 或 最小

我們會迭代所有的元素並紀錄已經追蹤過的最小 / 最大值.

<!--
### An example

Let's say the we want to find the maximum value in the unsorted list `[ 8, 3, 9, 4, 6 ]`.

Pick the first number, `8`, and store it as the maximum element so far. 

Pick the next number from the list, `3`, and compare it to the current maximum. `3` is less than `8` so the maximum `8` does not change.

Pick the next number from the list, `9`, and compare it to the current maximum. `9` is greater than `8` so we store `9` as the maximum.

Repeat this process until the all elements in the list have been processed.
-->

### 範例

假設我們想要找到無序陣列 `[ 8, 3, 9, 4, 6 ]` 中的最大值.

先看到第一個數字 `8`, 然後將它存起來當作目前為止最大的元素.

走訪下一個數字, `3`, 然後將它和目前最大元素比較. `3` 比 `8` 小, 所以到目前為止最大的還是 `8`.

再走訪下一個數字, `9`, 然後將它和目前最大值比較, `9` 比 `8` 大, 所以 `9` 就是到目前為止最大的數.

重複上面步驟直到走訪完所有元素.

<!--
### The code

Here is a simple implementation in Swift:
-->

### 程式碼

Swift 中的實作:

```swift
func minimum<T: Comparable>(var array: [T]) -> T? {
  guard !array.isEmpty else {
    return nil
  }

  var minimum = array.removeFirst()
  for element in array {
    minimum = element < minimum ? element : minimum
  }
  return minimum
}

func maximum<T: Comparable>(var array: [T]) -> T? {
  guard !array.isEmpty else {
    return nil
  }

  var maximum = array.removeFirst()
  for element in array {
    maximum = element > maximum ? element : maximum
  }
  return maximum
}
```

<!--
Put this code in a playground and test it like so:
-->

在 playground 中玩玩:

```swift
let array = [ 8, 3, 9, 4, 6 ]
minimum(array)   // This will return 3
maximum(array)   // This will return 9
```

<!--
### In the Swift standard library

The Swift library already contains an extension to `SequenceType` that returns the minimum/maximum element in a sequence.
-->

### 在 Swift 標準函式庫中

在 Swift 原生函式庫中已經有個 `SequenceType` 的擴充, 可以回傳序列中的最小 / 最大值.

```swift
let array = [ 8, 3, 9, 4, 6 ]
array.minElement()   // This will return 3
array.maxElement()   // This will return 9
```

<!--
## Maximum and minimum

To find both the maximum and minimum values contained in array while minimizing the number of comparisons we can compare the items in pairs. 
-->

## 最大值 和 最小值

我們可以用兩兩比對來減少同時要找最小值和最大值的走訪次數.

<!--
### An example

Let's say the we want to find the minimum and maximum value in the unsorted list `[ 8, 3, 9, 6, 4 ]`.

Pick the first number, `8`, and store it as the minimum and maximum element so far. 

Because we have an odd number of items we remove `8` from the list which leaves the pairs `[ 3, 9 ]` and `[ 6, 4 ]`.

Pick the next pair of numbers from the list, `[ 3, 9 ]`. Of these two numbers, `3` is the smaller one, so we compare `3` to the current minimum `8`, and we compare `9` to the current maximum `8`. `3` is less than `8` so the new minimum is `3`. `9` is greater than `8` so the new maximum is `9`.

Pick the next pair of numbers from the list, `[ 6, 4 ]`. Here, `4` is the smaller one, so we compare `4` to the current minimum `3`, and we compare `6` to the current maximum `9`. `4` is greater than `3` so the minimum does not change. `6` is less than `9` so the maximum does not change.

The result is a minimum of `3` and a maximum of `9`.
-->

### 範例

假設我們想找到無序列表 `[ 8, 3, 9, 6, 4 ]` 的最小和最大值.

先取得第一個數字, `8`, 然後設定她為目前最小值也是最大值.

因為陣列總共有奇數個元素, 我們移除 `8` 讓剩下來的元素可以兩兩組合成 `[ 3, 9 ]` 和 `[ 6, 4 ]`.

選取下一個數字配對, `[ 3, 9 ]`. 在這兩個數字中, `3` 是比較小的那個, 所以我們將 `3` 和目前最小值 `8` 比較, 然後拿 `9` 和當前最大值 `8` 比較. `3` 比 `8` 小, 所以新的最小值就是 `3`. `9` 比 `8` 還大所以新的最大值就是 `9`.

再選取下一個數字配對, `[ 6, 4 ]`. 這次, `4` 是兩個數中比較小的, 所以我們將 `4` 和當前最小值 `3` 比較, 然後拿 `6` 和當前最大值 `9` 比較. `4` 比 `3` 大所以最小值不變, `6` 比 `9` 小所以最大值也不便.

結果最小值是 `3` 而最大值是 `9`.

<!--
### The code

Here is a simple implementation in Swift:
-->

### 程式碼

在 Swift 中簡單的實作:


```swift
func minimumMaximum<T: Comparable>(var array: [T]) -> (minimum: T, maximum: T)? {
  guard !array.isEmpty else {
    return nil
  }

  var minimum = array.first!
  var maximum = array.first!

  let hasOddNumberOfItems = array.count % 2 != 0
  if hasOddNumberOfItems {
    array.removeFirst()
  }

  while !array.isEmpty {
    let pair = (array.removeFirst(), array.removeFirst())
    if pair.0 > pair.1 {
      if pair.0 > maximum {
        maximum = pair.0
      }
      if pair.1 < minimum {
        minimum = pair.1
      }
    } else {
      if pair.1 > maximum {
        maximum = pair.1
      }
      if pair.0 < minimum {
        minimum = pair.0
      }
    }
  }

  return (minimum, maximum)
}
```


<!--
Put this code in a playground and test it like so:
-->

在 playground 中測試玩玩看:

```swift
let result = minimumMaximum(array)!
result.minimum   // This will return 3
result.maximum   // This will return 9
```

<!--
By picking elements in pairs and comparing their maximum and minimum with the running minimum and maximum we reduce the number of comparisons to 3 for every 2 elements.
-->

我們用兩兩比對的方式, 讓比對次數降到 3 次, 因為每次比對 2 個元素.

<!--
## Performance

These algorithms run at **O(n)**. Each object in the array is compared with the running minimum/maximum so the time it takes is proportional to the array length.
-->

## 表現

此演算法時間複雜度是 **O(n)**. 每個元素都會跟當前的最小 / 最大值比較, 所以總體時間受陣列長度影響.


*Written by [Chris Pilcher](https://github.com/chris-pilcher)*
