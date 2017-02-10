# Union-Find (並查集)

<!--
Union-Find is a data structure that can keep track of a set of elements partitioned into a number of disjoint (non-overlapping) subsets. It is also known as disjoint-set data structure.

What do we mean by this? For example, the Union-Find data structure could be keeping track of the following sets:
-->

一種可以追蹤將某個集合分割成好幾個不相交 (無交集) 的子集合.

這是什麼意思? 舉例來說, 並查集可以持續追蹤以下的集合:

	[ a, b, f, k ]
	[ e ]
	[ g, d, c ]
	[ i, j ]

<!--
These sets are disjoint because they have no members in common.

Union-Find supports three basic operations:

1. **Find(A)**: Determine which subset an element **A** is in. For example, `find(d)` would return the subset `[ g, d, c ]`.

2. **Union(A, B)**: Join two subsets that contain **A** and **B** into a single subset. For example, `union(d, j)` would combine `[ g, d, c ]` and `[ i, j ]` into the larger set `[ g, d, c, i, j ]`.

3. **AddSet(A)**: Add a new subset containing just that element **A**. For example, `addSet(h)` would add a new set `[ h ]`.

The most common application of this data structure is keeping track of the connected components of an undirected [graph](../Graph/). It is also used for implementing an efficient version of Kruskal's algorithm to find the minimum spanning tree of a graph.
-->

這些集合是互斥的, 因為他們沒有任何共同的元素.

並查集支援三個基礎的操作:

1. **Find(A)**: 找到哪個集合含有元素 **A**. 例如 `find(d)` 會回傳 `[ g, d, c ]`.
2. **Union(A, B)**: 將含有 **A** 和 **B** 的兩個子集合合併. 例如 `union(d, j)` 會將 `[ g, d, c ]` 和 `[ i, j ]` 合併成 `[ g, d, c, i, j ]`.
3. **AddSet(A)**: 加入一個新的子集合, 內容只有元素 **A**. 例如 `addSet(h)` 會新增一個子集合 `[ h ]`.

一般使用這個資料結構的應用是持續追蹤 [無向圖](../Graph/) 中連結的頂點. 也可以用在 Kruskal 尋找最小生成樹的演算法上.

<!--
## Implementation

Union-Find can be implemented in many ways but we'll look at the most efficient.
-->

## 實作

並查集可以用很多方法實作, 這邊我們討論最有效率的.

```swift
public struct UnionFind<T: Hashable> {
  private var index = [T: Int]()
  private var parent = [Int]()
  private var size = [Int]()
}
```

<!--
Our Union-Find data structure is actually a forest where each subset is represented by a [tree](../Tree/).

For our purposes we only need to keep track of the parent of each tree node, not the node's children. To do this we use the array `parent` so that `parent[i]` is the index of node `i`'s parent.

Example: If `parent` looks like this,
-->

這個並查集的結構其實是個林, 因為每個子集合都是個 [樹](../Tree/)

針對我們的目的, 我們只需要追蹤每個節點的父節點, 不是子節點. 我們用 `parent` 陣列來達到這點, `parent[i]` 就是節點 `i` 的父節點.

例子: 如果 `parent` 看起來像這樣,

	parent [ 1, 1, 1, 0, 2, 0, 6, 6, 6 ]
	     i   0  1  2  3  4  5  6  7  8

<!--
then the tree structure looks like:
-->


轉成樹結構是像這樣:

	      1              6
	    /   \           / \
	  0       2        7   8
	 / \     /
	3   5   4

<!--
There are two trees in this forest, each of which corresponds to one set of elements. (Note: due to the limitations of ASCII art the trees are shown here as binary trees but that is not necessarily the case.)

We give each subset a unique number to identify it. That number is the index of  the root node of that subset's tree. In the example, node `1` is the root of the first tree and `6` is the root of the second tree.

So in this example we have two subsets, the first with the label `1` and the second with the label `6`. The **Find** operation actually returns the set's label, not its contents.

Note that the `parent[]` of a root node points to itself. So `parent[1] = 1` and `parent[6] = 6`. That's how we can tell something is a root node.
-->

林現在有兩個樹, 每個樹都都是一堆元素的集合. (注意: 因為受於 ASCII 表示法, 這些樹現在是以二元樹的形式呈現, 不過這並非必要的條件)

我們給每個子集樹一個唯一的數值來辨識它. 該數值是每個子集樹根節點的索引. 範例中, 節點 `1` 是第一個樹的根節點, 第二個樹是節點 `6`. 

在此例中我們有兩個子集合, 第一個樹的標籤 `1`, 第二個樹的標籤 `6`. **Find** 操作實際上回傳的是這些樹的標籤, 不是內容.

注意到跟節點的 `parent[]` 是指向自己. 所以 `parent[1] = 1`, `parent[6] = 6`. 這也是我們辨識根節點的一個方法.

<!--
## Add set

Let's look at the implementation of these basic operations, starting with adding a new set.
-->

## 增加集合 (AddSet)

來討論這些基本操作的實作, 從新增集合開始吧.

```swift
public mutating func addSetWith(_ element: T) {
  index[element] = parent.count  // 1
  parent.append(parent.count)    // 2
  size.append(1)                 // 3
}
```

<!--
When you add a new element, this actually adds a new subset containing just that element.

1. We save the index of the new element in the `index` dictionary. That lets us look up the element quickly later on.

2. Then we add that index to the `parent` array to build a new tree for this  set. Here, `parent[i]` is pointing to itself because the tree that represents the new set contains only one node, which of course is the root of that tree.

3. `size[i]` is the count of nodes in the tree whose root is at index `i`. For the new set this is 1 because it only contains the one element. We'll be using the `size` array in the Union operation.
-->

當你洗要新增一個元素, 實際上這動作是增加一個只包含此元素的子集合.

1. 我們將新元素的索引存到 `index` 字典中. 這樣之後查找此元素會比較快.
2. 然後將該索引加入 `parent` 陣列來創建此集合的樹. 在此, `parent[i]` 指向自己, 因為表示這個集合的樹目前只有一個節點, 所以當然是該樹的根節點.
3. `size[i]` 是指在根節點為 `index[i]` 的樹中有多少個節點. 因為這邊是新集合所以只有 1 個元素. 我們將會在 `Union` 操作中使用到 `size`.

<!--
## Find

Often we want to determine whether we already have a set that contains a given element. That's what the **Find** operation does. In our `UnionFind` data structure it is called `setOf()`:
-->

## 查找 (Find)

通常, 我們要確定是否已經有個包含給定元素的集合. 這就是 **Find** 操作. 在我們的 `並查集` 結構中, 此方法稱為 `setOf()`:

```swift
public mutating func setOf(_ element: T) -> Int? {
  if let indexOfElement = index[element] {
    return setByIndex(indexOfElement)
  } else {
    return nil
  }
}
```

<!--
This looks up the element's index in the `index` dictionary and then uses a helper method to find the set that this element belongs to:
-->

在 `index` 字典中查詢該元素的索引, 然後使用幫助方法來找到該元素所屬的集合:

```swift
private mutating func setByIndex(_ index: Int) -> Int {
  if parent[index] == index {  // 1
    return index
  } else {
    parent[index] = setByIndex(parent[index])  // 2
    return parent[index]       // 3
  }
}
```

<!--
Because we're dealing with a tree structure, this is a recursive method.

Recall that each set is represented by a tree and that the index of the root node serves as the number that identifies the set. We're going to find the root node of the tree that the element we're searching for belongs to, and return its index.

1. First, we check if the given index represents a root node (i.e. a node whose `parent` points back to the node itself). If so, we're done.

2. Otherwise we recursively call this method on the parent of the current node. And then we do a **very important thing**: we overwrite the parent of the current node with the index of root node, in effect reconnecting the node directly to the root of the tree. The next time we call this method, it will execute faster because the path to the root of the tree is now much shorter. Without that optimization, this method's complexity is **O(n)** but now in combination with the size optimization (covered in the Union section) it is almost **O(1)**.

3. We return the index of the root node as the result.

Here's illustration of what I mean. Let's say the tree looks like this:
-->

回想一下, 每個集合是用樹狀結構來表示, 然後該樹根節點的索引當作該集合的辨識標籤. 我們要找到搜尋元素所屬的樹的根節點, 然後回傳該根節點的索引.

1. 首先, 我們檢查給定的索引是否是個根節點 (即, 某個節點的 `parent` 指向自己). 如果是了話, 我們就完成了.
2. 如果第一步檢查失敗, 我們會遞迴呼叫此方法並帶入搜尋節點的父節點當參數. 然後我們會做一件 **非常重要的事**: 我們用根節點的索引覆寫搜尋節點的 `parent` 索引值, 直接把該節點和其所屬樹的根節點連結. 下一次我們在呼叫此方法時, 執行效率會更快, 因為該搜尋節點到其所屬樹的根節點路徑變短了. 如果沒有這個優化, 此方法的時間複雜度是 **O(n)**, 但是現在再跟 size 優化 (詳見 `Union` ) 結合起來, 這個操作會變 **O(1)** 時間.
3. 最後回傳搜尋節點所屬樹的根節點索引.

假設樹狀結構是這樣:

![BeforeFind](Images/BeforeFind.png)

<!--
We call `setOf(4)`. To find the root node we have to first go to node `2` and then to node `7`. (The indexes of the elements are marked in red.)

During the call to `setOf(4)`, the tree is reorganized to look like this:
-->

呼叫 `setOf(4)` 時. 為了找到根節點我們先走訪到節點 `2`, 然後再往上走訪到 `7`. (元素的索引用紅色顯示)

在呼叫完 `setOf(4)` 後, 樹狀結構會變得像這樣:

![AfterFind](Images/AfterFind.png)

<!--
Now if we need to call `setOf(4)` again, we no longer have to go through node `2` to get to the root. So as you use the Union-Find data structure, it optimizes itself. Pretty cool!

There is also a helper method to check that two elements are in the same set:
-->

如果我們再次呼叫 `setOf(4)`, 我們將不需要再走訪 `2`, 可以直接找到根節點. 所以當你使用並查集結構時, 它本身會優化自己. 挺酷的!

這是一個檢查兩個元素是否在同一個集合的幫助方法.

```swift
public mutating func inSameSet(_ firstElement: T, and secondElement: T) -> Bool {
  if let firstSet = setOf(firstElement), let secondSet = setOf(secondElement) {
    return firstSet == secondSet
  } else {
    return false
  }
}
```

<!--
Since this calls `setOf()` it also optimizes the tree.
-->

因為此方法會呼叫 `setOf()`, 所以此方法也會對樹做優化.


<!--
## Union

The final operation is **Union**, which combines two sets into one larger set.
-->

## 聯集 (Union)

最後的操作是 **聯集**, 會把兩個集合結合成一個大集合.

```swift
public mutating func unionSetsContaining(_ firstElement: T, and secondElement: T) {
  if let firstSet = setOf(firstElement), let secondSet = setOf(secondElement) {  // 1
    if firstSet != secondSet {               // 2
      if size[firstSet] < size[secondSet] {  // 3
        parent[firstSet] = secondSet         // 4
        size[secondSet] += size[firstSet]    // 5
      } else {
        parent[secondSet] = firstSet
        size[firstSet] += size[secondSet]
      }
    }
  }
}
```

<!--
Here is how it works:

1. We find the sets that each element belongs to. Remember that this gives us two integers: the indices of the root nodes in the `parent` array.

2. Check that the sets are not equal because if they are it makes no sense to union them.

3. This is where the size optimization comes in. We want to keep the trees as shallow as possible so we always attach the smaller tree to the root of the larger tree. To determine which is the smaller tree we compare trees by their sizes.

4. Here we attach the smaller tree to the root of the larger tree.

5. Update the size of larger tree because it just had a bunch of nodes added to it.

An illustration may help to better understand this. Let's say we have these two sets, each with its own tree:
-->

步驟解釋:

1. 我們找到兩個元素所屬的集合. 記得這邊會回傳給我們兩個整數: 兩個根節點在 `parent` 裡的索引.
2. 檢查集合是否相同, 因為如果他們相同根本不需要做合併.
3. 這步就是優化的地方. 我們希望保持樹的高度越少越好, 所以我們總是把比較小的樹連結到較大的樹上. 我們用 `size` 來比較兩個樹的大小.
4. 這步我們把小樹連結到大樹的根節點.
5. 更新大樹的 `size`, 因為它增加了一堆節點.

用圖來解釋會比較好理解. 假設我們有以下這兩個集合, 每個都有自己的樹:

![BeforeUnion](Images/BeforeUnion.png)

<!--
Now we call `unionSetsContaining(4, and: 3)`. The smaller tree is attached to the larger one:
-->

現在我們呼叫 `unionSetsContaining(4, and: 3)`. 現在小樹要和大數連結:

![AfterUnion](Images/AfterUnion.png)

<!--
Note that, because we call `setOf()` at the start of the method, the larger tree was also optimized in the process -- node `3` now hangs directly off the root.

Union with optimizations also takes almost **O(1)** time.
-->

注意到因為我們在此方法一開始用 `setOf()` 來找集合, 所以大小樹都會被優化 -- 節點 `3` 直接和根節點連接.

聯集操作也是差不多佔用 **O(1)** 時間.

<!--
## See also

See the playground for more examples of how to use this handy data structure.
-->

## 相關閱讀

playground 中有更多範例及實際操作.

[Union-Find at Wikipedia](https://en.wikipedia.org/wiki/Disjoint-set_data_structure)

*Written for Swift Algorithm Club by [Artur Antonov](https://github.com/goingreen)*
