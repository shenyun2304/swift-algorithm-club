# Red-Black Tree (紅黑樹)

<!--
A Red-Black tree is a special version of a [Binary Search  Tree](https://github.com/raywenderlich/swift-algorithm-club/tree/master/Binary%20Search%20Tree). Binary search trees(BSTs) can become unbalanced after a lot of insertions/deletions. The only difference between a node from a BST and a Red-Black Tree(RBT) is that RBT nodes have a color property added to them which can either be red or black. A RBT rebalances itself by making sure the following properties hold:
-->

紅黑樹是特殊的 [二元搜尋樹](../Binary Search Tree/). 在二元搜尋樹中, 有可能因為大量的插入和刪除操作, 而使得樹變得不平衡. 二元搜尋樹和紅黑樹唯一的差異就在於紅黑樹有一個顏色的屬性, 每個節點可以是紅色或黑色. 一個紅黑樹會依照下列屬性重新的自我平衡:

<!--
## Properties
1. A node is either red or black
2. The root is always black
3. All leaves are black
4. If a node is red, both of its children are black
5. Every path to a leaf contains the same number of black nodes (The amount of black nodes met when going down a RBT is called the black-depth of the tree.)
-->

## 屬性
1. 每個節點要不是紅色就是黑色.
2. 根節點永遠是黑色.
3. 所有的葉節點都是黑色.
4. 如果某個節點是紅色, 則它的子節點都是黑色.
5. 每個到葉節點的路徑上會擁有一樣數量的黑色節點. (當向下走訪節點時遇到的黑色節點數量稱為樹的"黑色深度")

<!--
## Methods
* `insert(_ value: T)` inserts the value into the tree
* `insert(_ values: [T])` inserts an array of values into the tree
* `delete(_ value: T)` deletes the value from the tree
* `find(_ value: T) -> RBTNode<T>` looks for a node in the tree with given value and returns it
* `minimum(n: RBTNode<T>) -> RBTNode<T>` looks for the maximum value of a subtree starting at the given node
* `maximum(n: RBTNode<T>) -> RBTNode<T>` looks for the minimum value of a subtree starting at the given node
* `func verify()` verifies if the tree is still valid. Prints warning messages if this is not the case
-->

## 方法

* `insert(_ value: T)`: 插入一個元素到樹中.
* `insert(_ values: [T])`: 插入一個元素陣列到樹中.
* `delete(_ value: T)`: 從樹中刪除一個元素.
* `find(_ value: T) -> RBTNode<T>` 查詢樹中某個元素值, 並回傳該節點.
* `minimum(n: RBTNode<T>) -> RBTNode<T>` 找到給定的節點子樹中的最小節點.
* `maximum(n: RBTNode<T>) -> RBTNode<T>` 找到給定的節點子樹中的最大節點.
* `func verify()` 驗證樹的條件是否還符合. 如果不符合了則打印出警告訊息.

<!--
## Rotation

In order to rebalance their nodes RBTs use an operation known as rotation. You can either left or right rotate a node and its child. Rotating a node and its child swaps the nodes and interchanges their subtrees. Left rotation swaps the parent node with its right child. while right rotation swaps the parent node with its left child.

Left rotation:

-->
## 旋轉

為了讓紅黑樹可以重新平衡, 紅黑樹使用一種稱為旋轉的操作. 你可以對節點選擇左旋轉或右旋轉. 旋轉是將該節點及其子樹和其子節點交換位置. 左旋轉是將節點和其右節點做互換, 右旋轉是將節點和其左節點做互換.

<!--
Left rotation:
-->

左旋轉:

```
before left rotating p       after left rotating p  
     p                         b
   /   \                     /   \
  a     b          ->       p     n
 / \   / \                 / \   
n   n  n  n               a   n
                         / \
                        n   n
```
<!--
Right rotation:
-->

右旋轉:

```
before right rotating p       after right rotating p  
     p                         a
   /   \                     /   \
  a     b          ->       n     p
 / \   / \                       / \   
n   n  n  n                     n   b
                                   / \
                                  n   n
```

<!--
## Insertion

We create a new node with the value to be inserted into the tree. The color of this new node is always red.
We perform a standard BST insert with this node. Now the three might not be a valid RBT anymore.
We now go through several insertion steps in order to make the tree valid again. We call the just inserted node n.

Step 1. We check if n is the rootNode, if so we paint it black and we are done. If not we go to step 2.

We now know that n at least has a parent as it is not the rootNode.

Step 2. We check if the parent of n is black if so we are done. If not we go to step 3.

We now know the parent is also not the root node as the parent is red. Thus n also has a grandparent and thus also an uncle as every node has two children. This uncle may however be a nullLeaf

Step 3. We check if n's uncle is red. If not we go to 4. If n's uncle is indeed red we recolor uncle and parent to black and n's grandparent to red. We now go back to step 1 performing the same logic on n's grandparent.

From here there are four cases:
- **The left left case.** n's parent is the left child of its parent and n is the left child of its parent.
- **The left right case** n's parent is the left child of its parent and n is the right child of its parent.
- **The right right case** n's parent is the right child of its parent and n is the right child of its parent.
- **The right left case** n's parent is the right child of its parent and n is the left child of its parent.

4. Step 4 checks if either the **left right** case or the **right left** case applies to the current situation.
  - If we find the **left right case**, we left rotate n's parent and go to step 5 while setting n to n's parent. (This transforms the **left right** case into the **left left** case)
  - If we find the **right left case**, we right rotate n's parent and go to step 5 while setting n to n's parent. (This transforms the **right left** case into the **right right** case)
  - If we find neither of these two we proceed to step 5.

n's parent is now red, while n's grandparent is black.

5. We swap the colors of n's parent and grandparent.
  - We either right rotate n's grandparent in case of the **left left** case
  - Or we left rotate n's grandparent in case of the **right right** case

Reaching the end we have successfully made the tree valid again.
-->

## 插入元素

我們建立了一個新的節點準備插入到樹中. 新的節點顏色一定是紅色.
我們使用標準的二元搜尋樹來插入這個節點. 不過現在的樹可能不是個合格的紅黑樹.
現在我們開始一步一步來調整成合格的紅黑樹. 我們把這個插入的節點稱作節點 n.

第一步. 我們檢查 n 是否是根節點, 如果是了話, 我們將它的顏色改成黑色, 然後就完成了. 如果不是, 那我們進行步驟二.

現在我們知道節點 n 至少有一個父節點 m, 因為它不是根節點.

步驟二. 檢查 n 的父節點 m 是否是黑色, 如果是了話那就完成了. 如果不是那進行步驟三.

現在我們知道節點 n 的父節點 m 也不是根節點, 因為它的父節點是紅色. 所以 n 有一個祖父節點 o 和一個叔節點 p, 因為在二元搜尋樹中每個父節點都有兩個子節點. 這個叔節點有可能是空節點.

步驟三. 檢查節點 n 的叔節點 p 是否為紅色. 如果不是那進行步驟四. 如果是紅色了話我們將此叔節點 p 和 n 的父節點 m 設為黑色, 並把 n 的祖父節點 o 設為紅色. 然後對 n 的祖父節點 o 回到步驟 1 的操作.

有四種情形:

- **左左**: n 的父節點 m 是左子節點, 而 n 也是左子節點.
- **左右**: n 的父節點 m 是左子節點, 而 n 也是右子節點.
- **右右**: n 的父節點 m 是右子節點, 而 n 也是右子節點.
- **右左**: n 的父節點 m 是右子節點, 而 n 也是左子節點.

步驟四. 檢查是 **左右** 還是 **右左** .

- 如果是 **左右**, 我們將 n 的父節點左旋轉並且在 n 與其父節點 m 做交換並進行步驟五. (這將會把 **左右** 的狀況變成 **左左**, 並且 n 變成 m 的父節點)
- 如果是 **右左**, 我們將 n 的父節點右旋轉並且在 n 與其父節點 m 做交換並進行步驟五. (這將會把 **右左** 的狀況變成 **右右**, 並且 n 變成 m 的父節點)
- 如果不是上面兩個那進行步驟五.

此時 n 和 m 都是紅色, 而 o 是黑色.

步驟五. 依照情形對 o 執行旋轉.

- 如果是 **左左** 狀況那我們執行右旋轉.
- 如果是 **右右** 狀況那我們執行左旋轉.

旋轉結束後, n 為 o 和 m 的父節點, 將 n 換成黑色, 並且將 o 換成紅色.

全部執行完畢我們就重新獲得合格的紅黑樹.

<!--
# Deletion

Deletion is a little more complicated than insertion. In the following we call del the node to be deleted.
First we try and find the node to be deleted using find()
we send the result of find to del.
We now go through the following steps in order to delete the node del.

First we do a few checks:
- Is del the rootNode if so set the root to a nullLeaf and we are done.
- If del has 2 nullLeaf children and is red. We check if del is either a left or a right child and set del's parent left or right to a nullLeaf.
- If del has two non nullLeaf children we look for the maximum value in del's left subtree. We set del's value to this maximum value and continue to instead delete this maximum node. Which we will now call del.

Because of these checks we now know that del has at most 1 non nullLeaf child. It has either two nullLeaf children or one nullLeaf and one regular red child. (The child is red otherwise the black-depth wouldn't be the same for every leaf)

We now call the non nullLeaf child of del, child. If del has two nullLeaf children child will be a nullLeaf. This means child can either be a nullLeaf or a red node.

We now have three options

- If del is red its child is a nullLeaf and we can just delete it as it doesn't change the black-depth of the tree. We are done

- If child is red we recolor it black and child's parent to del's parent. del is now deleted and we are done.

- Both del and child are black we go through

If both del and child are black we introduce a new variable sibling. Which is del's sibling. We also replace del with child and recolor it doubleBlack. del is now deleted and child and sibling are siblings.

We now have to go through a lot of steps in order to remove this doubleBlack color. Which are hard to explain in text without just writing the full code in text. This is due the many possible combination of nodes and colors. The code is commented, but if you don't quite understand please leave me a message. Also part of the deletion is described by the final link in the Also See section.
-->

## 刪除

刪除比插入還要複雜一點. 基本上還是依照二元搜尋樹的刪除方式, 但是紅黑狀況需要調整. 在接下來的敘述中我們把要刪除的節點稱為 del.

首先我們用 `find()` 找出要刪除的節點.
我們設定 del 是找到的節點.
依照以下步驟來刪除 del.

讓我們先做一些檢查:

- del 是根節點嗎? 如果是了話那將根節點設為空節點, 就結束了.
- 如果 del 有兩個空子節點, 並且 del 是紅色, 那直接將 del 設為空節點.
- 如果 del 有兩個非空子節點, 先搜尋 del 左子樹中的最大節點值, 然後將 del 的值設定為此最大值, 並繼續刪除這個節點.

經過這些檢查我們知道 del 現在最多有一個非空子節點. del 不是有兩個空子節點, 就是有一個空子節點跟一個紅子節點. (這個子節點如果是黑色那"黑色深度"條件就不合了)

我們現在將 del 的非空子節點稱為 child. 如果 del 有兩個空子節點了話 child 就是個空節點. 這表示 child 要不是空節點, 就是紅色節點.

現在我們有三個選項:

- 如果 del 是紅色節點而 child 是空節點, 就直接刪除, 因為並不影響 "黑色深度", 然後就完成了.

- 如果 child 是紅色, 那我們將 child 設為黑色, 並且將 child 取代 del 位置. 這樣 del 就安全刪除了.

- 如果 del 和 child 都是黑色節點, 我們將 child 取代 del 的位置並且把 child 設為 "雙黑色".

接下來我需要執行一些步驟來處理 "雙黑色" 的節點. 這用文字很難解釋, 因為牽扯到很多不同的節點顏色的排列組合. 程式碼內有註解, 不過如果你還是無法理解, 請留言給我. 有部分的內容在下面的相關閱讀內也有解說.

<!--
## Also see
-->

## 相關閱讀

* [Wikipedia](https://en.wikipedia.org/wiki/Red–black_tree)
* [GeeksforGeeks - introduction](http://www.geeksforgeeks.org/red-black-tree-set-1-introduction-2/)
* [GeeksforGeeks - insertion](http://www.geeksforgeeks.org/red-black-tree-set-2-insert/)
* [GeeksforGeeks - deletion](http://www.geeksforgeeks.org/red-black-tree-set-3-delete-2/)

<!--
Important to note is that GeeksforGeeks doesn't mention a few deletion cases that do occur. The code however does implement these.
-->

這邊要注意到 GeeksforGeeks 並沒有提到一些特別的刪除情況, 不過程式碼內有實作.

*Written for Swift Algorithm Club by Jaap Wijnen. Updated from Ashwin Raghuraman's contribution.*
