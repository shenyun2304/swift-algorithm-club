# Binary Search Tree (BST) 二元搜尋樹

<!--
A binary search tree is a special kind of [binary tree](../Binary Tree/) (a tree in which each node has at most two children) that performs insertions and deletions such that the tree is always sorted.

If you don't know what a tree is or what it is for, then [read this first](../Tree/).
-->

二元搜尋樹是[二元樹](../Binary Tree/) ( 每個節點限制最多只能有兩個子節點的樹 ) 的特例. 特別是在二元搜尋樹中的插入和移除都會保持樹總是有排序狀態.

如果你還不知道樹是什麼, 那麼先 [先讀這個](../Tree/).

<!--
## "Always sorted" property

Here is an example of a valid binary search tree:
-->

## "總是有序" 特性

這是一個二元搜尋樹的例子:

![A binary search tree](Images/Tree1.png)

<!--
Notice how each left child is smaller than its parent node, and each right child is greater than its parent node. This is the key feature of a binary search tree.

For example, `2` is smaller than `7` so it goes on the left; `5` is greater than `2` so it goes on the right.
-->

注意到每個左子節點一定小於它的父節點, 而每個右節點一定大於他的父節點. 這就是二元搜尋樹的大特點.

<!--
## Inserting new nodes

When performing an insertion, we first compare the new value to the root node. If the new value is smaller, we take the *left* branch; if greater, we take the *right* branch. We work our way down the tree this way until we find an empty spot where we can insert the new value.

Say we want to insert the new value `9`:

- We start at the root of the tree (the node with the value `7`) and compare it to the new value `9`.
- `9 > 7`, so we go down the right branch and repeat the same procedure but this time on node `10`.
- Because `9 < 10`, we go down the left branch.
- We've now arrived at a point where there are no more values to compare with. A new node for `9` is inserted at that location.

The tree now looks like this:
-->

## 插入一個新節點

當要插入一個新節點時, 我們先將新的節點和根節點比較. 如果新節點比較小, 我們往 *左* 子樹走訪; 如果新節點比較大, 我們往 *右* 子樹走訪. 我們持續走訪直到找到一個空位將我們的新節點插入. 

假設我們想要插入一個新節點 `9`:
- 我們先從根節點 (節點`7`) 來走訪, 並比較兩個節點.
- `9 > 7`, 所以我們往右子樹繼續走訪, 遇到節點 `10` 並重複此動作.
- 因為 `9 < 10`, 所以我們走訪節點 `10` 的左子樹.
- 當我們走訪到的地方沒有任何節點可以比較, 我們就將此節點 `9` 插入在此.

這棵樹現在像這樣:

![After adding 9](Images/Tree2.png)

<!--
There is always only one possible place where the new element can be inserted in the tree. Finding this place is usually pretty quick. It takes **O(h)** time, where **h** is the height of the tree.

> **Note:** The *height* of a node is the number of steps it takes to go from that node to its lowest leaf. The height of the entire tree is the distance from the root to the lowest leaf. Many of the operations on a binary search tree are expressed in terms of the tree's height.

By following this simple rule -- smaller values on the left, larger values on the right -- we keep the tree sorted in a way such that whenever we query it, we can quickly check if a value is in the tree.
-->

在插入節點時, 只會有一個地方可以插入. 找到個位置的時間複雜度是 **O(h)**, **h** 是樹的高度.

> **注意:** 節點的 *高度* 是指該節點到其子樹中最末端葉節點中間的層數. 一棵樹的高度是指根節點到整棵樹最末端節點中間的層數. 很多二元搜尋樹的操作都是用樹的高度來表示.

依循這簡單的規則 -- 小的值在左邊, 大的值在右邊 -- 我們將可以保持這棵樹排序的狀態, 並可在搜尋的時候快速查找.

<!--
## Searching the tree

To find a value in the tree, we essentially perform the same steps as with insertion:

- If the value is less than the current node, then take the left branch.
- If the value is greater than the current node, take the right branch.
- And if the value is equal to the current node, we've found it!

Like most tree operations, this is performed recursively until either we find what we're looking for, or run out of nodes to look at.

If we were looking for the value `5` in the example, it would go as follows:
-->


## 搜尋

想要在樹中找的一個值, 基本上的操作和插入是一樣的:

- 如果查找的值小於當前節點, 走訪左邊子樹.
- 如果查找的值大於當前節點, 走訪右邊子樹.
- 如果查找的值等於當前節點, 就找到它啦!

就像很多樹的操作一樣, 這個動作會一直遞迴直到我們找到該值, 或走訪完所有節點依然沒找到.

如果在此例中找尋 `5`, 動作會像這樣:

![Searching the tree](Images/Searching.png)

<!--
Thanks to the structure of the tree, searching is really fast. It runs in **O(h)** time. If you have a well-balanced tree with a million nodes, it only takes about 20 steps to find anything in this tree. (The idea is very similar to [binary search](../Binary Search) in an array.)
-->

多虧了這種結構, 搜尋非常的快速. 只使用了 **O(h)** 時間. 如果有個擁有百萬節點的樹, 而節點左右平衡的很好, 那大概只需要花 20 次走訪便能找到值. ( 這個概念非常像陣列中的 [二元搜尋](../Binary Search). )

<!--

## Traversing the tree

Sometimes you don't want to look at just a single node, but at all of them.

There are three ways to traverse a binary tree:

1. *In-order* (or *depth-first*): first look at the left child of a node, then at the node itself, and finally at its right child.
2. *Pre-order*: first look at a node, then its left and right children.
3. *Post-order*: first look at the left and right children and process the node itself last.

Once again, this happens recursively.

If you traverse a binary search tree in-order, it looks at all the nodes as if they were sorted from low to high. For the example tree, it would print `1, 2, 5, 7, 9, 10`:
-->

## 走訪

有時候你想要走訪整棵樹, 並不是某個節點.

有三種走訪策略:

1. *中序式* ( 又稱 *深度優先* ): 先走訪左節點, 再來是節點本身, 最後是右節點. ( 左 -> 中 -> 右 )
2. *前序式*: 先走訪節點本身, 再來是左節點, 最後是右節點. ( 中 -> 左 -> 右 )
3. *後序式*: 先走訪左節點, 再來是右節點, 最後是節點本身. ( 左 -> 右 -> 中 )

同樣的, 都可以使用遞迴操作.

如果使用中序式來走訪, 則結果將會有小到大排序. 依照下面的例子, 則會像這樣走訪: `1, 2, 5, 7, 9, 10`:

![Traversing the tree](Images/Traversing.png)

<!--
## Deleting nodes


Removing nodes is kinda tricky. It is easy to remove a leaf node, you just disconnect it from its parent:
-->

## 刪除節點


<!--
If the node to remove has only one child, we can link that child to the parent node. So we just pull the node out:
-->

如果要刪除的節點只有一個子節點, 則將該子節點取代要刪除的節點:

![Deleting a node with one child](Images/DeleteOneChild.png)

<!--
Removing nodes is also easy. After removing a node, we replace the node with either its biggest child on the left or its smallest child on the right. That way the tree is still sorted after the removal. In following example, 10 is removed and replaced with either 9 (Figure 2), or 11 (Figure 3).
-->

刪除其他節點也很簡單. 在移除該節點後, 我們用該節點左子樹中最大的節點或右子樹中最小的節點來取代它的位置. 這樣在移除節點後, 保留樹的有序性. 下面這個例子, 10 被移除了, 而 9 或 11 都可以取代它.

![Deleting a node with two children](Images/DeleteTwoChildren.png)

<!--
Note the replacement needs to happen when the node has at least one child. If it has no child, you just disconnect it from its parent:
-->

注意到取代動作只有在節點有子節點的情況才會發生. 如果是刪除葉節點, 那直接刪除就好:

![Deleting a leaf node](Images/DeleteLeaf.png)


<!--
## The code (solution 1)

So much for the theory. Let's see how we can implement a binary search tree in Swift. There are different approaches you can take. First, I'll show you how to make a class-based version but we'll also look at how to make one using enums.

Here's a first stab at a `BinarySearchTree` class:
-->

## 程式碼 (解法 1)


講了這麼多理論. 讓我們來看看在 Swift 中如何實現二元搜尋樹. 不到倒是有很多種方法. 首先, 我會展示如何使用一個 class-based 的方法來實作, 不過我們也會看一下如何使用 enums 來做.

這是實作的第一個類別, `BinarySearchTree`:

```swift
public class BinarySearchTree<T: Comparable> {
  private(set) public var value: T
  private(set) public var parent: BinarySearchTree?
  private(set) public var left: BinarySearchTree?
  private(set) public var right: BinarySearchTree?

  public init(value: T) {
    self.value = value
  }

  public var isRoot: Bool {
    return parent == nil
  }

  public var isLeaf: Bool {
    return left == nil && right == nil
  }

  public var isLeftChild: Bool {
    return parent?.left === self
  }

  public var isRightChild: Bool {
    return parent?.right === self
  }

  public var hasLeftChild: Bool {
    return left != nil
  }

  public var hasRightChild: Bool {
    return right != nil
  }

  public var hasAnyChild: Bool {
    return hasLeftChild || hasRightChild
  }

  public var hasBothChildren: Bool {
    return hasLeftChild && hasRightChild
  }

  public var count: Int {
    return (left?.count ?? 0) + 1 + (right?.count ?? 0)
  }
}
```

<!--


This class describes just a single node, not the entire tree. It's a generic type, so the node can store any kind of data. It also has references to its `left` and `right` child nodes and a `parent` node.


Here's how you'd use it:
-->

這個類別描述了一個單一節點結構, 並非整棵樹. 使用了通用型態, 所以節點可以適用於各種類型的資料. 類別含有它的 `左子節點`, `右子節點` 和 `父節點`

這是使用該類別來創造一個節點:

```swift
let tree = BinarySearchTree<Int>(value: 7)
```

<!--
The `count` property determines how many nodes are in the subtree described by this node. This doesn't just count the node's immediate children but also their children and their children's children, and so on. If this particular object is the root node, then it counts how many nodes are in the entire tree. Initially, `count = 0`.

> **Note:** Because `left`, `right`, and `parent` are optionals, we can make good use of Swift's optional chaining (`?`) and nil-coalescing operators (`??`). You could also write this sort of thing with `if let` but that is less concise.
-->

`count` 屬性顯示該節點的總子節點數. 這個屬性不只是計算該節點的第一層子節點, 是包含所有子樹的節點數. 根節點的 `count` 屬性就代表整棵樹的總子節點數 (不含根節點本身). 一開始時, `count = 0`.

> **注意:** 因為 `左子節點`, `右子節點` 和 `父節點` 是 Optional 屬性, 我們可以搭配 Swift 的 Optional 鏈 (`?`) 的特性 和 
nil-coalescing 運算子 (`??`). 你也可以使用 `if let` 的寫法, 不過就沒那麼簡潔了.

<!--
### Inserting nodes

A tree node by itself is pretty useless, so here is how you would add new nodes to the tree:
-->
### 插入節點

只有一個節點的樹是沒什麼意義的, 所以這邊示範如插入一個新的節點:

```swift
  public func insert(value: T) {
    if value < self.value {
      if let left = left {
        left.insert(value: value)
      } else {
        left = BinarySearchTree(value: value)
        left?.parent = self
      }
    } else {
      if let right = right {
        right.insert(value: value)
      } else {
        right = BinarySearchTree(value: value)
        right?.parent = self
      }
    }
  }
```
<!--
Like so many other tree operations, insertion is easiest to implement with recursion. We compare the new value to the values of the existing nodes and decide whether to add it to the left branch or the right branch.

If there is no more left or right child to look at, we create a `BinarySearchTree` object for the new node and connect it to the tree by setting its `parent` property.

> **Note:** Because the whole point of a binary search tree is to have smaller nodes on the left and larger ones on the right, you should always insert elements at the root, to make to sure this remains a valid binary tree!

To build the complete tree from the example you'd do:
-->

就像其他樹的操作, 插入利用遞迴實作是很簡單的. 我們拿新的值和樹中的節點做比對, 然後判斷是要往左還是往右繼續走訪.

如果走訪到沒有任何左或右子節點, 創建一個 `BinarySearchTree` 的物件, 並設定該物件的 `parent` 屬性來讓該物件和樹做連結.

> **注意:** 二元搜尋樹的重點在於越左邊的節點越小, 越右邊的越大, 你應該每次要插入節點時都對根節點插入, 來保證整棵樹的有序性.

要建立上述範例中的樹, 可以這樣做:

```swift
let tree = BinarySearchTree<Int>(value: 7)
tree.insert(2)
tree.insert(5)
tree.insert(10)
tree.insert(9)
tree.insert(1)
```
<!--
> **Note:** For reasons that will become clear later, you should insert the numbers in a somewhat random order. If you insert them in sorted order, the tree won't have the right shape.

For convenience, let's add an init method that calls `insert()` for all the elements in an array:
-->

> **注意:** 你應該要照上面的順序來插入節點, 不然結果會跟上述的例子不一樣, 詳細理由稍後再解釋.

方便起見, 讓我們增加一個 init 建構子, 會接收一個陣列當參數,並對陣列內元素呼叫 `insert()` 方法.

```swift
  public convenience init(array: [T]) {
    precondition(array.count > 0)
    self.init(value: array.first!)
    for v in array.dropFirst() {
      insert(v, parent: self)
    }
  }
```
<!--
Now you can simply do this:
-->

現在就可以這樣建立樹:

```swift
let tree = BinarySearchTree<Int>(array: [7, 2, 5, 10, 9, 1])
```
<!--
The first value in the array becomes the root of the tree.
-->
陣列的第一個元素會是根節點.

<!--
### Debug output

When working with somewhat complicated data structures such as this, it's useful to have human-readable debug output.
-->

### Debug 輸出

當在處理很複雜的結構時, 有一個可讀性高的 debug 輸出是非常好用的.

```swift
extension BinarySearchTree: CustomStringConvertible {
  public var description: String {
    var s = ""
    if let left = left {
      s += "(\(left.description)) <- "
    }
    s += "\(value)"
    if let right = right {
      s += " -> (\(right.description))"
    }
    return s
  }
}
```
<!--
When you do a `print(tree)`, you should get something like this:

	((1) <- 2 -> (5)) <- 7 -> ((9) <- 10)

The root node is in the middle. With some imagination, you should see that this indeed corresponds to the following tree:
-->

這樣當你執行 `print(tree)`, 你會得到:

	((1) <- 2 -> (5)) <- 7 -> ((9) <- 10)
	
根節點在正中間, 發揮一點想像力, 你可以發現這個表示的正是這個樣子的樹:

![The tree](Images/Tree2.png)

<!--
By the way, you may be wondering what happens when you insert duplicate items? We always insert those in the right branch. Try it out!

### Searching

What do we do now that we have some values in our tree? Search for them, of course! Being able to find items quickly is the entire purpose of a binary search tree. :-)

Here is the implementation of `search()`:
-->

對了, 你可能對插入一個重複的值會發生什麼事感到疑惑. 我們的程式一律將該節點插入到右邊. 試試看吧!

### 搜尋

當我們的樹中有一些元素後我們要做什麼? 當然是搜尋他們囉! 能夠快速的搜尋到他們就是二元搜尋樹的最大目的. :-)

這裡是 `search()` 的實作:

```swift
  public func search(value: T) -> BinarySearchTree? {
    if value < self.value {
      return left?.search(value)
    } else if value > self.value {
      return right?.search(value)
    } else {
      return self  // found it!
    }
  }
```

<!--
I hope the logic is clear: this starts at the current node (usually the root) and compares the values. If the search value is less than the node's value, we continue searching in the left branch; if the search value is greater, we dive into the right branch.

Of course, if there are no more nodes to look at -- when `left` or `right` is nil -- then we return `nil` to indicate the search value is not in the tree.

> **Note:** In Swift that's very conveniently done with optional chaining; when you write `left?.search(value)` it automatically returns nil if `left` is nil. There's no need to explicitly check for this with an `if` statement.

Searching is a recursive process but you can also implement it with a simple loop instead:
-->

我希望邏輯很清楚: 先從節點本身開始 ( 通常是根節點 ) 和該搜尋值比較. 如果該搜尋值比節點本身小, 則我們就往左子樹繼續搜尋; 反之, 則往右子樹搜尋.

當然, 如果已經沒有子節點可以搜尋 -- `左節點` 和 `右節點` 都是 `nil` --- 的時候, 我們回傳 `nil` 表示該搜尋值不在此樹中.

> **注意:** optional 鏈在 Swift 中非常方便; 當你撰寫 `left?.search(value)` 的時候, 如果 `left` 本身就是 `nil`, 會自動直接回傳 `nil`. 不需要特別在使用 `if` 去判斷.

搜尋是用遞迴來實作的, 不過也可以使用一個簡單的迴圈來做:

```swift
  public func search(value: T) -> BinarySearchTree? {
    var node: BinarySearchTree? = self
    while case let n? = node {
      if value < n.value {
        node = n.left
      } else if value > n.value {
        node = n.right
      } else {
        return node
      }
    }
    return nil
  }
```

<!--
Verify for yourself that you understand that these two implementations are equivalent. Personally, I prefer to use iterative code over recursive code but your opinion may differ. ;-)

Here's how to test searching:
-->

確定你瞭解這兩種實作方式是一樣的. 私人方面, 我偏好使用迭代, 但是你的想法可以不同. ;-)

這裡顯示如何測試搜尋:

```swift
tree.search(5)
tree.search(2)
tree.search(7)
tree.search(6)   // nil
```
<!--
The first three lines all return the corresponding `BinaryTreeNode` object. The last line returns `nil` because there is no node with value `6`.

> **Note:** If there are duplicate items in the tree, `search()` always returns the "highest" node. That makes sense, because we start searching from the root downwards.
-->

前三行都會回傳對應的 `BinaryTreeNode` 物件. 最後一行則回傳 `nil`, 因為樹中沒有 `6` 的節點.

> **注意:** 如果樹中有重複值的節點, `search()` 總是回傳最"高層"的節點. 原因是因為我們從根節點往下搜尋.

<!--
### Traversal
Remember there are 3 different ways to look at all nodes in the tree? Here they are:
-->


### 再論走訪

還記得有三種走訪方式嗎? 這裡是他們的實作:

```swift
  public func traverseInOrder(process: (T) -> Void) {
    left?.traverseInOrder(process: process)
    process(value)
    right?.traverseInOrder(process: process)
  }

  public func traversePreOrder(process: (T) -> Void) {
    process(value)
    left?.traversePreOrder(process: process)
    right?.traversePreOrder(process: process)
  }

  public func traversePostOrder(process: (T) -> Void) {
    left?.traversePostOrder(process: process)
    right?.traversePostOrder(process: process)
    process(value)
  }
```

<!--
They all do pretty much the same thing but in different orders. Notice once again that all the work is done recursively. Thanks to Swift's optional chaining, the calls to `traverseInOrder()` etc are ignored when there is no left or right child.

To print out all the values from the tree sorted from low to high you can write:
-->

這三個差異只是不同的走訪順序. 注意這邊都是以遞迴的方法來實現. 歸功於 Swift 的 Optional 鏈, 呼叫 `traverseInOrder()` 等方法時, 無須考慮該節點是否沒有子節點.

想要由小到大的把節點值打印出來, 可以這樣做:

```swift
tree.traverseInOrder { value in print(value) }
```
<!--
This prints the following:

	1
	2
	5
	7
	9
	10

You can also add things like `map()` and `filter()` to the tree. For example, here's an implementation of map:
-->

打印結果是這樣:

	1
	2
	5
	7
	9
	10
	
你也可以做 `map()` 或 `filter()` 的操作. 舉例來說, 以下是 map 的操作:


```swift

  public func map(formula: (T) -> T) -> [T] {
    var a = [T]()
    if let left = left { a += left.map(formula: formula) }
    a.append(formula(value))
    if let right = right { a += right.map(formula: formula) }
    return a
  }
```

<!--
This calls the `formula` closure on each node in the tree and collects the results in an array. `map()` works by traversing the tree in-order.

An extremely simple example of how to use `map()`:
-->

這裡對讓節點呼叫了名為 `formula` 的閉包, 然後將結果存到一個陣列中.

這裡顯示一個極為簡單的 `map()` 使用範例:

```swift
  public func toArray() -> [T] {
    return map { $0 }
  }
```
<!--
This turns the contents of the tree back into a sorted array. Try it out in the playground:
-->

這將把樹轉換成一個有序陣列. 在 playground 試試看吧:

```swift
tree.toArray()   // [1, 2, 5, 7, 9, 10]
```

<!--
As an exercise for yourself, see if you can implement filter and reduce.
-->

試試看, 能不能實作 filter 和 reduce.


<!--
### Deleting nodes

We can make the code much more readable by defining some helper functions.
-->

### 刪除節點

上面提過刪除節點需要點技巧. 我們加入一些函式來讓程式碼可讀性提高一些.

```swift
  private func reconnectParentToNode(node: BinarySearchTree?) {
    if let parent = parent {
      if isLeftChild {
        parent.left = node
      } else {
        parent.right = node
      }
    }
    node?.parent = parent
  }
```

<!--
Making changes to the tree involves changing a bunch of `parent` and `left` and `right` pointers. This function helps with that. It takes the parent of the current node -- that is `self` -- and connects it to another node. Usually that other node will be one of the children of `self`.


We also need a function that returns the minimum and maximum of a node:
-->

樹的變動牽連到一堆節點的 `父節點` 和 `左節點` 和 `右節點`. 這個函式就是在處理這些事情. 先獲得當前節點的父節點, 然後判斷當前節點是左節點還是右節點, 最後讓新的節點取代當前節點的位置. 通常新的節點是當前節點的某個子節點.

我們需要一些函式來獲得節點的最小和最大的子節點:

最小子節點:

```swift
  public func minimum() -> BinarySearchTree {
    var node = self
    while case let next? = node.left {
      node = next
    }
    return node
  }

```

<!--
To see how this works, take the following tree:
-->
要知道這如何運作, 以下面這棵樹為例:

![Example](Images/MinimumMaximum.png)

<!--
For example, if we look at node `10`, its leftmost descendent is `7`. We get there by following all the `left` pointers until there are no more left children to look at. The leftmost descendent of the root node `6` is `1`. Therefore, `1` is the minimum value in the entire tree

We won't need it for deleting, but for completeness' sake, here is the opposite of `minimum()`:

-->

舉例來說, 先針對節點 `10`, 它最左邊的葉節點是 `7`. 我們依照選擇左子節點的規則一直往下層走訪直到沒有任何節點. 根節點的最左邊葉節點是 `1`, 因此, 節點 `1` 是整棵樹值最小的節點. 

這是獲得最右邊葉節點的函式 ( 最大子節點 ):

```swift
  public func maximum() -> BinarySearchTree {
    var node = self
    while case let next? = node.right {
      node = next
    }
    return node
  }


```
<!--
It returns the rightmost descendent of the node. We find it by following `right` pointers until we get to the end. In the above example, the rightmost descendent of node `2` is `5`. The maximum value in the entire tree is `11`, because that is the rightmost descendent of the root node `6`.

Finally, we can write the code that removes a node from the tree:
-->

依照選擇右子節點的規則一直往下層走訪直到最後. 由此例子來看, 節點 `2` 的最右邊子節點是 `5`. 整棵樹的最大節點是 `11`, 因為該節點是根節點 `6` 最右邊的子節點.

最後, 我們可以得到刪除節點的程式碼:


```swift
  @discardableResult public func remove() -> BinarySearchTree? {
    let replacement: BinarySearchTree?

    // Replacement for current node can be either biggest one on the left or
    // smallest one on the right, whichever is not nil
    if let right = right {
      replacement = right.minimum()
    } else if let left = left {
      replacement = left.maximum()
    } else {
      replacement = nil
    }

    replacement?.remove()

    // Place the replacement on current node's position
    replacement?.right = right
    replacement?.left = left
    right?.parent = replacement
    left?.parent = replacement
    reconnectParentTo(node:replacement)

    // The current node is no longer part of the tree, so clean it up.
    parent = nil
    left = nil
    right = nil

    return replacement
  }
```

<!--
It doesn't look so scary after all. ;-) There are four situations to handle:

1. This node has two children. 
2. This node only has a left child. The left child replaces the node.
3. This node only has a right child. The right child replaces the node.
4. This node has no children. We just disconnect it from its parent.

First, we determine which node will replace the one we're removing and then we call `reconnectParentToNode()` to change the `left`, `right`, and `parent` pointers to make that happen. Since the current node is no longer part of the tree, we clean it up by setting its pointers to `nil`. Finally, we return the node that has replaced the removed one (or `nil` if this was a leaf node).

The only tricky situation here is `// 1` and that logic has its own helper method:
-->

看起來不怎麼可怕. ;-) 有四種情況要處理:

1. 這個節點有兩個子節點. 
2. 這個節點只有左子節點, 則由左子節點取代該節點.
3. 這個節點只有右子節點. 則由右子節點取代該節點.
4. 這個節點沒有子節點, 則直接移除該節點.

<!--
First, we determine which node will replace the one we're removing and then we call `reconnectParentToNode()` to change the `left`, `right`, and `parent` pointers to make that happen. Since the current node is no longer part of the tree, we clean it up by setting its pointers to `nil`. Finally, we return the node that has replaced the removed one (or `nil` if this was a leaf node).

The only tricky situation here is `// 1` and that logic has its own helper method:
-->

首先, 先決定要由哪個節點來取代我們要刪除的那個節點. 找到後呼叫 `reconnectParentToNode()` 去改變樹的狀態. 因為要刪除的節點已經不會再樹中了, 所以可以將該節點的所有連結節點都設為 `nil`. 最後, 回傳取代該節點的節點 ( 如果該節點是葉節點則回傳 `nil` ).

而處理第一種情況的函式如下所示:


```swift
  private func removeNodeWithTwoChildren(left: BinarySearchTree, _ right: BinarySearchTree) -> BinarySearchTree {
    let successor = right.minimum()
    successor.remove()
    
    successor.left = left
    left.parent = successor
    
    if right !== successor {
      successor.right = right
      right.parent = successor
    } else {
      successor.right = nil
    }
    
    return successor
  }
```

<!--
If the node to remove has two children, it must be replaced by the smallest child that is larger than this node's value. That always happens to be the leftmost descendent of the right child, i.e. `right.minimum()`. We take that node out of its original position in the tree and put it into the place of the node we're removing.

Try it out:
-->

如果要刪除的節點有兩個子節點, 那要拿該節點右子樹中最小的葉節點來取代該節點. 即 `right.minimun()`.
我們先將該葉節點移除樹中, 在將該節點取代我們要刪除的節點.

試試看:

```swift
if let node2 = tree.search(2) {
  print(tree)     // before
  node2.remove()
  print(tree)     // after
}
```
<!--
First you find the node that you want to remove with `search()` and then you call `remove()` on that object. Before the removal, the tree printed like this:
-->

先用 `search()` 找到你想要刪除的節點, 然後呼叫 `remove()`, 在刪除之前, 印出來的樹長這樣:

	((1) <- 2 -> (5)) <- 6 -> ((9) <- 10)

<!--
But after `remove()` you get:
-->

刪除後, 樹變成這樣:

	((1) <- 5) <- 6 -> ((9) <- 10)

<!--
As you can see, node `5` has taken the place of `2`.


> **Note:** What would happen if you deleted the root node? In that case, `remove()` tells you which node has become the new root. Try it out: call `tree.remove()` and see what happens.

Like most binary search tree operations, removing a node runs in **O(h)** time, where **h** is the height of the tree.
-->

可以看到, 節點 `5` 已經取代節點 `2` 的位置.

> **注意:** 如果移除根節點會發生什麼事? 在這種情況, `remove()` 回傳的就是取代根節點的節點. 試試看: 呼叫 `tree.remove()` 會發生什麼事.

就像大部分二元搜尋樹的操作一樣, 刪除節點需要 **O(h)** 的時間, 而 **h** 是樹的高度.

<!--
### Depth and height

Recall that the height of a node is the distance to its lowest leaf. We can calculate that with the following function:
-->

### 深度與高度

回顧一下, 一個節點的高度是該節點到其最下層葉節點的層數. 我們可以用這個函式計算出來:

```swift
  public func height() -> Int {
    if isLeaf {
      return 0
    } else {
      return 1 + max(left?.height() ?? 0, right?.height() ?? 0)
    }
  }
```

<!--
We look at the heights of the left and right branches and take the highest one. Again, this is a recursive procedure. Since this looks at all children of this node, performance is **O(n)**.

> **Note:** Swift's null-coalescing operator is used as shorthand to deal with `left` or `right` pointers that are nil. You could write this with `if let` but this is a lot more concise.

Try it out:
-->

我們取左右子樹中高度最大的那個來當節點高度. 再一次, 我們使用了遞迴. 因為這方法走訪了所有的子節點, 時間複雜度是 **O(n)**.

> **注意:** Swift 的 nil-coalescing 操作子在這裡是處理沒有 `左子節點` 或 `右子節點` 的狀況. 當然也可以使用 `if let`, 不過這樣比較簡潔.

試試看:

```swift
tree.height()  // 2
```
<!--
You can also calculate the *depth* of a node, which is the distance to the root. Here is the code:
-->

節點的 *深度* 是從該節點到根節點的層數, 以下是程式碼:

```swift
  public func depth() -> Int {
    var node = self
    var edges = 0
    while case let parent? = node.parent {
      node = parent
      edges += 1
    }
    return edges
  }
```

<!--
It steps upwards through the tree, following the `parent` pointers until we reach the root node (whose `parent` is nil). This takes **O(h)** time. Example:
-->

一直往父節點走訪, 直到根節點 ( `父節點` 是 nil ). 這要花 **O(h)** 的時間. 例如:

```swift
if let node9 = tree.search(9) {
  node9.depth()   // returns 2
}
```
<!--
### Predecessor and successor

The binary search tree is always "sorted" but that doesn't mean that consecutive numbers are actually next to each other in the tree.
-->

### 前驅節點與後繼節點
<!--
The binary search tree is always "sorted" but that doesn't mean that consecutive numbers are actually next to each other in the tree.
-->

雖然說二元搜尋樹總是"有序"的, 但那並不表示連續的數字都緊連著彼此.

![Example](Images/Tree2.png)

<!--
Note that you can't find the number that comes before `7` by just looking at its left child node. The left child is `2`, not `5`. Likewise for the number that comes after `7`.

The `predecessor()` function returns the node whose value precedes the current value in sorted order:
-->

注意到我們無法只走訪 `7` 的左子節點就找到排序中 `7` 以下的第一個值 ( `5` ), `7` 的左子節點是 `2` 不是 `5`. 對於排序中 `7` 以上的第一個值也是一樣的情況.

`predecessor()` 函式會回傳排序中第一個小於自己的節點 (前驅節點):

```swift
  public func predecessor() -> BinarySearchTree<T>? {
    if let left = left {
      return left.maximum()
    } else {
      var node = self
      while case let parent? = node.parent {
        if parent.value < value { return parent }
        node = parent
      }
      return nil
    }
  }
```

<!--
It's easy if we have a left subtree. In that case, the immediate predecessor is the maximum value in that subtree. You can verify in the above picture that `5` is indeed the maximum value in `7`'s left branch.

However, if there is no left subtree then we have to look at our parent nodes until we find a smaller value. So if we want to know what the predecessor is of node `9`, we keep going up until we find the first parent with a smaller value, which is `7`.

The code for `successor()` works the exact same way but mirrored:
-->

如果該節點有左子樹, 那前驅節點就是該子樹的最大葉節點. 你可以從上圖看出 `5` 的確是 `7` 左子樹中最大的葉節點.

不過呢, 如果該節點沒有左子樹, 那就只能往上找直到找到值小於該節點的節點. 所以如果想知道 `9` 的前驅節點是誰, 我們將一直往上找直到找到第一個小於 `9` 的節點, 就是 `7`.

`successor()` 做一樣的事情不過方向相反 (後繼節點):


```swift
  public func successor() -> BinarySearchTree<T>? {
    if let right = right {
      return right.minimum()
    } else {
      var node = self
      while case let parent? = node.parent {
        if parent.value > value { return parent }
        node = parent
      }
      return nil
    }
  }
```
<!--
Both these methods run in **O(h)** time.

> **Note:** There is a cool variation called a ["threaded" binary tree](../Threaded Binary Tree) where "unused" left and right pointers are repurposed to make direct links between predecessor and successor nodes. Very clever!
-->

兩個方法的時間複雜度都是 **O(h)**.

> **注意:** 有一個很酷的變化叫做 [線索二元樹](../Threaded Binary Tree), 其中每個節點都會擁有自己前驅及後繼的節點參考.

<!--
### Is the search tree valid?

If you were intent on sabotage you could turn the binary search tree into an invalid tree by calling `insert()` on a node that is not the root, like so:
-->

### 如何判斷是否為二元搜尋樹?

<!--
If you were intent on sabotage you could turn the binary search tree into an invalid tree by calling `insert()` on a node that is not the root, like so:
-->

你可以在非根節點上呼叫 `insert()` 來破壞二元搜尋樹的規則:

```swift
if let node1 = tree.search(1) {
  node1.insert(100)
}
```
<!--
The value of the root node is `7`, so a node with value `100` is supposed to be in the tree's right branch. However, you're not inserting at the root but at a leaf node in the tree's left branch. So the new `100` node is in the wrong place in the tree!

As a result, doing `tree.search(100)` gives nil.

You can check whether a tree is a valid binary search tree with the following method:
-->

根節點是 `7`, 所以 `100` 應該是在整棵樹的最右方葉節點. 但是呢, 如果不再根節點插入這個節點, 那這個 `100` 就會在錯誤的地方.

我們可以用這個方法來判斷一個樹狀結構是否為二元搜尋樹:

```swift
  public func isBST(minValue minValue: T, maxValue: T) -> Bool {
    if value < minValue || value > maxValue { return false }
    let leftBST = left?.isBST(minValue: minValue, maxValue: value) ?? true
    let rightBST = right?.isBST(minValue: value, maxValue: maxValue) ?? true
    return leftBST && rightBST
  }
```

<!--
This verifies that the left branch does indeed contain values that are less than the current node's value, and that the right branch only contains values that are larger.

Call it as follows:
-->

這個方法驗證左子樹中的所有節點都比自己還小, 而右子樹所有節點都比自身大.

所以試試看:

```swift
if let node1 = tree.search(1) {
  tree.isBST(minValue: Int.min, maxValue: Int.max)  // true
  node1.insert(100)                                 // EVIL!!!
  tree.search(100)                                  // nil
  tree.isBST(minValue: Int.min, maxValue: Int.max)  // false
}
```
<!--
## The code (solution 2)

We've implemented the binary tree node as a class but you can also use an enum.

The difference is reference semantics versus value semantics. Making a change to the class-based tree will update that same instance in memory. But the enum-based tree is immutable -- any insertions or deletions will give you an entirely new copy of the tree. Which one is best totally depends on what you want to use it for.

Here's how you'd make a binary search tree using an enum:
-->

## 程式碼 (解法 2)

我們用類別(class)來實作二元搜尋樹, 不過也可以用列舉(enum)來做.

這差異在於是傳值還是傳址呼叫. 使用類別做在更新時是對樹的記憶體區塊內容做更新. 而列舉每次操作都會回傳一個全新的物件. 哪個比較好就看你要怎麼用了.

使用列舉來實作二元搜尋樹:


```swift
public enum BinarySearchTree<T: Comparable> {
  case Empty
  case Leaf(T)
  indirect case Node(BinarySearchTree, T, BinarySearchTree)
}
```

<!--
The enum has three cases: 


- `Empty` to mark the end of a branch (the class-based version used `nil` references for this).
- `Leaf` for a leaf node that has no children.
- `Node` for a node that has one or two children. This is marked `indirect` so that it can hold `BinarySearchTree` values. Without `indirect` you can't make recursive enums.

> **Note:** The nodes in this binary tree don't have a reference to their parent node. It's not a major impediment but it will make certain operations slightly more cumbersome to implement.

As usual, we'll implement most functionality recursively. We'll treat each case of the enum slightly differently. For example, this is how you could calculate the number of nodes in the tree and the height of the tree:
-->

這個列舉有三個項目:

- `Empty` 表示空的位置 (在類別做法中使用 `nil` 來表示這點).
- `Leaf` 表示葉節點.
- `Node` 表示有一個或兩個子節點的節點. 這邊標記 `indirect` 才可以巢狀宣告 `BinarySearchTree` 類型的項目.

> **注意:** 這結構中節點沒有父節點的參考, 雖然不是很嚴重的問題, 但是在操作上是需要多花一點心思處理.

通常我們會使用遞迴來實作大部分的操作. 但是在列舉解法中會有一點點不同. 舉例來說, 這是計算總節點數和節點高度的函式:

```swift
  public var count: Int {
    switch self {
    case .Empty: return 0
    case .Leaf: return 1
    case let .Node(left, _, right): return left.count + 1 + right.count
    }
  }

  public var height: Int {
    switch self {
    case .Empty: return 0
    case .Leaf: return 1
    case let .Node(left, _, right): return 1 + max(left.height, right.height)
    }
  }
```
<!--
Inserting new nodes looks like this:
-->

插入一個節點:

```swift
  public func insert(newValue: T) -> BinarySearchTree {
    switch self {
    case .Empty:
      return .Leaf(newValue)

    case .Leaf(let value):
      if newValue < value {
        return .Node(.Leaf(newValue), value, .Empty)
      } else {
        return .Node(.Empty, value, .Leaf(newValue))
      }

    case .Node(let left, let value, let right):
      if newValue < value {
        return .Node(left.insert(newValue), value, right)
      } else {
        return .Node(left, value, right.insert(newValue))
      }
    }
  }
```
<!--
Try it out in a playground:
-->

在 playground 試試看吧:

```swift
var tree = BinarySearchTree.Leaf(7)
tree = tree.insert(2)
tree = tree.insert(5)
tree = tree.insert(10)
tree = tree.insert(9)
tree = tree.insert(1)
```

<!--
Notice that each time you insert something, you get back a completely new tree object. That's why you need to assign the result back to the `tree` variable.

Here is the all-important search function:
-->

每一次插入某些東西, 回傳值都是整個樹的物件. 所以需要重新給定新的值給 `tree` 變數.

以下是搜尋函式:

```swift
  public func search(x: T) -> BinarySearchTree? {
    switch self {
    case .Empty:
      return nil
    case .Leaf(let y):
      return (x == y) ? self : nil
    case let .Node(left, y, right):
      if x < y {
        return left.search(x)
      } else if y < x {
        return right.search(x)
      } else {
        return self
      }
    }
  }
```
<!--
As you can see, most of these functions have the same structure.

Try it out in a playground:
-->

可以看到, 大部分的函式是相同的結構.

在 playground 中試試看吧:

```swift
tree.search(10)
tree.search(1)
tree.search(11)   // nil
```
<!--
To print the tree for debug purposes you can use this method:
-->

為了 debug 可以用以下方法來印出樹狀結構:

```swift
extension BinarySearchTree: CustomDebugStringConvertible {
  public var debugDescription: String {
    switch self {
    case .Empty: return "."
    case .Leaf(let value): return "\(value)"
    case .Node(let left, let value, let right):
      return "(\(left.debugDescription) <- \(value) -> \(right.debugDescription))"
    }
  }
}
```
<!--
When you do `print(tree)` it will look something like this:

	((1 <- 2 -> 5) <- 7 -> (9 <- 10 -> .))

The root node is in the middle; a dot means there is no child at that position.
-->

當執行 `print(tree)`, 結果會像這樣:

	((1 <- 2 -> 5) <- 7 -> (9 <- 10 -> .))

根節點在中間; `.` 表示空位置.

<!--
## When the tree becomes unbalanced...

A binary search tree is *balanced* when its left and right subtrees contain roughly the same number of nodes. In that case, the height of the tree is *log(n)*, where *n* is the number of nodes. That's the ideal situation.

However, if one branch is significantly longer than the other, searching becomes very slow. We end up checking way more values than we'd ideally have to. In the worst case, the height of the tree can become *n*. Such a tree acts more like a [linked list](../Linked List/) than a binary search tree, with performance degrading to **O(n)**. Not good!

One way to make the binary search tree balanced is to insert the nodes in a totally random order. On average that should balance out the tree quite nicely. But it doesn't guarantee success, nor is it always practical.

The other solution is to use a *self-balancing* binary tree. This type of data structure adjusts the tree to keep it balanced after you insert or delete nodes. See [AVL tree](../AVL Tree) and [red-black tree](../Red-Black Tree) for examples.
-->

## 當樹不平衡

當二元搜尋樹的左子樹的總節點數和右子樹的總節點數差不多的時候, 這棵樹是 **平衡** 的. 這種情況時, 樹的高度大概是 *log(n)*, *n* 是總節點數. 這是個非常理想的狀況.

不過呢, 當樹的結構很明顯不平衡時, 搜尋就會變得很慢. 我們會走訪比想像中還要多的節點. 最糟的情況, 樹的高度會是 *n*. 那樣的結構就像 [鏈結列表](../Linked List/). 而搜尋的時間複雜度是 **O(n)**. 真糟糕!

要使一個二元搜尋樹平衡有個方法是隨機的插入節點. 平均來說這樣做對平衡還滿有用的. 但這並不保證成功, 雖然實際上是這樣.

另一個解法是使用 *自我平衡* 二元樹. 這種結構可以再新增和刪除節點時同時保持平衡. 去看看 [AVL 樹](../AVL Tree) 和 [紅黑樹](../Red-Black Tree) 吧.

<!--
## See also

[Binary Search Tree on Wikipedia](https://en.wikipedia.org/wiki/Binary_search_tree)
-->

## 相關閱讀

[Binary Search Tree on Wikipedia](https://en.wikipedia.org/wiki/Binary_search_tree)

*Written for the Swift Algorithm Club by [Nicolas Ameghino](http://www.github.com/nameghino) and Matthijs Hollemans*
