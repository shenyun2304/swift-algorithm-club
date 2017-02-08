# Threaded Binary Tree (引線二元樹)

<!--
A threaded binary tree is a special kind of [binary tree](../Binary Tree/) (a
tree in which each node has at most two children) that maintains a few extra
variables to allow cheap and fast **in-order traversal** of the tree.  We will
explore the general structure of threaded binary trees, as well as
[the Swift implementation](ThreadedBinaryTree.swift) of a fully functioning
threaded binary tree.

If you don't know what a tree is or what it is for, then
[read this first](../Tree/).
-->

這是一種特殊的 [二元樹](../Binary Tree/), 多了一些變數來辦到低功耗又快速的 **中序走訪**. 這裡會探索一般引線二元樹和 [在 Swift 中的實作](ThreadedBinaryTree.swift).

如果你還不知道樹是什麼, 那 [先看看這個](../Tree/).


<!--
## In-order traversal

The main motivation behind using a threaded binary tree over a simpler and
smaller standard binary tree is to increase the speed of an in-order traversal
of the tree.  An in-order traversal of a binary tree visits the nodes in the
order in which they are stored, which matches the underlying ordering of a
[binary search tree](../Binary Search Tree/).  This means most threaded binary
trees are also binary search trees.  The idea is to visit all the left children
of a node first, then visit the node itself, and then visit the right children
last.

An in-order traversal of any binary tree generally goes as follows (using Swift
syntax):
-->

## 中序走訪

會使用引線二元樹的動機是為了要加快循序走訪的速度. 在二元樹中要循序走訪每個節點, 結果會和 [二元搜尋樹](../Binary Search Tree/) 一樣. 這表示大部分的引線二元樹其實是二元搜尋樹. 概念是要先走訪左子節點, 然後節點本身, 最後右子節點.

在二元樹中的中序式走訪大致是這樣:


```swift
func traverse(n: Node?) {
  if (n == nil) { return
  } else {
    traverse(n.left)
    visit(n)
    traverse(n.right)
  }
}
```

<!--
Where `n` is a a node in the tree (or `nil`), each node stores its children as
`left` and `right`, and "visiting" a node can mean performing any desired
action on it.  We would call this function by passing to it the root of the
tree we wish to traverse.

While simple and understandable, this algorithm uses stack space proportional
to the height of the tree due to its recursive nature.  If the tree has **n**
nodes, this usage can range anywhere from **O(log n)** for a fairly balanced
tree, to **O(n)** to a very unbalanced tree.

A threaded binary tree fixes this problem.

> For more information about in-order traversals [see here](../Binary Tree/).
-->

`n` 是樹中的某個節點 (可以是 `nil`), 每個節點有左右兩個子節點 `left` 和 `right`, 而 "visit(節點)" 可以代表執行任何想要在該節點上的操作. 我們可以呼叫此方法並傳入根節點來達到我們要的走訪.

很簡單也很容易理解, 因為這是個遞迴的方法所以使用的堆疊空間和樹的高度成正比. 如果共有 **n** 個節點, 對於一般平衡的二元樹有 **O(log n)** 的效能, 而對最不平衡的二元樹則是 **O(n)**.

引線二元樹解決了此問題

> 詳細關於中序走訪的文章, 可以 [參考這裡](../Binary Tree/).


<!--
## Predecessors and successors

An in-order traversal of a tree yields a linear ordering of the nodes.  Thus
each node has both a **predecessor** and a **successor** (except for the first
and last nodes, which only have a successor or a predecessor respectively).  In
a threaded binary tree, each left child that would normally be `nil` instead
stores the node's predecessor (if it exists), and each right child that would
normally be `nil` instead stores the node's successor (if it exists).  This is
what separates threaded binary trees from standard binary trees.

There are two types of threaded binary trees:  **single threaded** and **double
threaded**:
- A single threaded tree keeps track of **either** the in-order predecessor
  **or** successor (left **or** right).
- A double threaded tree keeps track of **both** the in-order predecessor
  **and** successor (left **and** right).

Using a single or double threaded tree depends on what we want to accomplish.
If we only need to traverse the tree in one direction (either forward or
backward), then we use a single threaded tree.  If we want to traverse in both
directions, then we use a double threaded tree.

It is important to note that each node stores either its predecessor or its
left child, and either its successor or its right child.  The nodes do not
need to keep track of both.  For example, in a double threaded tree, if a node
has a right child but no left child, it will track its predecessor in place of
its left child.

Here is an example valid "full" threaded binary tree:
-->
## 前驅與後繼

中序式的走訪要讓節點線性排序. 因此每個節點都有 **前驅節點** 和 **後繼節點** (線性中的第一個和最後一個節點除外). 在引線二元樹中, 每個節點的 左/右 子節點正常來說都是 `nil` 而不是該節點的 前驅/後繼 節點, 這就是引線二元樹和一般二元樹不同的地方.

有兩種型態的引線二元樹: **單引線** 和 **雙引線**:

- 單引線二元樹追蹤中序式的前驅或後繼節點 (左節點 **或** 右節點)
- 雙引線二元樹追蹤中序式的前驅和後繼節點 (左節點 **和** 右節點)

要用單引線還是雙引線取決於我們想要達成什麼. 如果只是想單向走訪 (正向或反向), 那用單引線就好. 如果我們想正反向走訪, 那就要用雙引線.

注意到每個節點會儲存他的前驅節點/左子節點和後繼節點/右子節點. 不會同時儲存前驅及左子節點, 右邊亦然. 舉個例子., 在雙引線二元樹中, 如果某個節點有右子節點但沒有左子節點, 會用該節點的前驅節點來取代左節點.

這是一個合格且 "完整" 的引線二元樹:

![Full](Images/Full.png)


<!--
While the following threaded binary tree is not "full," it is still valid.  The
structure of the tree does not matter as long as it follows the definition of a
binary search tree:
-->

接下來這個引線二元樹並不 "完整", 但還是合格的. 樹的結構只要符合二元搜尋樹就可以:

![Partial](Images/Partial.png)

<!--
The solid lines denote the links between parents and children, while the dotted
lines denote the "threads."  It is important to note how the children and
thread edges interact with each other.  Every node besides the root has one
entering edge (from its parent), and two leaving edges: one to the left and one
to the right.  The left leaving edge goes to the node's left child if it
exists, and to its in-order predecessor if it does not.  The right leaving edge
goes to the node's right child if it exists, and to its in-order successor if
it does not.  The exceptions are the left-most node and the right-most node,
which do not have a predecessor or successor, respectively.
-->

實線表示父子節點的關聯, 虛線就是 "引線". 了解子節點和引線間的關係是很重要的. 除了根節點之外每個節點都有個進入的線 (從它的父節點), 和兩條離開的線: 一左一右. 如果該節點存在左子節點, 則左引線連接左子節點, 如果左子節點不存在, 則左引線連接前驅節點. 右邊亦然. 不過最左邊的節點和最右邊的節點例外, 因為一個沒有前驅一個沒有後繼.

<!--
## Representation

Before we go into detail about the methods of a threaded binary tree, we should
first explain how the tree itself is represented.  The core of this data
structure is the `ThreadedBinaryTree<T: Comparable>` class.  Each instance of
this class represents a node with six member variables:  `value`, `parent`,
`left`, `right`, `leftThread`, and `rightThread`.  Of all of these, only
`value` is required.  The other five are Swift *optionals* (they may be `nil`).
- `value: T` is the value of this node (e.g. 1, 2, A, B, etc.)
- `parent: ThreadedBinaryTree?` is the parent of this node
- `left: ThreadedBinaryTree?` is the left child of this node
- `right: ThreadedBinaryTree?` is the right child of this node
- `leftThread: ThreadedBinaryTree?` is the in-order predecessor of this node
- `rightThread: ThreadedBinaryTree?` is the in-order successor of this node

As we are storing both `leftThread` and `rightThread`, this is a double
threaded tree. Now we are ready to go over some of the member functions in our
`ThreadedBinaryTree` class.
-->

## 名詞解釋

再進一步研究引線二元樹之前, 先來解釋這種結構如何表達. 核心是 `ThreadedBinaryTree<T: Comparable>` 類別. 這類別的實例會有 6 個變數成員: `value`, `parent`, `left`, `right`, `leftThread` 和 `rightThread`. 其中只有 `value` 是非空的. 其他 5 個變數在 Swift 中是所謂 **optionals** (可以是 `nil`) 的.

- `value: T` 表示節點的值 (例如: 1, 2, A, B, etc.)
- `parent: ThreadedBinaryTree?` 表示此節點的父節點
- `left: ThreadedBinaryTree?` 表示此節點的左子節點
- `right: ThreadedBinaryTree?` 表示此節點的右子節點
- `leftThread: ThreadedBinaryTree?` 在中序式中此節點的前驅節點
- `rightThread: ThreadedBinaryTree?` 在中序式中此節點的後繼節點

這邊同時儲存了 `leftThread` 和 `rightThread`, 表示是個雙引線二元樹. 


<!--
## Traversal algorithm

Let's start with the main reason we're using a threaded binary tree.  It is now
very easy to find the in-order predecessor and the in-order successor of any
node in the tree.  If the node has no `left`/`right` child, we can simply
return the node's `leftThread`/`rightThread`.  Otherwise, it is trivial to move
down the tree and find the correct node.
-->

## 走訪演算法

開始進行我們使用引線二元樹的主要原因. 現在要找到節點的前驅和後繼節點都很容易了. 如果節點沒有 `left` / `right` 子節點, 那就很簡單的回傳 `leftThread` / `rightThread`. 除此之外, 要找到正確的節點往下層走訪也很清楚.

```swift
  func predecessor() -> ThreadedBinaryTree<T>? {
    if let left = left {
      return left.maximum()
    } else {
      return leftThread
    }
  }

  func successor() -> ThreadedBinaryTree<T>? {
    if let right = right {
      return right.minimum()
    } else {
      return rightThread
    }
  }
```

<!--
> Note: `maximum()` and `minimum()` are methods of `ThreadedBinaryTree` which
return the largest/smallest node in a given sub-tree.  See
[the implementation](ThreadedBinaryTree.swift) for more detail.

Because these are `ThreadedBinaryTree` methods, we can call
`node.predecessor()` or `node.successor()` to obtain the predecessor or
successor of any `node`, provided that `node` is a `ThreadedBinaryTree` object.

Because predecessors and/or successors are tracked, an in-order traversal of a
threaded binary tree is much more efficient than the recursive algorithm
outlined above.  We use these predecessor/successor attributes to great effect
in this new algorithm for both forward and backward traversals:
-->

> 注意: `maximum()` 和 `minimum()` 是 `ThreadedBinaryTree` 的方法, 會回傳在其子樹中 最大 / 最小的節點. 詳見 [實作](ThreadedBinaryTree.swift)

因為有追蹤前驅 和/或 後繼節點, 對於引線二元樹的中序走訪比上面一開始說的方法還要有效率. 使用 前驅 / 後繼屬性對於此演算法的走訪有很大的影響:

```swift
func traverseInOrderForward(visit: T -> Void) {
  var n: ThreadedBinaryTree
  n = minimum()
  while true {
    visit(n.value)
    if let successor = n.successor() {
      n = successor
    } else {
      break
    }
  }
}

func traverseInOrderBackward(visit: T -> Void) {
  var n: ThreadedBinaryTree
  n = maximum()
  while true {
    visit(n.value)
    if let predecessor = n.predecessor() {
      n = predecessor
    } else {
      break
    }
  }
}
```

<!--
Again, this a method of `ThreadedBinaryTree`, so we'd call it via
`node.traverseInorderForward(visitFunction)`.  Note that we are able to specify
a function that executes on each node as they are visited.  This function can
be anything you want, as long as it accepts `T` (the type of the values of the
nodes of the tree) and has no return value.

Let's walk through a forward traversal of a tree by hand to get a better idea
of how a computer would do it.  For example, take this simple threaded tree:
-->

這些是 `ThreadedBinaryTree` 的方法, 所以我們這樣使用它 `node.traverseInorderForward(visitFunction)`. 注意到我們可以給定任何一個我們希望走訪節點時執行的函式. 此方法只接收一個 `T` 型別的變數 (此型別為樹中節點值的型別), 而且沒有回傳值.

讓我們用正向走訪來當例子. 以這個引線樹來說:

![Base](Images/Base.png)

<!--
We start at the root of the tree, **9**.  Note that we don't `visit(9)` yet.
From there we want to go to the `minimum()` node in the tree, which is **2** in
this case.  We then `visit(2)` and see that it has a `rightThread`, and thus
we immediately know what its `successor()` is.  We follow the thread to **5**,
which does not have any leaving threads.  Therefore, after we `visit(5)`, we go
to the `minimum()` node in its `right` subtree, which is **7**.  We then
`visit(7)` and see that it has a `rightThread`, which we follow to get back to
**9**.  *Now* we `visit(9)`, and after noticing that it has no `rightThread`,
we go to the `minimum()` node in its `right` subtree, which is **12**.  This
node has a `rightThread` that leads to `nil`, which signals that we have
completed the traversal!  We visited the nodes in order **2, 5, 7, 9, 12**,
which intuitively makes sense, as that is their natural increasing order.

A backward traversal would be very similar, but you would replace `right`,
`rightThread`, `minimum()`, and `successor()` with `left`, `leftThread`,
`maximum()`, and `predecessor()`.
-->

我們從根節點開始, **9**. 這邊我們還沒做 `visit(9)`. 先走訪樹中的 `minimum()` 節點, **2**. 執行 `visit(2)` 然後利用它的 `rightThread` 很快的找到它的 `successor()`. 循線到達 **5**, 此節點沒有任何的引線. 因此, 在 `visit(5)` 之後我們走訪它 `right` 子樹的 `minimum()`, **7**. 執行 `visit(7)` 後發現它有 `rightThread`, 循線到達了 **9**, `visit(9)` 之後發現它沒有 `rightThread`, 所以走訪該節點 `right` 子樹的 `minimum()`, **12**. 此節點的 `rightThread` 是 `nil`, 這表示已經走訪完成了! 所以中序式走訪順序為 **2, 5, 7, 9 , 12**, 結果非常直觀.

<!--
## Insertion and deletion

The quick in-order traversal that a threaded binary trees gives us comes at a
small cost.  Inserting/deleting nodes becomes more complicated, as we have to
continuously manage the `leftThread` and `rightThread` variables.  Rather than
walking through some boring code, it is best to explain this with an example
(although you can read through [the implementation](ThreadedBinaryTree.swift)
if you want to know the finer details).  Please note that this requires
knowledge of binary search trees, so make sure you have 
[read this first](../Binary Search Tree/).

> Note: we do allow duplicate nodes in this implementation of a threaded binary
> tree.  We break ties by defaulting insertion to the right.

Let's start with the same tree that we used for the above traversal example:
-->

## 插入和刪除

引線二元樹帶給我們快速的中序式走訪, 但卻讓插入/刪除節點變得比較複雜一點, 我們必須持續的管理 `leftThread` 和  `rightThread` 變數. 與其研究無聊的程式碼, 用範例來解釋最好 (如果你想知道更深入的細節, 可以參考這個 [實作](ThreadedBinaryTree.swift)). 請注意到這邊需要有二元搜尋樹的基礎知識, 所以確認你有先讀過 [這個](../Binary Search Tree/).

> 注意: 在這個實作中, 我們確實可以允許重複的節點.  We break ties by defaulting insertion to the right.

讓我們用上面範例的樹當例子:

![Base](Images/Base.png)

<!--
Suppose we insert **10** into this tree.  The resulting graph would look like
this, with the changes highlighted in red:
-->

假設要插入 **10**. 結果會像這樣, 改變的部分用紅色顯示:

![Insert1](Images/Insert1.png)

<!--
If you've done your homework and are familiar with binary search trees, the
placement of this node should not surprise you.  What's new is how we maintain
the threads between nodes.  So we know that we want to insert **10** as
**12**'s `left` child.  The first thing we do is set **12**'s `left` child to
**10**, and set **10**'s `parent` to **12**.  Because **10** is being inserted
on the `left`, and **10** has no children of its own, we can safely set
**10**'s `rightThread` to its `parent` **12**.  What about **10**'s
`leftThread`?  Because we know that **10** < **12**, and **10** is the only
`left` child of **12**, we can safely set **10**'s `leftThread` to **12**'s
(now outdated) `leftThread`.  Finally we set **12**'s `leftThread = nil`, as it
now has a `left` child.

Let's now insert another node, **4**, into the tree:
-->

如果你熟悉二元搜尋樹, 那這個結果應該不意外. 不同地方在於我們如何維持引線和節點的關係. 我們知道要將 **10** 插入到 **12** 的 `left` 子節點. 我們要做的第一件事是將 **12** 的 `left` 子節點設定為 **10**, 而 **10** 的 `parent` 設為 **12**. 那 **10** 的 `leftThread` 怎麼處理? 因為 **10** < **12**, 而 **10** 又是 **12** 唯一的 `left` 子節點, 所以我們可以將 **10** 的 `leftThread` 設為 **12** 的 `leftThread` (此引線目前是錯誤的). 最後把 **12** 的 `leftThread` 設為 `nil`, 現在 **12** 有一個 `left` 子節點.

在來插入一個節點, **4** 看看:

![Insert2](Images/Insert2.png)

<!--
While we are inserting **4** as a `right` child, it follows the exact same
process as above, but mirrored (swap `left` and `right`).  For the sake of
completeness, we'll insert one final node, **15**:
-->

當我們將 **4** 當作 `right` 子節點插入時, 流程上面一模一樣, 但是左右相反 (`left` 換成 `right`). 為了完整起見, 我們最後在插入 **15**:

![Insert3](Images/Insert3.png)

<!--
Now that we have a fairly crowded tree, let's try removing some nodes.
Compared to insertion, deletion is a little more complicated.  Let's start with
something simple, like removing **7**, which has no children:
-->

現在這個樹有點擁擠, 讓我們嘗試移除移除一些節點. 和插入相比, 刪除比較複雜一點. 讓我們從簡單的開始, 像是移除 **7**, 是個葉節點:

![Remove1](Images/Remove1.png)

<!--
Before we can just throw **7** away, we have to perform some clean-up.  In this
case, because **7** is a `right` child and has no children itself, we can
simply set the `rightThread` of **7**'s `parent`(**5**) to **7**'s (now
outdated) `rightThread`.  Then we can just set **7**'s `parent`, `left`,
`right`, `leftThread`, and `rightThread` to `nil`, effectively removing it from
the tree.

Let's try something a little harder.  Say we remove **5** from the tree:
-->

因為我們可以直接移除 **7**, 所以要做一些清理的動作. 現在的狀況, 因為 **7** 是個 `right` 子節點, 而且沒有任何子節點, 我們可以簡單的將 **7** 的 `parent` (**5**) 的 `rightThread` 設定成 **7** 的 `rightThread`. 然後再將 **7** 的 `parent`, `left`, `right`, `leftThread`, `rightThread` 設為 `nil`, 這樣就將此節點移除了.

來增加一點難度. 假設我們要把 **5** 移除:

![Remove2](Images/Remove2.png)

<!--
This is a little trickier, as **5** has some children that we have to deal
with.  The core idea is to replace **5** with its first child, **2**.  To
accomplish this, we of course set **2**'s `parent` to **9** and set **9**'s
`left` child to **2**.  Note that **4**'s `rightThread` used to be **5**, but
we are removing **5**, so it needs to change.  It is now important to
understand two important properties of threaded binary trees:

1. For the rightmost node **m** in the `left` subtree of any node **n**,
**m**'s `rightThread` is **n**.
2. For the leftmost node **m** in the `right` subtree of any node **n**,
**m**'s `leftThread` is **n**.

Note how these properties held true before the removal of **5**, as **4** was
the rightmost node in **5**'s `left` subtree.  In order to maintain this
property, we must set **4**'s `rightThread` to **9**, as **4** is now the
rightmost node in **9**'s `left` subtree.  To completely remove **5**, all we
now have to do is set **5**'s `parent`, `left`, `right`, `leftThread`, and
`rightThread` to `nil`.

How about we do something crazy?  What would happen if we tried to remove
**9**, the root node?  This is the resulting tree:
-->

這有一點技巧, 因為 **5** 有一些子節點要處理. 主要概念是用 **5** 的第一個子節點 **2** 來取代它. 為了達到這點, 我們把 **2** 的 `parent` 設為 **9**

![Remove3](Images/Remove3.png)

Whenever we want to remove a node that has two children, we take a slightly
different approach than the above examples.  The basic idea is to replace the
node that we want to remove with the leftmost node in its `right`  subtree,
which we call the replacement node.

> Note: we could also replace the node with the rightmost node in its `left`
> subtree.  Choosing left or right is mostly an arbitrary decision.

Once we find the replacement node, **10** in this case, we remove it from the
tree using the algorithms outlined above.  This ensures that the edges in the
`right` subtree remain correct.  From there it is easy to replace **9** with
**10**, as we just have to update the edges leaving **10**.  Now all we have to
do is fiddle with the threads in order to maintain the two properties outlined
above.  In this case, **12**'s `leftThread` is now **10**. Node **9** is no
longer needed, so we can finish the removal process by setting all of its
variables to `nil`.

In order to illustrate how to remove a node that has only a `right` child,
we'll remove one final node, **12** from the tree:

![Remove4](Images/Remove4.png)

The process to remove **12** is identical to the process we used to remove
**5**, but mirrored.  **5** had a `left` child, while **12** has a `right`
child, but the core algorithm is the same.

And that's it!  This was just a quick overview of how insertion and deletion
work in threaded binary trees, but if you understood these examples, you should
be able to insert or remove any node from any tree you want.  More detail can
of course be found in
[the implementation](ThreadedBinaryTree.swift).


## Miscellaneous methods

There are many other smaller operations that a threaded binary tree can do,
such as `searching()` for a node in the tree, finding the `depth()` or
`height()` of a node, etc.  You can check
[the implementation](ThreadedBinaryTree.swift) for the full technical details.
Many of these methods are inherent to binary search trees as well, so you can
find [further documentation here](../Binary Search Tree/).


## See also 

[Threaded Binary Tree on Wikipedia](https://en.wikipedia.org/wiki/Threaded_binary_tree)

*Written for the Swift Algorithm Club by
[Jayson Tung](https://github.com/JFTung)*

*Images made using www.draw.io*
