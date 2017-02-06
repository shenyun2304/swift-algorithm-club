# Selection Sampling (選擇抽樣)

<!--
Goal: Select *k* items at random from a collection of *n* items.

Let's say you have a deck of 52 playing cards and you need to draw 10 cards at random. This algorithm lets you do that.

Here's a very fast version:
-->

目標: 在 *n* 個元素的集合中選取 *k* 個元素.

假設有一副 52 張的樸克牌, 而你要從中隨機選擇 10 張. 這個演算法可以讓你達到這件事.

這是個非常快的版本:

```swift
func select<T>(from a: [T], count k: Int) -> [T] {
  var a = a
  for i in 0..<k {
    let r = random(min: i, max: a.count - 1)
    if i != r {
      swap(&a[i], &a[r])
    }
  }
  return Array(a[0..<k])
}
```

<!--
As often happens with these [kinds of algorithms](../Shuffle/), it divides the array into two regions. The first region contains the selected items; the second region is all the remaining items.

Here's an example. Let's say the array is:
-->

就像 [類似的演算法](../Shuffle/) 一樣, 它將陣列分割成兩個區塊. 第一個區塊包含了被選中的元素; 第二個區塊就是其他的元素.

來個例子. 假設陣列是這樣:


	[ "a", "b", "c", "d", "e", "f", "g" ]
	
<!--
We want to select 3 items, so `k = 3`. In the loop, `i` is initially 0, so it points at `"a"`.
-->

我們希望選取 3 個元素, 所以 `k = 3`. 在迴圈中, `i` 初始是 0, 所以他指向 `"a"`.

	[ "a", "b", "c", "d", "e", "f", "g" ]
	   i

<!--
We calculate a random number between `i` and `a.count`, the size of the array. Let's say this is 4. Now we swap `"a"` with `"e"`, the element at index 4, and move `i` forward:
-->

我們取 `i` 到 `a.count` 之間一個隨機數字. 假設是 4. 所以將 `"a"` 和在索引 4 的元素 `"e"` 交換位置, 然後遞增 `i`:


	[ "e" | "b", "c", "d", "a", "f", "g" ]
	         i

<!--
The `|` bar shows the split between the two regions. `"e"` is the first element we've selected. Everything to the right of the bar we still need to look at.

Again, we ask for a random number between `i` and `a.count`, but because `i` has shifted, the random number can never be less than 1. So we'll never again swap `"e"` with anything.

Let's say the random number is 6 and we swap `"b"` with `"g"`:
-->

`|` 分隔出左右兩個區塊. `"e"` 是第一個我們選取的元素. 在 `|` 右邊的元素都是我們尚未決定是否選取的目標.

再一次, 我們從 `i` 到 `a.count` 之間選一個隨機數字, 但這時候 `i` 已經遞增了, 所以隨機的數字不可能比 1 還小. 讓我們不會去把 `"e"` 換掉.

假設這次隨機數字是 6, 所以我們將 `"b"` 和 `"g"` 交換:


	[ "e" , "g" | "c", "d", "a", "f", "b" ]
	               i

<!--
One more random number to pick, let's say it is 4 again. We swap `"c"` with `"a"` to get the final selection on the left:
-->

尚還需要一個隨機數字, 讓我們假設這次取到 4. 所以我們將 `"c"` 和 `"a"` 交換.


	[ "e", "g", "a" | "d", "c", "f", "b" ]

<!--
And that's it. Easy peasy. The performance of this function is **O(k)** because as soon as we've selected *k* elements, we're done.

Here is an alternative algorithm, called "reservoir sampling":
-->

這樣就結束了. 簡單. 這函式的時間複雜度是 **O(k)**, 因為當我們選完 *k* 個元素後, 就結束了.


這是另一個演算法, 稱為 "水塘抽樣":


```swift
func reservoirSample<T>(from a: [T], count k: Int) -> [T] {
  precondition(a.count >= k)

  var result = [T]()      // 1
  for i in 0..<k {
    result.append(a[i])
  }

  for i in k..<a.count {  // 2
    let j = random(min: 0, max: i)
    if j < k {
      result[j] = a[i]
    }
  }
  return result
}
```

<!--
This works in two steps:

1. Fill the `result` array with the first `k` elements from the original array. This is called the "reservoir".
2. Randomly replace elements in the reservoir with elements from the remaining pool.

The performance of this algorithm is **O(n)**, so it's a little bit slower than the first algorithm. However, its big advantage is that it can be used for arrays that are too large to fit in memory, even if you don't know what the size of the array is (in Swift this might be something like a lazy generator that reads the elements from a file).

There is one downside to the previous two algorithms: they do not keep the elements in the original order. In the input array `"a"` came before `"e"` but now it's the other way around. If that is an issue for your app, you can't use this particular method.

Here is an alternative approach that does keep the original order intact, but is a little more involved:
-->

這流程有兩個步驟:

1. 把 `result` 陣列填滿原陣列的前 `k` 個元素. 這個叫做 "水塘"
2. 隨機從剩下來的元素中選取元素來替代掉水塘裡的元素

此演算法的複雜度是 **O(n)**, 所以比第一種演算法稍微慢了一點.
無論如何, 有個大優點在於當原始陣列大於記憶體可容納的空間時, 就算你不知道整個陣列的樣貌, 這個方法可以正常運作. (在 Swift 中這跟 lazy 產生器有點像)

這兩個演算法都有個缺點, 他們無法保持選出來的元素在原陣列裡的順序. 在原始陣列中 `"a"` 在 `"e"` 之前, 不過現在已經該變了. 如果這點對你的應用很重要, 那你可能無法使用這個方法.

這裡是另一個方法且可以保持原來的順序, 不過複雜一點點:

```swift
func select<T>(from a: [T], count requested: Int) -> [T] {
  var examined = 0
  var selected = 0
  var b = [T]()
  
  while selected < requested {                          // 1
    examined += 1
    
    let r = Double(arc4random()) / 0x100000000          // 2
    
    let leftToExamine = a.count - examined + 1          // 3
    let leftToAdd = requested - selected

    if Double(leftToExamine) * r < Double(leftToAdd) {  // 4
      selected += 1
      b.append(a[examined - 1])
    }
  }
  return b
}
```

<!--
This algorithm uses probability to decide whether to include a number in the selection or not. 

1. The loop steps through the array from beginning to end. It keeps going until we've selected *k* items from our set of *n*. Here, *k* is called `requested` and *n* is `a.count`.

2. Calculate a random number between 0 and 1. We want `0.0 <= r < 1.0`. The higher bound is exclusive; we never want it to be exactly 1. That's why we divide the result from `arc4random()` by `0x100000000` instead of the more usual `0xffffffff`.

3. `leftToExamine` is how many items we still haven't looked at. `leftToAdd` is how many items we still need to select before we're done.

4. This is where the magic happens. Basically, we're flipping a coin. If it was heads, we add the current array element to the selection; if it was tails, we skip it.

Interestingly enough, even though we use probability, this approach always guarantees that we end up with exactly *k* items in the output array.

Let's walk through the same example again. The input array is:
-->

這個演算法使用機率來決定是否要選取數字.

1. 迴圈從陣列的起頭走訪到尾端. 持續走訪直到我們從 *n* 個元素的集合中選取了 *k* 個元素. 在這裡 *k* 稱為 `requested` 而 *n* 稱為 `a.count`.
2. 在 0 到 1 之間選擇一個隨機數字. 我們要 `0.0 <= r < 1.0`. 不含上限; 我們不要有取到 1 的狀況. 這就是為什麼我們把 `arc4random()` 除以 `0x100000000` 而不是 `0xffffffff` 的原因.
3. `leftToExamine` 是我們還有多少個元素還沒走訪過. `leftToAdd` 是我們還需要選擇多少個元素.
4. 這裡就是神奇的地方了. 基本上, 我們在丟硬幣. 如果是頭像, 我們就選取當前的元素; 如果是字面, 我們就跳過.

讓我們跑一次流程看看. 假設原始陣列是:

	[ "a", "b", "c", "d", "e", "f", "g" ]

<!--
The loop looks at each element in turn, so we start at `"a"`. We get a random number between 0 and 1, let's say it is 0.841. The formula at `// 4` multiplies the number of items left to examine with this random number. There are still 7 elements left to examine, so the result is: 
-->

從頭走訪每個元素, 所以我們從 `"a"` 開始. 我們得到一個 0 到 1 的隨機數字, 假設是 0.841. 在 `//4` 中的公式中將還沒走訪的元素個數和此數相乘. 因為還有 7 個尚未走訪的元素, 所以結果是:

	7 * 0.841 = 5.887

<!--
We compare this to 3 because we wanted to select 3 items. Since 5.887 is greater than 3, we skip `"a"` and move on to `"b"`.

Again, we get a random number, let's say 0.212. Now there are only 6 elements left to examine, so the formula gives:
-->

我們把這個數和 3 比較 (因為我們要選 3 個). 因為 5.887 比 3 大, 所以我們跳過 `"a"` 然後走訪下一個, `"b"`.

再一次, 我們獲得一個隨機數字, 假設是 0.212. 現在有 6 個元素尚未走訪過, 所以依照公式:


	6 * 0.212 = 1.272

<!--
This *is* less than 3 and we add `"b"` to the selection. This is the first item we've selected, so two left to go.

On to the next element, `"c"`. The random number is 0.264, giving the result:
-->

這個數字 *的確* 比 3 小, 所以我們選取 `"b"`. 這是第一次選取元素, 還有兩個元素要選.

走訪下一個元素, `"c"`. 隨機數字是 0.264, 公式結果: 

	5 * 0.264 = 1.32

<!--
There are only 2 elements left to select, so this number must be less than 2. It is, and we also add `"c"` to the selection. The total selection is `[ "b", "c" ]`.

Only one item left to select but there are still 4 candidates to look at. Suppose the next random number is 0.718. The formula now gives:
-->

剩下 2 個元素要選, 所以這個數值必須要小於 2 才會選, 而它的確小於 2, 所以我們選取 `"c"`. 現在選取列表是 `[ "b", "c" ]`.

只剩下 1 個元素要選, 但是還有 4 個尚未走訪的元素. 假設隨機數字是 0.718. 公式:

	4 * 0.718 = 2.872

<!--
For this element to be selected the number has to be less than 1, as there is only 1 element left to be picked. It isn't, so we skip `"d"`. Only three possibilities left -- will we make it before we run out of elements?

The random number is 0.346. The formula gives:
-->

這時候此元素要能被選取公式值必須要小於 1 了, 因為只剩下 1 元素要選. 所以我們跳過 `"d"`. 剩下 3 個尚未走訪的元素 -- 我們可以在走訪完所有元素之前完成任務嗎?

這次隨機數字是 0.246. 公式結果:

	3 * 0.346 = 1.038
	
<!--
Just a tiny bit too high. We skip `"e"`. Only two candidates left...

Note that now literally we're dealing with a toin coss: if the random number is less than 0.5 we select `"f"` and we're done. If it's greater than 0.5, we go on to the final element. Let's say we get 0.583:
-->

數值高了一點. 跳過 `"e"`. 剩下兩個元素...

注意我們正在處理一個硬幣的投擲: 如果隨機數字比 0.5 還小, 那我們選了 `"f"` 後就結束了. 但如果大於 0.5, 我們就會走訪到最後的元素. 假設我們拿到的隨機數字是 0.583:

	2 * 0.583 = 1.166

<!--
We skip `"f"` and look at the very last element. Whatever random number we get here, it should always select `"g"` or we won't have selected enough elements and the algorithm doesn't work!

Let's say our final random number is 0.999 (remember, it can never be 1.0 or higher). Actually, no matter what we choose here, the formula will always give a value less than 1:
-->

我們跳過 `"f"` 然後走訪最後一個元素. 這時候無論隨機數字是多少, 我們只能選取 `"g"`, 不然我們就沒有選夠我們要的數量, 那此演算法就失敗了!

假設我們最後的隨機數字是 0.999 (記住, 不可能比 1.0 還高). 事實上, 無論我們拿到多少, 公式結果一定會小於 1:

	1 * 0.999 = 0.999

<!--
And so the last element will always be chosen if we didn't have a big enough selection yet. The final selection is `[ "b", "c", "g" ]`. Notice that the elements are still in their original order, because we examined the array from left to right.

Maybe you're not convinced yet... What if we always got 0.999 as the random value (the maximum possible), would that still select 3 items? Well, let's do the math:
-->

所以如果我們沒有選到足夠的元素, 最後一個元素是一定會被選取的. 最後的選取列表是 `[ "b", "c", "g"]`. 注意到元素之間的順序和原本一樣, 因為我們是從左到右來走訪陣列的.



	7 * 0.999 = 6.993     is this less than 3? no
	6 * 0.999 = 5.994     is this less than 3? no
	5 * 0.999 = 4.995     is this less than 3? no
	4 * 0.999 = 3.996     is this less than 3? no
	3 * 0.999 = 2.997     is this less than 3? YES
	2 * 0.999 = 1.998     is this less than 2? YES
	1 * 0.999 = 0.999     is this less than 1? YES

<!--
It always works! But does this mean that elements closer to the end of the array have a higher probability of being chosen than those in the beginning? Nope, all elements are equally likely to be selected. (Don't take my word for it: see the playground for a quick test that shows this in practice.)

Here's an example of how to test this algorithm:
-->

此方法一定會成功! 但是這表示越接近尾端的元素會比前端的元素還容易選取嗎? 不, 所有元素被選取的機率是相同的. (別只是看看這邊寫的; 到 playground 裡快速的測試一下吧.)


```swift
let input = [
  "there", "once", "was", "a", "man", "from", "nantucket",
  "who", "kept", "all", "of", "his", "cash", "in", "a", "bucket",
  "his", "daughter", "named", "nan",
  "ran", "off", "with", "a", "man",
  "and", "as", "for", "the", "bucket", "nan", "took", "it",
]

let output = select(from: input, count: 10)
print(output)
print(output.count)
```

<!--
The performance of this second algorithm is **O(n)** as it may require a pass through the entire input array.

> **Note:** If `k > n/2`, then it's more efficient to do it the other way around and choose `k` items to remove.

Based on code from Algorithm Alley, Dr. Dobb's Magazine, October 1993.
-->


這個第二個演算法的時間複雜度是 **O(n)**, 因為它有可能會走訪整個陣列.

> **注意:** 如果 `k > n/2`, 那反過來選取要移除的元素會更有效率.

程式碼基礎源於 1993 年 Dr. Dobb's 雜誌 Algorithm Alley 專欄.

*Written for Swift Algorithm Club by Matthijs Hollemans*
