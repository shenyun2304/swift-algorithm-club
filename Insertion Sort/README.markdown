# Insertion Sort (插入排序)

<!--
Goal: Sort an array from low to high (or high to low).

You are given an array of numbers and need to put them in the right order. The insertion sort algorithm works as follows:

- Put the numbers on a pile. This pile is unsorted.
- Pick a number from the pile. It doesn't really matter which one you pick, but it's easiest to pick from the top of the pile. 
- Insert this number into a new array. 
- Pick the next number from the unsorted pile and also insert that into the new array. It either goes before or after the first number you picked, so that now these two numbers are sorted.
- Again, pick the next number from the pile and insert it into the array in the proper sorted position.
- Keep doing this until there are no more numbers on the pile. You end up with an empty pile and an array that is sorted.

That's why this is called an "insertion" sort, because you take a number from the pile and insert it in the array in its proper sorted position. 
-->

目標: 將陣列依照遞減 (或遞增) 排序

給定一個數字陣列, 任務是將它們排序. 插入排序演算法是這樣運作的:

- 把數字放到一個管線中. 這個管線是無序的.
- 從管線中拿一個數字. 拿哪一個沒有差別, 但是從管線的上方取出來比較簡單.
- 將數字插入一個新的陣列.
- 從無序的管線中拿出下一個數字並插入剛剛的新陣列. 這個數字要不是在剛剛那個數字的前面就是在後面, 所以現在這兩個數字已經排序了.
- 再一次, 取得下一個數字並在正確順序的地方插入這個新陣列.
- 持續這個動作直到管線中沒有任何數字. 你最後會將管線清空而該新陣列就是排序後的陣列.

這就是名為 "插入" 排序的原因, 從管線中拿出數字並插入到新陣列中正確順序的位置.

<!--
## An example

Let's say the numbers to sort are `[ 8, 3, 5, 4, 6 ]`. This is our unsorted pile.

Pick the first number, `8`, and insert it into the new array. There is nothing in that array yet, so that's easy. The sorted array is now `[ 8 ]` and the pile is `[ 3, 5, 4, 6 ]`.

Pick the next number from the pile, `3`, and insert it into the sorted array. It should go before the `8`, so the sorted array is now `[ 3, 8 ]` and the pile is reduced to `[ 5, 4, 6 ]`.

Pick the next number from the pile, `5`, and insert it into the sorted array. It goes in between the `3` and `8`. The sorted array is `[ 3, 5, 8 ]` and the pile is `[ 4, 6 ]`.

Repeat this process until the pile is empty.
-->

## 例子

讓我們假設要排序的是 `[ 8, 3, 5, 4, 6 ]`. 這是我們的無序管線.

取得第一個數字, `8`, 然後插入到新陣列. 目前該陣列中沒有任何元素, 所以很簡單, 這時候有序陣列是 `[ 8 ]`, 而管線是 `[ 3, 5, 4, 6 ]`.

取得下一個數字, `3`, 然後插入到有序陣列中. 它應該要在 `8` 之前, 所以有序陣列現在是 `[ 3, 8 ]`, 現在管線是 `[ 5, 4, 6 ]`.

取得下一個數字, `5`, 他應該在 `3` 和 `8` 之間. 有序陣列是  `[ 3, 5, 8 ]`, 管線是 `[ 4, 6 ]`.

持續重複直到管線清空.


<!--
## In-place sort

The above explanation makes it seem like you need two arrays: one for the unsorted pile and one that contains the numbers in sorted order.

But you can perform the insertion sort *in-place*, without having to create a separate array. You just keep track of which part of the array is sorted already and which part is the unsorted pile.

Initially, the array is `[ 8, 3, 5, 4, 6 ]`. The `|` bar shows where the sorted portion ends and the pile begins:
-->

## 內部排序

上面的解釋看起來需要兩個陣列: 一個給無序管線, 一個給有序陣列.

但你是可以將插入排序做 *內部化* 的作動, 不需要再創造另一個陣列. 你只需要追蹤陣列的哪些部分是已經排序過哪些還沒就好.

初始時, 陣列是 `[ 8, 3, 5, 4, 6 ]`. `|` 表示之前的是已排序, 之後的是未排序.

	[| 8, 3, 5, 4, 6 ]

<!--
This shows that the sorted portion is empty and the pile starts at `8`.

After processing the first number, we have:
-->

這樣表示有序的部分是空的, 而管線從 `8` 開始.

經過處理第一個數字, 我們得到:

	[ 8 | 3, 5, 4, 6 ]

<!--
The sorted portion is `[ 8 ]` and the pile is `[ 3, 5, 4, 6 ]`. The `|` bar has shifted one position to the right.

This is how the content of the array changes during the sort:
-->

這時候有序的部分是 `[ 8 ]` 而管線的部分是 `[ 3, 5, 4, 6 ]`. `|` 向右移動了一個位置.

所有動作像這樣:

	[| 8, 3, 5, 4, 6 ]
	[ 8 | 3, 5, 4, 6 ]
	[ 3, 8 | 5, 4, 6 ]
	[ 3, 5, 8 | 4, 6 ]
	[ 3, 4, 5, 8 | 6 ]
	[ 3, 4, 5, 6, 8 |]

<!--
In each step, the `|` bar moves up one position. As you can see, the beginning of the array up to the `|` is always sorted. The pile shrinks by one and the sorted portion grows by one, until the pile is empty and there are no more unsorted numbers left.
-->

每一步, `|` 就移動一個位置. 可以看到, 陣列的開始到 `|` 之間都是排序過的. 管線也每一步少一個元素, 有序的部分多一個元素, 直到管線的部分是空的, 排序就完成了.

<!--
## How to insert

At each step you pick the top-most number from the unsorted pile and insert it into the sorted portion of the array. You must put that number in the proper place so that the beginning of the array remains sorted. How does that work?

Let's say we've already done the first few elements and the array looks like this:
-->

## 如何插入

在每個步驟中你從無序管線中取最前端的數字, 然後將它插入的有序的部分. 你必須將此數字放到正確的位置以保證陣列前端部分保持有序.
這怎麼辦到?

讓我們假設我們已經完成前端一些元素, 而現在陣列像這樣:

	[ 3, 5, 8 | 4, 6 ]

<!--
The next number to sort is `4`. We need to insert that into the sorted portion `[ 3, 5, 8 ]` somewhere. 

Here's one way to do this: Look at the previous element, `8`. 
-->

下一個數字是 `4`. 我們要將它插入到有序的部分 `[ 3, 5, 8 ]`. 

有個方法達成: 比對前一個元素 `8`.


	[ 3, 5, 8, 4 | 6 ]
	        ^
	        
<!--
Is this greater than `4`? Yes it is, so the `4` should come before the `8`. We swap these two numbers to get:
-->

他有大於 `4` 嗎? 有的, 所以 `4` 應該在 `8` 之前. 我們將這兩個數字交換位置得到:

	[ 3, 5, 4, 8 | 6 ]
	        <-->
	       swapped

<!--
We're not done yet. The new previous element, `5`, is also greater than `4`. We also swap these two numbers:
-->

我們還沒結束. 再往前看一個元素 `5`, 也比 `4` 大. 所以再次交換這兩個元素.

	[ 3, 4, 5, 8 | 6 ]
	     <-->
	    swapped

<!--
Again, look at the previous element. Is `3` greater than `4`? No, it is not. That means we're done with number `4`. The beginning of the array is sorted again.

This was a description of the inner loop of the insertion sort algorithm, which you'll see in the next section. It inserts the number from the top of the pile into the sorted portion by swapping numbers.
-->

再往前看一個元素, `3`. 這時候 `3` 小於 `4`. 那表示我們已經處理完 `4` 了. 陣列再次前端保持有序.

這裡描述了內部迭代和插入排序演算法, 這些接下來都可以看到. 這種方法用交換數字來讓陣列前端排序.

<!--
## The code

Here is an implementation of insertion sort in Swift:
-->

## 程式碼

這是 Swift 中插入排序的實作:

```swift
func insertionSort(_ array: [Int]) -> [Int] {
  var a = array                             // 1
  for x in 1..<a.count {                    // 2
    var y = x
    while y > 0 && a[y] < a[y - 1] {        // 3
      swap(&a[y - 1], &a[y])
      y -= 1
    }
  }
  return a
}
```

<!--
Put this code in a playground and test it like so:
-->

在 playground 中測試看看:

```swift
let list = [ 10, -1, 3, 9, 2, 27, 8, 5, 1, 3, 0, 26 ]
insertionSort(list)
```

<!--
Here is how the code works.

1. Make a copy of the array. This is necessary because we cannot modify the contents of the `array` parameter directly. Like Swift's own `sort()`, the `insertionSort()` function will return a sorted *copy* of the original array.

2. There are two loops inside this function. The outer loop looks at each of the elements in the array in turn; this is what picks the top-most number from the pile. The variable `x` is the index of where the sorted portion ends and the pile begins (the position of the `|` bar). Remember, at any given moment the beginning of the array -- from index 0 up to `x` -- is always sorted. The rest, from index `x` until the last element, is the unsorted pile.

3. The inner loop looks at the element at position `x`. This is the number at the top of the pile, and it may be smaller than any of the previous elements. The inner loop steps backwards through the sorted array; every time it finds a previous number that is larger, it swaps them. When the inner loop completes, the beginning of the array is sorted again, and the sorted portion has grown by one element.

> **Note:** The outer loop starts at index 1, not 0. Moving the very first element from the pile to the sorted portion doesn't actually change anything, so we might as well skip it. 
-->

程式是這樣運作的.

1. 複製陣列. 這是必須的, 因為我們不能直接修改帶入參數 `array` 的值. 就像 Swift 內建的 `sort()`, `insertionSort()` 函式將回傳 *複製* 原陣列後排序過的陣列.
2. 

## No more swaps

The above version of insertion sort works fine, but it can be made a tiny bit faster by removing the call to `swap()`. 

You've seen that we swap numbers to move the next element into its sorted position:

	[ 3, 5, 8, 4 | 6 ]
	        <-->
            swap
	        
	[ 3, 5, 4, 8 | 6 ]
         <-->
	     swap

Instead of swapping with each of the previous elements, we can just shift all those elements one position to the right, and then copy the new number into the right position.

	[ 3, 5, 8, 4 | 6 ]   remember 4
	           *
	
	[ 3, 5, 8, 8 | 6 ]   shift 8 to the right
	        --->
	        
	[ 3, 5, 5, 8 | 6 ]   shift 5 to the right
	     --->
	     
	[ 3, 4, 5, 8 | 6 ]   copy 4 into place
	     *

In code that looks like this:

```swift
func insertionSort(_ array: [Int]) -> [Int] {
  var a = array
  for x in 1..<a.count {
    var y = x
    let temp = a[y]
    while y > 0 && temp < a[y - 1] {
      a[y] = a[y - 1]                // 1
      y -= 1
    }
    a[y] = temp                      // 2
  }
  return a
}
```

The line at `//1` is what shifts up the previous elements by one position. At the end of the inner loop, `y` is the destination index for the new number in the sorted portion, and the line at `//2` copies this number into place.

## Making it generic

It would be nice to sort other things than just numbers. We can make the datatype of the array generic and use a user-supplied function (or closure) to perform the less-than comparison. This only requires two changes to the code.

The function signature becomes:

```swift
func insertionSort<T>(_ array: [T], _ isOrderedBefore: (T, T) -> Bool) -> [T] {
```

The array has type `[T]` where `T` is the placeholder type for the generics. Now `insertionSort()` will accept any kind of array, whether it contains numbers, strings, or something else.

The new parameter `isOrderedBefore: (T, T) -> Bool` is a function that takes two `T` objects and returns true if the first object comes before the second, and false if the second object should come before the first. This is exactly what Swift's built-in `sort()` function does.

The only other change is in the inner loop, which now becomes:

```swift
      while y > 0 && isOrderedBefore(temp, a[y - 1]) {
```

Instead of writing `temp < a[y - 1]`, we call the `isOrderedBefore()` function. It does the exact same thing, except we can now compare any kind of object, not just numbers.

To test this in a playground, do:

```swift
let numbers = [ 10, -1, 3, 9, 2, 27, 8, 5, 1, 3, 0, 26 ]
insertionSort(numbers, <)
insertionSort(numbers, >)
```

The `<` and `>` determine the sort order, low-to-high and high-to-low, respectively.

Of course, you can also sort other things such as strings,

```swift
let strings = [ "b", "a", "d", "c", "e" ]
insertionSort(strings, <)
```

or even more complex objects:

```swift
let objects = [ obj1, obj2, obj3, ... ]
insertionSort(objects) { $0.priority < $1.priority }
```

The closure tells `insertionSort()` to sort on the `priority` property of the objects.

Insertion sort is a *stable* sort. A sort is stable when elements that have identical sort keys remain in the same relative order after sorting. This is not important for simple values such as numbers or strings, but it is important when sorting more complex objects. In the example above, if two objects have the same `priority`, regardless of the values of their other properties, those two objects don't get swapped around.

## Performance

Insertion sort is really fast if the array is already sorted. That sounds obvious, but this is not true for all search algorithms. In practice, a lot of data will already be largely -- if not entirely -- sorted and insertion sort works quite well in that case.

The worst-case and average case performance of insertion sort is **O(n^2)**. That's because there are two nested loops in this function. Other sort algorithms, such as quicksort and merge sort, have **O(n log n)** performance, which is faster on large inputs.

Insertion sort is actually very fast for sorting small arrays. Some standard libraries have sort functions that switch from a quicksort to insertion sort when the partition size is 10 or less.

I did a quick test comparing our `insertionSort()` with Swift's built-in `sort()`. On arrays of about 100 items or so, the difference in speed is tiny. However, as your input becomes larger, **O(n^2)** quickly starts to perform a lot worse than **O(n log n)** and insertion sort just can't keep up.

## See also

[Insertion sort on Wikipedia](https://en.wikipedia.org/wiki/Insertion_sort)

*Written for Swift Algorithm Club by Matthijs Hollemans*
