# Binary Tree 二元樹
<!--
A binary tree is a [tree](../Tree/) where each node has 0, 1, or 2 children. This is a binary tree:
-->

二元樹是一種 [樹](../Tree/), 其每個節點只能擁有 0, 1, 2 個子節點.

![A binary tree](Images/BinaryTree.png)

<!--
The child nodes are usually called the *left* child and the *right* child. If a node doesn't have any children, it's called a *leaf* node. The *root* is the node at the very top of the tree (programmers like their trees upside down).

Often nodes will have a link back to their parent but this is not strictly necessary.

Binary trees are often used as [binary search trees](../Binary Search Tree/). In that case, the nodes must be in a specific order (smaller values on the left, larger values on the right). But this is not a requirement for all binary trees.

For example, here is a binary tree that represents a sequence of arithmetical operations, `(5 * (a - 10)) + (-4 * (3 / b))`:
-->

在二元樹中, 子節點通常稱為 *左節點* 和 *右節點*. 如果某個節點沒有任何一個子節點, 則我們稱之為 *葉節點*. *根節點* 是指整個樹中最上層的節點 (程式設計員喜歡將樹上下顛倒).

通常節點都會含有其父節點的參考物件, 但這並非必要.

[二元搜尋樹](../Binary Search Tree/) 是二元樹常見的使用方式. 在二元搜尋樹中每個節點還需要依照特別的順序排列 (小的值在左邊, 大的值在右邊). 但對二元樹來說這不是必需的.

舉例來說, 這是一個代表 `(5 * (a - 10)) + (-4 * (3 / b))` 四則運算的二元樹:

![A binary tree](Images/Operations.png)

<!--
## The code

Here's how you could implement a general-purpose binary tree in Swift:
-->

## 程式碼

這是 Swift 實現一般二元樹的例子:

```swift
public indirect enum BinaryTree<T> {
  case node(BinaryTree<T>, T, BinaryTree<T>)
  case empty
}
```

<!--
As an example of how to use this, let's build that tree of arithmetic operations:
-->

讓我們使用這個結構來建立上述的四則運算:

```swift
// leaf nodes
let node5 = BinaryTree.node(.empty, "5", .empty)
let nodeA = BinaryTree.node(.empty, "a", .empty)
let node10 = BinaryTree.node(.empty, "10", .empty)
let node4 = BinaryTree.node(.empty, "4", .empty)
let node3 = BinaryTree.node(.empty, "3", .empty)
let nodeB = BinaryTree.node(.empty, "b", .empty)

// intermediate nodes on the left
let Aminus10 = BinaryTree.node(nodeA, "-", node10)
let timesLeft = BinaryTree.node(node5, "*", Aminus10)

// intermediate nodes on the right
let minus4 = BinaryTree.node(.empty, "-", node4)
let divide3andB = BinaryTree.node(node3, "/", nodeB)
let timesRight = BinaryTree.node(minus4, "*", divide3andB)

// root node
let tree = BinaryTree.node(timesLeft, "+", timesRight)
```

<!--
You need to build up the tree in reverse, starting with the leaf nodes and working your way up to the top.

It will be useful to add a `description` method so you can print the tree:
-->

你需要反方向來建立這個樹, 從葉節點向上建立. 建立一個 `description` 方法有助於看清楚樹的結構:


```swift
extension BinaryTree: CustomStringConvertible {
  public var description: String {
    switch self {
    case let .node(left, value, right):
      return "value: \(value), left = [" + left.description + "], right = [" 
                                         + right.description + "]"
    case .empty:
      return ""
    }
  }
}
```

<!--
If you `print(tree)` you should see something like this:
-->

如果使用 `print(tree)` 你將會看到這樣的結果:

	value: +, left = [value: *, left = [value: 5, left = [], right = []], right = [value: -, left = [value: a, left = [], right = []], right = [value: 10, left = [], right = []]]], right = [value: *, left = [value: -, left = [], right = [value: 4, left = [], right = []]], right = [value: /, left = [value: 3, left = [], right = []], right = [value: b, left = [], right = []]]]

<!--
With a bit of imagination, you can see the tree structure. ;-) It helps if you indent it:
-->

加上一點想像力, 你會看到這個樹的結構. ;-) 縮排後:

	value: +, 
		left = [value: *, 
			left = [value: 5, left = [], right = []], 
			right = [value: -, 
				left = [value: a, left = [], right = []], 
				right = [value: 10, left = [], right = []]]], 
		right = [value: *, 
			left = [value: -, 
				left = [], 
				right = [value: 4, left = [], right = []]], 
			right = [value: /, 
				left = [value: 3, left = [], right = []], 
				right = [value: b, left = [], right = []]]]

<!--
Another useful method is counting the number of nodes in the tree:
-->
另一個方便的方法是計算節點的數量:

```swift
  public var count: Int {
    switch self {
    case let .node(left, _, right):
      return left.count + 1 + right.count
    case .empty:
      return 0
    }
  }
```
<!--
On the tree from the example, `tree.count` should be 12.

Something you often need to do with trees is traverse them, i.e. look at all the nodes in some order. There are three ways to traverse a binary tree:

1. *In-order* (or *depth-first*): first look at the left child of a node, then at the node itself, and finally at its right child.
2. *Pre-order*: first look at a node, then at its left and right children. 
3. *Post-order*: first look at the left and right children and process the node itself last.

Here is how you'd implement that:
-->

上述例子中的樹, `tree.count` 應該是 12.

你常會對一個樹做的動作是走訪他們所有的節點. 這裡有三個走訪二元樹的方法:

1. *中序式* ( 又稱 *深度優先* ): 先走訪左節點, 再來是節點本身, 最後是右節點. ( 左 -> 中 -> 右 )
2. *前序式*: 先走訪節點本身, 再來是左節點, 最後是右節點. ( 中 -> 左 -> 右 )
3. *後序式*: 先走訪左節點, 再來是右節點, 最後是節點本身. ( 左 -> 右 -> 中 )

<!--
Here is how you'd implement that:
-->

實作:

```swift
  public func traverseInOrder(process: (T) -> Void) {
    if case let .node(left, value, right) = self {
      left.traverseInOrder(process: process)
      process(value)
      right.traverseInOrder(process: process)
    }
  }
  
  public func traversePreOrder(process: (T) -> Void) {
    if case let .node(left, value, right) = self {
      process(value)
      left.traversePreOrder(process: process)
      right.traversePreOrder(process: process)
    }
  }
  
  public func traversePostOrder(process: (T) -> Void) {
    if case let .node(left, value, right) = self {
      left.traversePostOrder(process: process)
      right.traversePostOrder(process: process)
      process(value)
    }
  }
```

<!--
As is common when working with tree structures, these functions call themselves recursively.

For example, if you traverse the tree of arithmetic operations in post-order, you'll see the values in this order:
-->

在樹的操作中, 這幾個方法很常見, 而這幾個函數會遞迴的呼叫自己.

舉例來說, 如果你要使用後序式走訪, 你將會得到以下走訪順序:


	5
	a
	10
	-
	*
	4
	-
	3
	b
	/
	*
	+

<!--
The leaves appear first. The root node appears last.

You can use a stack machine to evaluate these expressions, something like the following pseudocode:
-->

葉節點會最先被走訪, 而根節點會最後才走訪到.

你可以使用堆疊器來編譯這個表示式, 而你會得到類似以下的偽代碼:


```swift
tree.traversePostOrder { s in 
  switch s {
  case this is a numeric literal, such as 5:
    push it onto the stack
  case this is a variable name, such as a:
    look up the value of a and push it onto the stack
  case this is an operator, such as *:
    pop the two top-most items off the stack, multiply them,
    and push the result back onto the stack
  }
  the result is in the top-most item on the stack
}
```

*Written for Swift Algorithm Club by Matthijs Hollemans*
