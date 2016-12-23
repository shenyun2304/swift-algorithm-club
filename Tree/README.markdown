# Trees (樹)

<!--
A tree represents hierarchical relationships between objects. This is a tree:
-->

樹呈現了物件之間的階層關係. 像這樣:

![A tree](Images/Tree.png)

<!--
A tree consists of nodes, and these nodes are linked to one another.

Nodes have links to their children and usually to their parent as well. The children are the nodes below a given node; the parent is the node above. A node always has just one parent but can have multiple children.
-->

樹是由節點組成, 而節點和節點之間有連結關係.

大部分的節點會連結自己的子節點和父節點. 子節點是指所屬某個節點以下的節點; 而父節點是以上的節點. 每個節點總是只有一個父節點, 但是可以擁有很多個子節點.

![A tree](Images/ParentChildren.png)

<!--
A node without a parent is the *root* node. A node without children is a *leaf* node.

The pointers in a tree do not form cycles. This is not a tree:
-->

沒有父節點的節點稱為 *根節點*. 而沒有子節點的節點稱為 *葉節點*.

在樹中的連結不可以形成迴圈. 像這樣就不是一個樹:

![Not a tree](Images/Cycles.png)

<!--
Such a structure is called a [graph](../Graph/). A tree is really a very simple form of a graph. (In a similar vein, a [linked list](../Linked List/) is a simple version of a tree. Think about it!)

This article talks about a general-purpose tree. That's a tree without any kind of restrictions on how many children each node may have, or on the order of the nodes in the tree.

Here's a basic implementation in Swift:
-->

上面這種結構稱為 [圖](../Graph/). 樹是一種非常簡單的圖. (延伸思路, 一個 [鏈結列表](../Linked List/) 是一個非常簡單的樹.)

這篇文章我們討論一般廣義的樹. 也就是說沒有任何子節點數或節點順序的條件:

```swift
public class TreeNode<T> {
  public var value: T

  public weak var parent: TreeNode?
  public var children = [TreeNode<T>]()

  public init(value: T) {
    self.value = value
  }

  public func addChild(_ node: TreeNode<T>) {
    children.append(node)
    node.parent = self
  }
}
```

<!--
This describes a single node from the tree. It has a value of generic type `T`, a reference to a `parent` node, and an array of child nodes.

It will be useful to add a `description` method so you can print the tree:
-->

上面描述了一個樹中的節點. 接收泛型 `T` 的元素, 一個 `父節點` 的參考, 和子節點陣列.

為了打印出樹的結構, 這裡增加 `description` 的方法:


```swift
extension TreeNode: CustomStringConvertible {
  public var description: String {
    var s = "\(value)"
    if !children.isEmpty {
      s += " {" + children.map { $0.description }.joined(separator: ", ") + "}"
    }
    return s
  }
}
```

<!--
To see this in action in a playground:
-->

在 playground 中試試看:

```swift
let tree = TreeNode<String>(value: "beverages")

let hotNode = TreeNode<String>(value: "hot")
let coldNode = TreeNode<String>(value: "cold")

let teaNode = TreeNode<String>(value: "tea")
let coffeeNode = TreeNode<String>(value: "coffee")
let chocolateNode = TreeNode<String>(value: "cocoa")

let blackTeaNode = TreeNode<String>(value: "black")
let greenTeaNode = TreeNode<String>(value: "green")
let chaiTeaNode = TreeNode<String>(value: "chai")

let sodaNode = TreeNode<String>(value: "soda")
let milkNode = TreeNode<String>(value: "milk")

let gingerAleNode = TreeNode<String>(value: "ginger ale")
let bitterLemonNode = TreeNode<String>(value: "bitter lemon")

tree.addChild(hotNode)
tree.addChild(coldNode)

hotNode.addChild(teaNode)
hotNode.addChild(coffeeNode)
hotNode.addChild(chocolateNode)

coldNode.addChild(sodaNode)
coldNode.addChild(milkNode)

teaNode.addChild(blackTeaNode)
teaNode.addChild(greenTeaNode)
teaNode.addChild(chaiTeaNode)

sodaNode.addChild(gingerAleNode)
sodaNode.addChild(bitterLemonNode)
```

<!--
If you print out the value of `tree`, you'll get:
-->

把樹打印出來, 你會得到:

	beverages {hot {tea {black, green, chai}, coffee, cocoa}, cold {soda {ginger ale, bitter lemon}, milk}}

<!--
That corresponds to the following structure:
-->

這個對應的樹狀結構長這樣:

![Example tree](Images/Example.png)

<!--
The `beverages` node is the root because it has no parent. The leaves are `black`, `green`, `chai`, `coffee`, `cocoa`, `ginger ale`, `bitter lemon`, `milk` because they don't have any child nodes.

For any node you can look at the `parent` property and work your way back up to the root:
-->

因為 `beverages` 沒有父節點, 所以它是根節點, 葉節點是 `black`, `green`, `chai`, `coffee`, `cocoa`, `ginger ale`, `bitter lemon`, `milk`, 因為它們沒有任何子節點.

在任何一個節點上, 你總是可以向上找到該節點的 `父節點`, 而一路向上最後可以達到 `根節點`.


```swift
teaNode.parent           // this is the "hot" node
teaNode.parent!.parent   // this is the root
```
<!--
We often use the following definitions when talking about trees:

- **Height of the tree.** This is the number of links between the root node and the lowest leaf. In our example the height of the tree is 3 because it takes three jumps to go from the root to the bottom.

- **Depth of a node.** The number of links between this node and the root node. For example, the depth of `tea` is 2 because it takes two jumps to reach the root. (The root itself has depth 0.)

There are many different ways to construct trees. For example, sometimes you don't need to have a `parent` property at all. Or maybe you only need to give each node a maximum of two children -- such a tree is called a [binary tree](../Binary Tree/). A very common type of tree is the [binary search tree](../Binary Search Tree/) (or BST), a stricter version of a binary tree where the nodes are ordered in a particular way to speed up searches.

The general purpose tree I've shown here is great for describing hierarchical data, but it really depends on your application what kind of extra functionality it needs to have.

Here's an example of how you could use the `TreeNode` class to determine if the tree contains a particular value. You first look at the node's own `value` property. If there's no match, then you look at all your children in turn. Of course, those children are also `TreeNodes` so they will repeat the same process recursively: first look at their own value and then at their children. And their children will also do the same thing again, and so on... Recursion and trees go hand-in-hand.

Here's the code:
-->

我們對樹通常有以下幾個定義:

- **樹的高度**: 這是從根節點到最末端葉節點中間連結的數量. 在我們的例子中, 高度是 3.

- **節點的深度**: 這是計算該節點到根節點中間連結的數量. 拿 `tea` 當例子, 深度是 2. (根節點的深度是 0)

有很多建構樹的方式. 舉裡來說, 有時候你根本不需要 `父節點`. 或者你需要每個節點最多只有兩個子節點 -- 這種樹稱為 [二元樹](../Binary Tree/). 常見的二元樹是 [二元搜尋樹](../Binary Search Tree/) (或 BST), 這是一個跟二元樹很相近的結構, 差異只在節點是有排序過的.

樹的主要目的在於描述階層型態的資料, 但這也是要看你的應用需要哪些功能, 再考慮是否使用這樣的結構.

這裡展示你如何使用 `TreeNode` 類別來實作搜尋功能. 首先先判斷節點本身的 `值` 和要搜尋的是否相同, 如果不同, 就開始搜尋所有的子節點. 這些子節點也是 `TreeNode` 類別的物件, 所以可以遞迴的重複這個步驟.

程式碼:

```swift
extension TreeNode where T: Equatable {
  func search(_ value: T) -> TreeNode? {
    if value == self.value {
      return self
    }
    for child in children {
      if let found = child.search(value) {
        return found
      }
    }
    return nil
  }
}
```
<!--
And an example of how to use this:
-->

使用範例:

```swift
tree.search("cocoa")    // returns the "cocoa" node
tree.search("chai")     // returns the "chai" node
tree.search("bubbly")   // nil
```

<!--
It's also possible to describe a tree using nothing more than an array. The indices in the array then create the links between the different nodes. For example, if we have:
-->

也可以用一個陣列來表示一棵樹. 陣列中, 元素的索引就是每個節點的值, 而元素內容則表示該節點父節點的索引, 舉例來說:

	0 = beverage	5 = cocoa		9  = green
	1 = hot			6 = soda		10 = chai
	2 = cold		7 = milk		11 = ginger ale
	3 = tea			8 = black		12 = bitter lemon
	4 = coffee				

<!--
Then we can describe the tree with the following array:
-->

我們可以表示成:

	[ -1, 0, 0, 1, 1, 1, 2, 2, 3, 3, 3, 6, 6 ]

<!--
Each entry in the array is a pointer to its parent node. The item at index 3, `tea`, has the value 1 because its parent is `hot`. The root node points to `-1` because it has no parent. You can only traverse such trees from a node back to the root but not the other way around.

By the way, sometimes you see algorithms using the term *forest*. Unsurprisingly, that is the name given to a collection of separate tree objects. For an example of this, see [union-find](../Union-Find/).
-->

在索引 3 的元素, `tea`, 他的值是 1 表示他的父節點是 `hot`. 因為根節點沒父節點, 所以值是 `-1`. 

這種表示法, 每個節點只能往上走訪, 沒辦法往下走訪.


對了, 有時候你會看到某些演算法使用 *林*. 顧名思義, 那就是一個很多樹的集合. 參考 [聯集查找](../Union-Find/).

*Written for Swift Algorithm Club by Matthijs Hollemans*
