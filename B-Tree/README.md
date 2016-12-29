# B-Tree
<!--
A B-Tree is a self-balancing search tree, in which nodes can have more than two children.
-->

B-Tree 是一種自我平衡搜尋樹, 所有的節點可以有超過兩個子節點.

<!--
### Properties

A B-Tree of order *n* satisfies the following properties:
 - Every node has at most *2n* keys.
 - Every node (except root) has at least *n* keys.
 - Every non-leaf node with *k* keys has *k+1* children.
 - The keys in all nodes are sorted in increasing order. 
 - The subtree between two keys *k* and *l* of a non-leaf node contains all the keys between *k* and *l*.
 - All leaves appear at the same level.

A second order B-Tree with keys from 1 to 20 looks like this:
-->

### 屬性

一個 order 為 *n* 的 B-Tree 須符合下列條件:

 - 每個節點最多擁有 *2n* 個鍵值.
 - 節點除了根以外至少都要擁有 *n* 個鍵值.
 - 每個擁有 *k* 個鍵值的非葉節點會有 *k+1* 個子節點.
 - 在每個節點裡了鍵值皆為遞增排序.
 - 在非葉節點鍵值 *k* 及 *l* 之間的子節點, 其所有鍵值皆在 *k* 和 *l* 之間.
 - 所有葉節點均在同一層.

一個 order 為 2 且擁有 1 到 20 個鍵值 的 B-Tree 像下面這樣:

![A B-Tree with 20 keys.](Images/BTree20.png)

<!--
### The representation of a B-Tree node in code
-->

### B-Tree 中的節點程式碼

```swift
class BTreeNode<Key: Comparable, Value> {
  unowned var owner: BTree<Key, Value>
  
  fileprivate var keys = [Key]()
  var children: [BTreeNode]?
  
  ...
}
```

<!--
The main parts of a node are two arrays:
 - An array containing the keys
 - An array containing the children
-->

節點的主要部分是兩個陣列:
 - 包含鍵值的陣列
 - 包含子節點的陣列

![Node.](Images/Node.png)

<!--
Nodes also have a reference to the tree they belong to.  
This is necessary because nodes have to know the order of the tree.
-->

節點同時也必須有所屬 B-Tree 的指標物件.因為節點需要知道 B-Tree 的 order.

<!--
*Note: The array containing the children is an Optional, because leaf nodes don't have children.*
-->
*注意: children 變數之所以為 Optional 是因為葉節點沒有子節點.*

<!--
## Searching

1. Searching for a key `k` begins at the root node.
2. We perform a linear search on the keys of the node, until we find a key `l` that is not less than `k`,  
   or reach the end of the array.
3. If `k == l` then we have found the key.
4. If `k < l`: 
    - If the node we are on is not a leaf, then we go to the left child of `l`, and perform the steps 3 - 5 again.
    - If we are on a leaf, then `k` is not in the tree.
5. If we have reached the end of the array:
    - If we are on a non-leaf node, then we go to the last child of the node, and perform the steps 3 - 5 again.
    - If we are on a leaf, then `k` is not in the tree.

-->

## 搜尋

1. 從根節點開始搜尋鍵值 `k` .
2. 對節點中的鍵值用線性搜尋的方式找 `k`, 直到找到不小於 `k` 的鍵值 `l` , 或已經達到鍵值陣列的結尾.
3. 如果 `k == l`, 那我們就找到該鍵值.
4. 如果 `k < l`:
	- 如果我們正在搜尋的節點非葉節點, 那繼續前往 `l` 的左子節點, 然後重複 3 - 5 步驟.
	- 如果我們正在搜尋的節點是葉節點, 那該鍵值 `k` 並不在樹中.
5. 如果我們已經達到陣列的結尾:
	- 如果我們正在搜尋的節點非葉節點, 那繼續前往當前節點的最後一個子節點, 並重複 3 - 5 步驟.
	- 如果我們正在搜尋的節點是葉節點, 那該鍵值 `k` 並不在樹中.

<!--
### The code

`value(for:)` method searches for the given key and if it's in the tree,  
it returns the value associated with it, else it returns `nil`.
-->
### 程式碼

`value(for:)` 方法會針對傳入的參數 key 做搜尋, 並會傳對應該鍵值的值, 若該鍵值不在樹中, 則回傳 `nil`.

<!--
## Insertion

Keys can only be inserted to leaf nodes.

1. Perform a search for the key `k` we want to insert.
2. If we haven't found it and we are on a leaf node, we can insert it.
 - If after the search the key `l` which we are standing on is greater than `k`:  
   We insert `k` to the position before `l`.
 - Else:  
   We insert `k` to the position after `l`.

After insertion we should check if the number of keys in the node is in the correct range.  
If there are more keys in the node than `order*2`, we need to split the node.
-->

## 插入鍵值

只能在葉節點上插入鍵值.

1. 搜尋鍵值 `k`.
2. 如果沒有找到該鍵值並且當前節點是葉節點, 則可執行新增.
	- 如果找到一個鍵值 `l` 大於 `k`: 	
	  將鍵值 `k` 插入 `l` 之前.
 	- Else: 	
 	  將鍵值 `k` 插入 `l` 之後.

插入鍵值後需要檢查該節點的鍵值數是否符合 B-Tree 限制, 如果插入該鍵值後節點的鍵值數超過 `order*2`, 那就需要分割該節點.

<!--
#### Splitting a node

1. Move up the middle key of the node we want to split, to its parent (if it has one).  
2. If it hasn't got a parent(it is the root), then create a new root and insert to it.  
   Also add the old root as the left child of the new root.
3. Split the node into two by moving the keys (and children, if it's a non-leaf) that were after the middle key
   to a new node.  
4. Add the new node as a right child for the key that we have moved up.  

After splitting a node it is possible that the parent node will also contain too many keys, so we need to split it also.  
In the worst case the splitting goes up to the root (in this case the height of the tree increases).

An insertion to a first order tree looks like this:
-->

#### 分割節點

1. 將節點的中間鍵值移到父節點 (如果父節點存在).
2. 如果沒有父節點 (該節點為根節點), 則新增一個根節點並插入該鍵值. 並將原根節點設為新根節點的左子節點.
3. 依據移動到父節點的中間鍵值將該節點一分為二.
4. 對於移動到父節點的中間鍵值, 建立一個新的右節點, 並將新增的鍵值放入該節點.

在分割節點之後是有可能發生父節點的鍵值數也超過了限制, 所以還需要對父節點再做一次分割.
最糟糕的情況是一路分割到根節點 (這種情況下這棵樹的高度就會增加).

一個對於 order 為 1 的 B-Tree 插入一鍵值狀況如下:

![Splitting](Images/InsertionSplit.png)

<!--
### The code

The method `insert(_:for:)` does the insertion.
After it has inserted a key, as the recursion goes up every node checks the number of keys in its child.  
if a node has too many keys, its parent calls the `split(child:atIndex:)` method on it.

The root node is checked by the tree itself.  
If the root has too many nodes after the insertion the tree calls the `splitRoot()` method.
-->

### 程式碼

`insert(_:for:)` 方法實現了上述的規則.

在插入一個鍵值之後, 遞迴的向上檢查每個節點的鍵值, 如果某個節點有太多鍵值, 其父節點會呼叫 `split(child:atIndex:)` 方法來處理.

如果根節點有太多鍵值, 則根節點本身匯呼叫 `splitRoot()` 方法來處理.

<!--
## Removal

Keys can only be removed from leaf nodes.

1. Perform a search for the key `k` we want to remove.
2. If we have found it:
   - If we are on a leaf node:  
     We can remove the key.
   - Else:  
     We overwrite `k` with its inorder predecessor `p`, then we remove `p` from the leaf node.

After a key have been removed from a node we should check that the node has enough keys.
If a node has fewer keys than the order of the tree, then we should move a key to it,  
or merge it with one of its siblings.

-->

## 移除鍵值

只能在葉節點上移除鍵值.

1. 搜尋鍵值 `k`.
2. 如果有找到:
   - 如果當前節點是葉節點:  
     移除該鍵值.
   - 如果非葉節點:  
     將 `k` 的前一個鍵值的子樹中的最後一個葉節點的最大鍵值 `p` 和 `k` 交換, 並將 `p` 在該葉節點中刪除.

在刪除一個鍵值之後, 應該檢查該節點是否還有足夠的鍵值, 如果該節點的鍵值不足, 則需要移動一個鍵值給它, 或者將該節點和其同層節點合併.

<!--
#### Moving a key to the child

If the problematic node has a nearest sibling that has more keys than the order of the tree,  
we should perform this operation on the tree, else we should merge the node with one of its siblings.

Let's say the child we want to fix `c1` is at index `i` in its parent node's children array.

If the child `c2` at index `i-1` has more keys than the order of the tree:  

1. We move the key at index `i-1` from the parent node to the child `c1`'s keys array at index `0`.
2. We move the last key from `c2` to the parent's keys array at index `i-1`.
3. (If `c1` is non-leaf) We move the last child from `c2` to `c1`'s children array at index 0.

Else:  

1. We move the key at index `i` from the parent node to the end of child `c1`'s keys array.
2. We move the first key from `c2` to the parent's keys array at index `i`.
3. (If `c1` isn't a leaf) We move the first child from `c2` to the end of `c1`'s children array. 
-->

#### 移動鍵值給子節點

如果一個節點的鍵值不夠, 而且和該節點同層的其他節點中有節點的鍵值太多, 則使用這個操作來處理, 如果沒有其他問題節點, 則將此節點和其他同層節點做合併.

假設我們要處理的問題節點 `c1` 在他的父節點 children 陣列中是第 `i` 個元素, `c2` 和 `c1` 是同父節點, 並且是父節點 children 陣列中第 `i-1` 個元素.

如果 `c2` 含有超過限制數量的鍵值:  

1. 將父節點中鍵值陣列的第 `i-1` 個鍵值插入 `c1` 鍵值陣列的第 `0` 個位置.
2. 將 `c2` 最後的鍵值移動到父節點鍵值陣列的第 `i-1` 個位置.
3. (如果 `c1` 不是葉節點) 將 `c2` 最後的子節點插入 `c1` 第 `0` 個子節點.

Else:  

1. 將父節點中鍵值陣列的第 `i` 個鍵值插入 `c1` 鍵值陣列最後的位置. 
2. 將 `c2` 的第一個鍵值移動到父節點鍵值陣列的第 `i` 個位置.
3. (如果 `c1` 不是葉節點) 將 `c2` 第一個子節點插入到 `c1` 最後一個子節點.


![Moving Key](Images/MovingKey.png)

<!--
####Merging two nodes

Let's say we want to merge the child `c1` at index `i` in its parent's children array.

If `c1` has a left sibling `c2`:

1. We move the key from the parent at index `i-1` to the end of `c2`'s keys array.
2. We move the keys and the children(if it's a non-leaf) from `c1` to the end of `c2`'s keys and children array.
3. We remove the child at index `i-1` from the parent node.

Else if `c1` only has a right sibling `c2`:

1. We move the key from the parent at index `i` to the beginning of `c2`'s keys array.
2. We move the keys and the children(if it's a non-leaf) from `c1` to the beginning of `c2`'s keys and children array.
3. We remove the child at index `i` from the parent node.

After merging it is possible that now the parent node contains too few keys,  
so in the worst case merging also can go up to the root, in which case the height of the tree decreases.

-->

#### 合併兩個節點

假設要合併 `c1` 是在父節點 children 陣列中第 `i` 個的元素.

如果 `c1` 左邊有一個同層的節點 `c2`:

1. 將父節點第 `i-1` 個鍵值插入到 `c2` 鍵值陣列的最後.
2. 將 `c1` 的鍵值陣列和子節點陣列 (當 `c1` 不是葉節點) 插入到 `c2` 的鍵值陣列和子節點陣列的尾端.
3. 從父節點中移除第 `i-1` 個子節點.

如果 `c1` 只有右邊一個同層節點 `c2`:

1. 將父節點第 `i` 個鍵值插入到 `c2` 鍵值陣列的前端.
2. 將 `c1` 的鍵值陣列和子節點陣列 (當 `c1` 不是葉節點) 插入到 `c2` 的鍵值陣列和子節點陣列的前端.
3. 從父節點中移除第 `i` 個子節點.

在合併結束後有可能父節點的鍵值數會不足, 最糟糕的情況是會一路合併到根節點, 此時樹的高度將會減少.

![Merging Nodes](Images/MergingNodes.png)

<!--
### The code

- `remove(_:)` method removes the given key from the tree. After a key has been deleted,  
  every node checks the number of keys in its child. If a child has less nodes than the order of the tree,
  it calls the `fix(childWithTooFewKeys:atIndex:)` method.  

- `fix(childWithTooFewKeys:atIndex:)` method decides which way to fix the child (by moving a key to it,
  or by merging it), then calls `move(keyAtIndex:to:from:at:)` or 
  `merge(child:atIndex:to:)` method according to its choice.
-->

### 程式碼

- `remove(_:)` 方法將在樹中刪除傳入的鍵值參數. 刪除之後每個節點會檢查自己的鍵值數, 如果鍵值數小於樹的 order, 該節點會呼叫 `fix(childWithTooFewKeys:atIndex:)` 方法

- `fix(childWithTooFewKeys:atIndex:)` 方法決定哪種方法去修正節點 (移動還是合併鍵值), 呼叫 `move(keyAtIndex:to:from:at:)` 方法或 `merge(child:atIndex:to:)` 方法去處理移動或合併.

<!--
## See also
-->
## 相關閱讀

[Wikipedia](https://en.wikipedia.org/wiki/B-tree)  
[GeeksforGeeks](http://www.geeksforgeeks.org/b-tree-set-1-introduction-2/)




*Written for Swift Algorithm Club by Viktor Szilárd Simkó*
