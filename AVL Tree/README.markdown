# AVL Tree
<!--
An AVL tree is a self-balancing form of a [binary search tree](../Binary Search Tree/), in which the height of subtrees differ at most by only 1.

A binary tree is *balanced* when its left and right subtrees contain roughly the same number of nodes. That is what makes searching the tree really fast. But if a binary search tree is unbalanced, searching can become really slow.

This is an example of an unbalanced tree:

-->

AVL 樹是一個自我平衡的 [二元搜尋樹](../Binary Search Tree/), 其中子樹的高度最多相差 1 .

當一個二元樹的左子樹和右子樹擁有的節點數量差不多, 則我們稱這個二元樹是 **平衡** 的. 這樣的設計會使得搜尋非常快速. 但是如果當二元搜尋樹是不平衡的時候, 搜尋可能會非常緩慢.

這是一個不平衡的樹:

![Unbalanced tree](Images/Unbalanced.png)

<!--
All the children are in the left branch and none are in the right. This is essentially the same as a [linked list](../Linked List/). As a result, searching takes **O(n)** time instead of the much faster **O(log n)** that you'd expect from a binary search tree.

A balanced version of that tree would look like this:
-->

所有的子樹都在左邊分支, 這種情況基本上跟 [鏈結列表](../Linked List/) 是一樣的. 搜尋的複雜度是 **O(n)**, 比二元搜尋樹的 **O(log n)** 不好.

這是一個平衡的樹:

![Balanced tree](Images/Balanced.png)

<!--
One way to make the binary search tree balanced is to insert the nodes in a totally random order. But that doesn't guarantee success, nor is it always practical.

The other solution is to use a *self-balancing* binary tree. This type of data structure adjusts the tree to keep it balanced after you insert or delete nodes. The height of such a tree is guaranteed to be *log(n)* where *n* is the number nodes. On a balanced tree all insert, remove, and search operations take only **O(log n)** time. That means fast. ;-)
-->

把一個二元搜尋數做平衡化有一個做法是隨機的新增節點. 但是這方法並不保證可以成功, 也不總是實用.

另一種做法是使用 **自我平衡二元樹**. 這種資料結構讓樹結構在新增和刪除節點時保持平衡狀態. 在 *n* 個節點的樹中保證樹的高度是 *log(n)*. 並且在平衡樹中所有的新增, 移除, 和搜尋操作只會佔用 **O(log n)** 時間, 快多了. ;-)

<!--
## Introducing the AVL tree

An AVL tree fixes any imbalances by "rotating" the tree to the left or right.

A node in an AVL tree is considered balanced if its subtrees differ in "height" by at most 1. The tree itself is balanced if all its nodes are balanced.

The *height* of a node is how many steps it takes to get to that node's lowest leaf. For example, in the following tree it takes three steps to go from A to E, so the height of A is 3. The height of B is 2, the height of C is 1, and the height of the others is 0 because they are leaf nodes.

-->

## AVL tree 介紹

AVL 樹 使用"旋轉"來解決所有的不平衡狀態

在 AVL 樹中, 當一個節點的左子樹和右子樹的高度相差不超過 1 的情況下, 這個節點被認定為平衡, 當所有節點都平衡時, 這個樹就會是平衡狀態了.

一個節點的 *高度* 是指從該節點到其葉節點中間的層數. 舉例來說, 在下列的樹中從 A 到 E 經過了三層, 所以 A 的高度就是 3. B 的高度是 2, 而 C 的高度是 1, 因為 D 和 F 是葉節點, 所以 D 和 F 的高度是 0.

![Node height](Images/Height.png)

<!--
As mentioned, in an AVL tree a node is balanced if its left and right subtree have the same height. It doesn't have to be the exact same height, but the difference may not be greater than 1. These are all examples of balanced trees:
-->

如剛剛提到的, 一個 AVL 樹的節點只有在左子樹高度和右子樹高度相差不大於 1 時才會被認定為平衡. 這裡是一些平衡樹的例子:

![Balanced trees](Images/BalanceOK.png)

<!--
But the following are trees that are unbalanced, because the height of the left subtree is too large compared to the right subtree:
-->

以下這些樹並非平衡樹, 因為左右子樹的高度相差大於 1 .

![Unbalanced trees](Images/BalanceNotOK.png)

<!--
The difference between the heights of the left and right subtrees is called the *balance factor*. It is calculated as follows:

	balance factor = abs(height(left subtree) - height(right subtree))

If after an insertion or deletion the balance factor becomes greater than 1, then we need to re-balance this part of the AVL tree. And that is done with rotations.
-->

左右子樹高度的差叫做 *平衡因子(balance factor)*. 計算方法如下:

	balance factor = abs(height(left subtree) - height(right subtree))

如果因為新增或移除節點使得平衡因子大於 1 . 則我們需要對這個部分的分支做重新平衡的動作. 而此動作稱為 "輪轉".

<!--
## Rotations

Each tree node keeps track of its current balance factor in a variable. After inserting a new node, we need to update the balance factor of its parent node. If that balance factor becomes greater than 1, we "rotate" part of that tree to restore the balance.

TODO: describe with pictures how these rotations work

Insertion never needs more than 2 rotations. Removal might require up to *log(n)* rotations.
-->

## 輪轉

每一個節點將自己的平衡因子儲存在一個變數中, 每當新增一個節點, 我們需要更新該節點的父節點的平衡因子. 如果該父節點的平衡因子大於 1, 則我們需要 "輪轉" 某些部分去維持整個平衡.

TODO: 需要圖片來解釋輪轉動作原理

新增從來不需要超過 2 次輪轉. 移除可能需要 *log(n)* 次輪轉.

<!--
## The code

Most of the code in [AVLTree.swift](AVLTree.swift) is just regular [binary search tree](../Binary Search Tree/) stuff. You'll find this in any implementation of a binary search tree. For example, searching the tree is exactly the same. The only things that an AVL tree does slightly differently are inserting and deleting the nodes.

> **Note:** If you're a bit fuzzy on the regular operations of a binary search tree, I suggest you [catch up on those first](../Binary Search Tree/). It will make the rest of the AVL tree easier to understand.

The interesting bits are in the `balance()` method which is called after inserting or deleting a node. 
-->

## 程式碼

在 [AVLTree.swift](AVLTree.swift) 中的程式碼大部分是標準 [二元搜尋樹](../Binary Search Tree/) 的東西. 舉例來說, 在樹中的搜尋幾乎是一樣的, AVL 樹不一樣的地方在新增和移除節點的地方.


> **注意:** 如果你對於一般的二元搜尋數操作已經模糊了, 我建議你 [先複習一下](../Binary Search Tree/). 這樣將會讓 AVL 樹更加容易理解.

有趣的地方在於每次新增或移除後執行的 `balance()` 方法

<!--
## See also

[AVL tree on Wikipedia](https://en.wikipedia.org/wiki/AVL_tree)

AVL tree was the first self-balancing binary tree. These days, the [red-black tree](../Red-Black Tree/) seems to be more popular.
-->

## 相關閱讀

[維基百科上的 AVL 樹](https://en.wikipedia.org/wiki/AVL_tree)

AVL 樹是第一個自我平衡的樹結構. 不過最近 [紅黑樹](../Red-Black Tree/) 好像比較受歡迎.



*Written for Swift Algorithm Club by Mike Taghavi and Matthijs Hollemans*
