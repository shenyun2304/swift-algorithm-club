# Segment Tree (區段樹)

<!--
I'm pleased to present to you Segment Tree. It's actually one of my favorite data structures because it's very flexible and simple in realization.

Let's suppose that you have an array **a** of some type and some associative function **f**. For example, the function can be sum, multiplication, min, max, [gcd](../GCD/), and so on.

Your task is to:

- answer a query for an interval given by **l** and **r**, i.e. perform `f(a[l], a[l+1], ..., a[r-1], a[r])`
- support replacing an item at some index `a[index] = newItem`

For example, if we have an array of numbers:
-->

很高興可以向你呈現區段樹. 我最愛的資料結構之一, 因為它非常彈性且簡單實現.

假設有個陣列 **a** 和相關的函式 **f**. 該函式可以是相加, 相乘, 最小值, 最大值, [最大公因數](../GCD/)... 等等.

你的任務是:

- 回答一個 **l** 到 **r** 區間的結果, 即. 呈現 `f(a[l], a[l+1], ..., a[r-1], a[r])`
- 覆寫某個索引的元素 `a[index] = newItem`

舉例來說, 如果我們有個數字陣列:

```swift
var a = [ 20, 3, -1, 101, 14, 29, 5, 61, 99 ]
```

<!--
We want to query this array on the interval from 3 to 7 for the function "sum". That means we do the following:
-->

我們想要對索引 3 到 7 的元素執行函式 "相加". 表示我們要做這樣的事情:

	101 + 14 + 29 + 5 + 61 = 210
	
<!--
because `101` is at index 3 in the array and `61` is at index 7. So we pass all the numbers between `101` and `61` to the sum function, which adds them all up. If we had used the "min" function, the result would have been `5` because that's the smallest number in the interval from 3 to 7.

Here's naive approach if our array's type is `Int` and **f** is just the sum of two integers:
-->

因為 `101` 在索引 3 的地方, 而 `61` 在索引 7 的地方. 所以我們將 `101` 到 `61` 中間所有的數字都傳給相加函式. 如果我們用了 "最小值" 函式, 那結果會是 `5`, 因為它是在索引 3 到 7 之間最小的數字.

這是個原始的方法來做到如果是 `Int` 陣列, 而 **f** 是兩個整數相加的函式:

```swift
func query(array: [Int], l: Int, r: Int) -> Int {
  var sum = 0
  for i in l...r {
    sum += array[i]
  }
  return sum
}
```

<!--
The running time of this algorithm is **O(n)** in the worst case, that is when **l = 0, r = n-1** (where **n** is the number of elements in the array). And if we have **m** queries to answer we get **O(m*n)** complexity.

If we have an array with 100,000 items (**n = 10^5**) and we have to do 100 queries (**m = 100**), then our algorithm will do **10^7** units of work. Ouch, that doesn't sound very good. Let's look at how we can improve it.

Segment trees allow us to answer queries and replace items with **O(log n)** time. Isn't it magic? :sparkles:

The main idea of segment trees is simple: we precalculate some segments in our array and then we can use those without repeating calculations.
-->

這個演算法最糟的時間是 **O(n)**, 就是當 **l = 0, r = n - 1** 的時候 (**n** 是陣列中元素個數). 如果我們有 **m** 個範圍需要計算, 那就是 **O(m*n)** 時間.

假設有個內含 100,000 元素的陣列 (**n = 10^5**) 而我們要對 100 範圍做計算 (**m = 100**), 那我們的演算法需要做 **10^7** 次工作. 毆, 這聽起來不妙啊. 來看看可以怎樣改善它.

區段樹可以做到計算範圍函式和覆寫元素佔用 **O(log n)** 時間. 神奇吧? :sparkles:

區段樹的概念很簡單: 我們預先計算陣列中某些區段, 然後我們可以使用那些結果而不用再重新計算.

<!--
## Structure of segment tree

A segment tree is just a [binary tree](../Binary Tree/) where each node is an instance of the `SegmentTree` class:

-->

## 區段樹結構

區段樹是個 [二元樹](../Binary Tree/) 而每個節點是 `SegmentTree` 的物件:

```swift
public class SegmentTree<T> {
  private var value: T
  private var function: (T, T) -> T
  private var leftBound: Int
  private var rightBound: Int
  private var leftChild: SegmentTree<T>?
  private var rightChild: SegmentTree<T>?
}
```
<!--
Each node has the following data:

- `leftBound` and `rightBound` describe an interval
- `leftChild` and `rightChild` are pointers to child nodes
- `value` is the result of applying the function `f(a[leftBound], a[leftBound+1], ..., a[rightBound-1], a[rightBound])`

If our array is `[1, 2, 3, 4]` and the function `f = a + b`, the segment tree looks like this:
-->

每個節點有下列資料:

- `leftBound` 和 `rightBound` 來描述區間
- `leftChild` 和 `rightChild` 是子節點的參考指標
- `value` 是指執行函式 `f(a[leftBound], a[leftBound+1], ..., a[rightBound-1], a[rightBound])` 的結果

如果我們陣列是 `[1, 2, 3, 4]` 而函式是 `f = a + b`, 那區段樹會像這樣:

![structure](Images/Structure.png)

<!--
The `leftBound` and `rightBound` of each node are marked in red.
-->

每個節點的 `leftBound` 和 `rightBound` 用紅色標示.

<!--
## Building a segment tree

Here's how we create a node of the segment tree:
-->

## 建立一個區段樹

這裡是我們創建一個區段樹:

```swift
public init(array: [T], leftBound: Int, rightBound: Int, function: @escaping (T, T) -> T) {
    self.leftBound = leftBound
    self.rightBound = rightBound
    self.function = function

    if leftBound == rightBound {                    // 1
      value = array[leftBound]
    } else {
      let middle = (leftBound + rightBound) / 2     // 2

      // 3
      leftChild = SegmentTree<T>(array: array, leftBound: leftBound, rightBound: middle, function: function)
      rightChild = SegmentTree<T>(array: array, leftBound: middle+1, rightBound: rightBound, function: function)

      value = function(leftChild!.value, rightChild!.value)  // 4
    }
  }
```

<!--
Notice that this is a recursive method. You give it an array such as `[1, 2, 3, 4]` and it builds up the entire tree, from the root node to all the child nodes.

1. The recursion terminates if `leftBound` and `rightBound` are equal. Such a `SegmentTree` instance represents a leaf node. For the input array `[1, 2, 3, 4]`, this process will create four such leaf nodes: `1`, `2`, `3`, and `4`. We just fill in the `value` property with the number from the array.

2. However, if `rightBound` is still greater than `leftBound`, we create two child nodes. We divide the current segment into two equal segments (at least, if the length is even; if it's odd, one segment will be slightly larger).

3. Recursively build child nodes for those two segments. The left child node covers the interval **[leftBound, middle]** and the right child node covers **[middle+1, rightBound]**.

4. After having constructed our child nodes, we can calculate our own value because **f(leftBound, rightBound) = f(f(leftBound, middle), f(middle+1, rightBound))**. It's math!

Building the tree is an **O(n)** operation.
-->

注意這是一個遞迴的方法. 你給定一個陣列像是 `[1, 2, 3, 4]`, 此方法會從根節點到所有葉節點建立整個樹.

1. 遞迴會在 `leftBound` 和 `rightBound` 相同時結束. `SegmentTree` 物件代表的是葉節點. 對於 `[1, 2, 3, 4]` 這個創建過程會建立 4 個葉節點: `1`, `2`, `3` 和 `4`. 我們把這些數字填入 `value` 變數中.
2. 無論如何, 如果 `rightBound` 還是比 `leftBound` 大, 我們創建兩個子節點. 我們將當前的區段分割成兩個大小差不多的區段 (如果元素個數是奇數, 那有一個區段會比較大一點)
3. 針對該兩個區段遞迴的創建子節點. 左子樹包含 **[leftBound, middle]** 範圍, 而右子樹包含 **[middle+1, rightBound]** 範圍.
4. 結束架構整個樹以後, 就可以計算自己本身的值, 因為 **f(leftBound, rightBound) = f(f(leftBound, middle), f(middle+1, rightBound))**. 簡單的數學!

創建整個樹是 **O(n)** 的操作.

<!--
## Getting answer to query

We go through all this trouble so we can efficiently query the tree.

Here's the code:
-->

## 計算結果

我們走訪所有的問題讓我們可以有效率的對樹作操作.

程式碼:

```swift
  public func query(withLeftBound: leftBound: Int, rightBound: Int) -> T {
    // 1
    if self.leftBound == leftBound && self.rightBound == rightBound {
      return self.value
    }
    
    guard let leftChild = leftChild else { fatalError("leftChild should not be nil") }
    guard let rightChild = rightChild else { fatalError("rightChild should not be nil") }
    
    // 2
    if leftChild.rightBound < leftBound {
      return rightChild.query(withLeftBound: leftBound, rightBound: rightBound)
      
    // 3
    } else if rightChild.leftBound > rightBound {
      return leftChild.query(withLeftBound: leftBound, rightBound: rightBound)
      
    // 4
    } else {
      let leftResult = leftChild.query(withLeftBound: leftBound, rightBound: leftChild.rightBound)
      let rightResult = rightChild.query(withLeftBound: rightChild.leftBound, rightBound: rightBound)
      return function(leftResult, rightResult)
    }
  }
```

<!--
Again, this is a recursive method. It checks four different possibilities.

1) First, we check if the query segment is equal to the segment for which our current node is responsible. If it is we just return this node's value.
-->

再一次, 這是個遞迴的方法. 它檢查四種不同的可能.

1) 首先, 我們檢查查詢的區塊是否和當前節點相符.
如果是了話那直接回傳此節點值.

![equalSegments](Images/EqualSegments.png)

<!--
2) Does the query segment fully lie within the right child? If so, recursively perform the query on the right child.
-->

2) 查詢的範圍是否全部都在右子樹中? 如果是, 那就對右子樹做遞迴並回傳.

![rightSegment](Images/RightSegment.png)

<!--
3) Does the query segment fully lie within the left child? If so, recursively perform the query on the left child.
-->

3) 查詢的範圍是否全部都在左子樹中? 如果是, 那就對左子樹做遞迴並回傳.

![leftSegment](Images/LeftSegment.png)

<!--
4) If none of the above, it means our query partially lies in both children so we combine the results of queries on both children.
-->

4) 如果以上皆非, 那表示查詢的範圍同時牽涉到左右子樹, 所以查詢兩個子樹結果後套用函式並回傳.

![mixedSegment](Images/MixedSegment.png)

<!--
This is how you can test it out in a playground:
-->

在 playground 中的測試:

```swift
let array = [1, 2, 3, 4]

let sumSegmentTree = SegmentTree(array: array, function: +)

sumSegmentTree.query(withLeftBound: 0, rightBound: 3)  // 1 + 2 + 3 + 4 = 10
sumSegmentTree.query(withLeftBound: 1, rightBound: 2)  // 2 + 3 = 5
sumSegmentTree.query(withLeftBound: 0, rightBound: 0)  // just 1
sumSegmentTree.query(withLeftBound: 3, rightBound: 3)  // just 4
```

<!--
Querying the tree takes **O(log n)** time.
-->

操作時間佔用 **O(log n)**.

<!--
## Replacing items

The value of a node in the segment tree depends on the nodes below it. So if we want to change a value of a leaf node, we need to update all its parent nodes too.

Here is the code:
-->

## 覆寫元素

節點值是依該節點的子樹值計算而來. 所以假如要更新某個葉節點的值, 那我們需要更新該節點以上所有父節點的值.

程式碼:


```swift
  public func replaceItem(at index: Int, withItem item: T) {
    if leftBound == rightBound {
      value = item
    } else if let leftChild = leftChild, rightChild = rightChild {
      if leftChild.rightBound >= index {
        leftChild.replaceItem(at: index, withItem: item)
      } else {
        rightChild.replaceItem(at: index, withItem: item)
      }
      value = function(leftChild.value, rightChild.value)
    }
  }
```

<!--
As usual, this works with recursion. If the node is a leaf, we just change its value. If the node is not a leaf, then we recursively call `replaceItem(at: )` to update its children. After that, we recalculate the node's own value so that it is up-to-date again.

Replacing an item takes **O(log n)** time.

See the playground for more examples of how to use the segment tree.
-->

通常, 用遞迴可以完成. 如果該節點是葉節點, 我們改變它的值就好. 如果節點不是葉節點, 那就遞迴的呼叫 `replaceItem(at: )` 來更新它的子節點, 最後會重新計算該節點的值.

覆寫元素使用 **O(log n)** 時間.

更多詳細請見 playground.

<!--
## See also
-->


## 相關閱讀

[Segment tree at PEGWiki](http://wcipeg.com/wiki/Segment_tree)

*Written for Swift Algorithm Club by [Artur Antonov](https://github.com/goingreen)*
