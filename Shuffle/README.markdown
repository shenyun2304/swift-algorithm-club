# Shuffle (洗牌)

<!--
Goal: Rearrange the contents of an array.

Imagine you're making a card game and you need to shuffle a deck of cards. You can represent the deck by an array of `Card` objects and shuffling the deck means to change the order of those objects in the array. (It's like the opposite of sorting.)

Here is a naive way to approach this in Swift:
-->

目標: 將陣列的元素重新排列.

想像你在製作一個紙牌遊戲而你需要洗牌. 你可以用一個內含 `Card` 物件的陣列 來表示所有的紙牌, 而洗牌表示要打亂陣列中元素的排列順序. (有點像排序的相反.)

在 Swift 中使用原生的寫法:

```swift
extension Array {
  public mutating func shuffle() {
    var temp = [Element]()
    while !isEmpty {
      let i = random(count)
      let obj = remove(at: i)
      temp.append(obj)
    }
    self = temp
  }
}
```

<!--
To try it out, copy the code into a playground and then do:
-->


```swift
var list = [ "a", "b", "c", "d", "e", "f", "g" ]
list.shuffle()
list.shuffle()
list.shuffle()
```

<!--
You should see three different arrangements -- or [permutations](../Combinatorics/) to use math-speak -- of the objects in the array.

This shuffle works *in place*, it modifies the contents of the original array. The algorithm works by creating a new array, `temp`, that is initially empty. Then we randomly choose an element from the original array and append it to `temp`, until the original array is empty. Finally, the temporary array is copied back into the original one.

> **Note:** `random()` is a helper function that returns a random integer between 0 and the given maximum.

This code works just fine but it's not very efficient. Removing an element from an array is an **O(n)** operation and we perform this **n** times, making the total algorithm **O(n^2)**. We can do better!
-->

你可以看到三種不同 -- 或使用數學方法來 [排列](../Combinatorics/) -- 的元素排列.

這個洗牌程式是 *到位* 的, 它會改變原始的陣列. 演算法執行時會創建新陣列 `temp`, 初始時是空的. 然後隨機從原始陣列中選取一個元素塞到 `temp` 中, 直到原始陣列清空. 最後這個暫存的陣列會覆寫原始陣列.

> **注意:** `random()` 是一個幫助函式回傳 0 到給定數字之間的隨機數字.

這個程式可以運作但是效率不好. 從陣列中移除元素是 **O(n)** 的操作, 而我們這樣做了 **n** 次, 使得整個演算法用了 **O(n^2)** 時間. 我們可以做到更好!

<!--
## The Fisher-Yates / Knuth shuffle

Here is a much improved version of the shuffle algorithm:
-->

## Fisher-Yates / Knuth 洗牌

這是個更進步的洗牌演算法:

```swift
extension Array {
  public mutating func shuffle() {
    for i in stride(from: count - 1, through: 1, by: -1) {
      let j = random(i + 1)
      if i != j {
        swap(&self[i], &self[j])
      }
    }
  }
}
```

<!--
Again, this picks objects at random. In the naive version we placed those objects into a new temporary array so we could keep track of which objects were already shuffled and which still remained to be done. In this improved algorithm, however, we'll move the shuffled objects to the end of the original array. 

> **Note**: When you write `random(x)`, the largest number it will return is `x - 1`. We want to have `j <= i`, not `j < i`, so the largest number from the random number generator needs to be `i`, not `i - 1`. That's why we do `random(i + 1)`. If we didn't add that 1 to compensate, it would make some shuffle orders more likely to occur than others.

Let's walk through the example. We have the array:
-->

同樣的, 這個方法也隨機選了一個物件. 在使用原生方法的版本中我們將這些選出來的物件放入一個新的暫存陣列中, 所以我們可以追蹤哪些元素已經被重新排列了而哪些還沒. 而在這優化過的演算法中, 我們將要被洗牌的元素放到陣列的尾端.

> **注意:** 當你撰寫 `random(x)` 的時候, 會回傳的最大值是 `x - 1`. 而我們希望 `j <= i`, 不是 `j < i`, 隨機能到到的數值最大應該是 `i` 不是 `i - 1`. 這就是為什麼 `random(i + 1)`. 如果我們沒有加 1. 它會讓某些洗牌排列的出現比例比其他種高.

來討論個例子. 假設陣列是這樣:

	[ "a", "b", "c", "d", "e", "f", "g" ]

<!--
The loop starts at the end of the array and works its way back to the beginning. The very first random number can be any element from the entire array. Let's say it returns 2, the index of `"c"`. We swap `"c"` with `"g"` to move it to the end:
-->

迴圈從陣列結尾開始, 然後迭代到陣列起頭. 第一個隨機數字可能是陣列中的任何一個元素. 假設回傳 2, 是 `"c"` 的索引. 我們將 `"c"` 和 `"g"` 交換位置:

	[ "a", "b", "g", "d", "e", "f" | "c" ]
	             *                    *

<!--
The array now consists of two regions, indicated by the `|` bar. Everything to the right of the bar is shuffled already. 

The next random number is chosen from the range 0...6, so only from the region `[ "a", "b", "g", "d", "e", "f" ]`. It will never choose `"c"` since that object is done and we'll no longer touch it.

Let's say the random number generator picks 0, the index of `"a"`. Then we swap `"a"` with `"f"`, which is the last element in the unshuffled portion, and the array looks like this:
-->

陣列現在由 `|` 分隔成兩個區塊. 所有在 `|` 右邊的元素是已經洗牌過了.

下一個隨機數字的繁為是 0...6, 所以只會在 `[ "a", "b", "g", "d", "e", "f" ]` 範圍內. 永遠不會選到 `"c"`, 因為它已經是洗牌過了我們將不會去碰到它.

假設隨機數字選到 0, `"a"` 的索引. 我們將 `"a"` 和 `"f"` 交換位置, 陣列現在是這樣:


	[ "f", "b", "g", "d", "e" | "a", "c" ]
	   *                         *

<!--
The next random number is somewhere in `[ "f", "b", "g", "d", "e" ]`, so let's say it is 3. We swap `"d"` with `"e"`:
-->

現在隨機數字的選取範圍是 `[ "f", "b", "g", "d", "e" ]`, 假設這次取到 3. 我們將 `"d"` 和 `"e"` 交換位置:


	[ "f", "b", "g", "e" | "d", "a", "c" ]
	                  *     *

<!--
And so on... This continues until there is only one element remaining in the left portion. For example:
-->

一直持續到左邊部分只剩下一個元素. 像是:

	[ "b" | "e", "f", "g", "d", "a", "c" ]

<!--
There's nothing left to swap that `"b"` with, so we're done.

Because we only look at each array element once, this algorithm has a guaranteed running time of **O(n)**. It's as fast as you could hope to get!
-->

沒有可以跟 `"b"` 交換的位置了, 完成了.

因為陣列中的元素我們只走訪一次, 所以這樣的演算法保證時間複雜度是  **O(n)**.


<!--
## Creating a new array that is shuffled

There is a slight variation on this algorithm that is useful for when you want to create a new array instance that contains the values `0` to `n-1` in random order.

Here is the code:
-->

## 建立一個已經洗牌過的陣列

用這個演算法做個簡單的變化, 如果你想要創建一個內容已經是從 `0` 到 `n-1` 隨機排列的陣列, 可以這樣創建:

```swift
public func shuffledArray(_ n: Int) -> [Int] {
  var a = [Int](repeating: 0, count: n)
  for i in 0..<n {
    let j = random(i + 1)
    if i != j {
      a[i] = a[j]
    }
    a[j] = i
  }
  return a
}
```

<!--
To use it:
-->

這樣使用:

```swift
let numbers = shuffledArray(10)
```

<!--
This returns something like `[3, 0, 9, 1, 8, 5, 2, 6, 7, 4]`. As you can see, every number between 0 and 10 is in that list, but shuffled around. Of course, when you try it for yourself the order of the numbers will be different. 

The `shuffledArray()` function first creates a new array with `n` zeros. Then it loops `n` times and in each step adds the next number from the sequence to a random position in the array. The trick is to make sure that none of these numbers gets overwritten with the next one, so it moves the previous number out of the way first!

The algoritm is quite clever and I suggest you walk through an example yourself, either on paper or in the playground. (Hint: Again it splits the array into two regions.)

## See also

These Swift implementations are based on pseudocode from the [Wikipedia article](https://en.wikipedia.org/wiki/Fisher–Yates_shuffle).

Mike Bostock has a [great visualization](http://bost.ocks.org/mike/shuffle/) of the shuffle algorithm.
-->


這會回傳類似 `[3, 0, 9, 1, 8, 5, 2, 6, 7, 4]` 的陣列. 可以看到, 所有的數字都介於 0 到 10 之間, 而且是無序的. 當然如果你自己嘗試了話順序會不同.

`shuffledArray()` 函式先建立了含有 `n` 個 0 的陣列. 然後迭代 `n` 次, 每次迭代將迭代數覆寫到一個隨機的位置. 這樣的做法可以保證這些元素不會被

*Written for Swift Algorithm Club by Matthijs Hollemans*
