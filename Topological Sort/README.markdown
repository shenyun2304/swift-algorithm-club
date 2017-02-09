# Topological Sort (拓樸排序)

<!--
Topological sort is an algorithm that orders a directed graph such that for each directed edge *u→v*, vertex *u* comes before vertex *v*.

In other words, a topological sort places the vertices of a [directed acyclic graph](../Graph/) on a line so that all directed edges go from left to right. 

Consider the graph in the following example:
-->

可以將 [有向無循環圖](../Graph) 所有的有向邊轉換成一條線上由左至右的邊.

針對這個圖:

![Example](Images/Graph.png)

<!--
This graph has two possible topological sorts:
-->

這個圖有兩個可能的拓樸排序:

![Example](Images/TopologicalSort.png)

<!--
The topological orderings are **S, V, W, T, X** and **S, W, V, T, X**. Notice how the arrows all go from left to right.

The following is not a valid topological sort for this graph, since **X** and **T** cannot happen before **V**:
-->

排序後的順序是 **S, V, W, T, X** 和 **S, W, V, T, X**. 注意箭頭方向.

下面這個不是此圖的拓樸排序, 因為 **X** 和 **T** 不可能比 **V** 還早走訪.

![Example](Images/InvalidSort.png)

<!--
## Where is this used?

Let's consider that you want to learn all the algorithms and data structures from the Swift Algorithm Club. This might seem daunting at first but we can use topological sort to get things organized.

Since you're learning about topological sort, let's take this topic as an example. What else do you need to learn first before you can fully understand topological sort? Well, topological sort uses [depth-first search](../Depth-First Search/) as well as a [stack](../Stack/). But before you can learn about the depth-first search algorithm, you need to know what a [graph](../Graph/) is, and it helps to know what a [tree](../Tree/) is. In turn, graphs and trees use the idea of linking objects together, so you may need to read up on that first. And so on...

If we were to represent these objectives in the form of a graph it would look as follows:
-->

## 用在哪些地方?

假設你想學會所有 Swift 演算法社團中的資料結構和演算法.
一開始看起來挺恐怖的, 但我們可以用拓樸排序來整理事情.

因為你正在學拓樸排序, 讓我們用這個專題當例子. 在完全理解拓樸排序之前你需要先學會什麼? 嗯, 拓樸排序會用到 [深度優先搜尋](../Depth-First Search/) 和 [堆疊](../Stack/). 但是在你學深度優先搜尋之前, 你需要知道什麼是 [圖](../Graph/), 而這件事有助於讓你知道 [樹](../Tree/) 是什麼. 反過來說, 圖和樹是用物件鏈結的概念, 所以你可能需要先閱讀相關知識. 以此類推...

如果我們將這些目標用途來表示會像這樣:

![Example](Images/Algorithms.png)

<!--
If we consider each algorithm to be a vertex in the graph you can clearly see the dependencies between them. To learn something you might have to know something else first. This is exactly what topological sort is used for -- it will sort things out so that you know what to do first.
-->

如果把每種演算法當作一個圖中的頂點, 可以很清楚的看到演算法之間的關聯. 要學哪些和需要哪些基礎知識. 這正是拓樸排序的目的 -- 它會把事情排序讓你知道要先做哪些.

<!--
## How does it work?

**Step 1: Find all vertices that have in-degree of 0**

The *in-degree* of a vertex is the number of edges pointing at that vertex. Vertices with no incoming edges have an in-degree of 0. These vertices are the starting points for the topological sort.

In the context of the previous example, these starting vertices represent algorithms and data structures that don't have any prerequisites; you don't need to learn anything else first, hence the sort starts with them.

**Step 2: Traverse the graph with depth-first search**

Depth-first search is an algorithm that starts traversing the graph from a certain vertex and explores as far as possible along each branch before backtracking. To find out more about depth-first search, please take a look at the [detailed explanation](../Depth-First%20Search/).

We perform a depth-first search on each vertex with in-degree 0. This tells us which vertices are connected to each of these starting vertices.

**Step 3: Remember all visited vertices**

As we perform the depth-first search, we maintain a list of all the vertices that have been visited. This is to avoid visiting the same vertex twice.

**Step 4: Put it all together**

The last step of the sort is to combine the results of the different depth-first searches and put the vertices in a sorted list.
-->

## 如何運作?

**步驟 1: 找到所有內分支度為 0 的頂點**

一個頂點的 *內分支度* 是指以該頂點為終點 (箭頭指向該頂點) 的邊數. 沒有任何箭頭指向的頂點內分支度為 0. 這些頂點是拓樸排序的起點.

在上面的例子中, 這些起始頂點代表著那些沒有先決條件的演算法; 你不需要先學習其他的演算法就能直接學習該演算法, 因此排序從這些頂點開始.

**步驟 2: 用深度優先搜尋走訪圖**

深度優先搜尋從某個頂點開始先探索該頂點可以到達得最遠距離後才進行下一個頂點的走訪, 想知道更多深度優先搜尋的詳細, 請參考 [更深入的解釋](../Depth-First Search/).

我們在內分支度為 0 的頂點執行深度優先搜尋. 這動作讓我們知道和起始頂點連結的有哪些頂點.

**步驟 3: 記住所有已經走訪過的頂點**

使用深度優先搜尋, 我們可以把所有已經走訪過的頂點放到列表中. 避免重複走訪同樣的頂點.

**步驟 4: 全部放一起**

最後把每次深度優先搜尋走訪的結果和起來, 然後把頂點放到有序列表.

<!--
## Example

Consider the following graph:
-->

## 範例

以此圖為例:

![Graph Example](Images/Example.png)

<!--
**Step 1:** The vertices with 0 in-degree are: **3, 7, 5**. These are our starting vertices.

**Step 2:** Perform depth-first search for each starting vertex, without remembering vertices that have already been visited:
-->

**步驟 1:** 內分支度為 0 的頂點有: **3, 7, 5**. 這幾個是起始頂點.

**步驟 2:** 對每個起始頂點執行深度優先搜尋, 在此步驟中先不管已經走訪過的節點: 

```
Vertex 3: 3, 10, 8, 9
Vertex 7: 7, 11, 2, 8, 9
Vertex 5: 5, 11, 2, 9, 10
```

<!--
**Step 3:** Filter out the vertices already visited in each previous search:
-->
**步驟 3:** 把已經走訪過的節點過濾掉:

```
Vertex 3: 3, 10, 8, 9
Vertex 7: 7, 11, 2
Vertex 5: 5
```

<!--
**Step 4:** Combine the results of these three depth-first searches. The final sorted order is **5, 7, 11, 2, 3, 10, 8, 9**. (Important: we need to add the results of each subsequent search to the *front* of the sorted list.)

The result of the topological sort looks like this:
-->

**步驟 4:** 將這些結果組合起來. 最終順序是 **5, 7, 11, 2, 3, 10, 8, 9**. (重點: 在合併的時候, 要把每個走訪的起始頂點放在最前面)

拓樸排序後結果像這樣:

![Result of the sort](Images/GraphResult.png)

<!--
> **Note:** This is not the only possible topological sort for this graph. For example, other valid solutions are **3, 7, 5, 10, 8, 11, 9, 2** and **3, 7, 5, 8, 11, 2, 9, 10**. Any order where all the arrows are going from left to right will do. 
-->

> **注意:** 這並不是對這個圖唯一的拓樸排序. 舉例來說, 另外合格的排序有 **3, 7, 5, 10, 8, 11, 9, 2** 和 **3, 7, 5, 8, 11, 2, 9, 10**. 每個排序箭頭都是由左向右.

<!--
## The code

Here is how you could implement topological sort in Swift (see also [TopologicalSort1.swift](TopologicalSort1.swift)):
-->

## 程式碼

在 Swift 中實作拓樸排序 (也可參考 [TopologicalSort1.swift](TopologicalSort1.swift)):


```swift
extension Graph {
  public func topologicalSort() -> [Node] {
    // 1
    let startNodes = calculateInDegreeOfNodes().filter({ _, indegree in
      return indegree == 0
    }).map({ node, indegree in
      return node
    })
    
    // 2
    var visited = [Node : Bool]()
    for (node, _) in adjacencyLists {
      visited[node] = false
    }
    
    // 3
    var result = [Node]()
    for startNode in startNodes {
      result = depthFirstSearch(startNode, visited: &visited) + result
    }

    // 4    
    return result
  }
}
```

<!--
Some remarks:

1. Find the in-degree of each vertex and put all the vertices with in-degree 0 in the `startNodes` array. In this graph implementation, vertices are called "nodes". Both terms are used interchangeably by people who write graph code.

2. The `visited` array keeps track of whether we've already seen a vertex during the depth-first search. Initially, we set all elements to `false`.

3. For each of the vertices in the `startNodes` array, perform a depth-first search. This returns an array of sorted `Node` objects. We prepend that array to our own `result` array.

4. The `result` array contains all the vertices in topologically sorted order.

> **Note:** For a slightly different implementation of topological sort using depth-first search, see [TopologicalSort3.swift](TopologicalSort3.swift). This uses a stack and does not require you to find all vertices with in-degree 0 first.
-->

備註:

1. 找到內分支度為 0 的所有頂點並存在 `startNodes` 陣列中. 在這個實作中, 頂點稱為 "節點". 這兩個用詞在圖狀結構中是可互換的.
2. `visited` 陣列追蹤我們已經在深度優先搜尋中走訪過的頂點. 初始化時, 所有的元素值都是 `false`.
3. 在 `startNodes` 中的每個頂點, 執行深度優先搜尋. 這會回傳一個 `Node` 物件的有序陣列. 將結果放到 `result` 陣列的前端.
4. `result` 陣列為拓樸排序後的結果

> **注意:** 另一個不使用深度優先搜尋來走訪的方法, 請見 [TopologicalSort3.swift](TopologicalSort3.swift). 此方法使用堆疊, 而不需要先找到所有內分支度為 0 的頂點.

<!--
## Kahn's algorithm

Even though depth-first search is the typical way to perform a topological sort, there is another algorithm that also does the job. 

1. Find out what the in-degree is of every vertex.
2. Put all the vertices that have no predecessors in a new array called `leaders`. These vertices have in-degree 0 and therefore do not depend on any other vertices.
3. Go through this list of leaders and remove them one-by-one from the graph. We don't actually modify the graph, we just decrement the in-degree of the vertices they point to. That has the same effect.
4. Look at the (former) immediate neighbor vertices of each leader. If any of them now have an in-degree of 0, then they no longer have any predecessors themselves. We'll add those vertices to the `leaders` array too.
5. This repeats until there are no more vertices left to look at. At this point, the `leaders` array contains all the vertices in sorted order.

This is an **O(n + m)** algorithm where **n** is the number of vertices and **m** is the number of edges. You can see the implementation in [TopologicalSort2.swift](TopologicalSort2.swift).

Source: I first read about this alternative algorithm in the Algorithm Alley column in Dr. Dobb's Magazine from May 1993.
-->

## Kahn 演算法

雖然說深度優先搜尋是拓樸排序中典型的做法, 但還是有其他演算法可以做到一樣的事情.

1. 找到所有頂點的內分支度.
2. 把所有沒有前驅的頂點放到 `leaders` 陣列中. 這些頂點內分支度為 0.
3. 走訪 `leaders` 陣列並從圖中把 `leaders` 陣列中的元素一個接一個移除. 其實並沒有真的去改圖的結構, 只是減少這些頂點指向的那些頂點的內分支度.
4. 檢查 `leaders` 中頂點的鄰頂點. 這些鄰頂點中如果有任何一個內分支度變成 0, 表示這些頂點也沒有前驅頂點了. 然後把這些頂點加入到 `leaders` 陣列中.
5. 這樣的步驟持續重複直到沒有頂點可以檢查. 此時, `leaders` 陣列就會是拓樸排序後的樣子.

這是個 **O(n + m)** 的演算法, **n** 是頂點數量 , **m** 是邊的數量. 你可以在 [TopologicalSort2.swift](TopologicalSort2.swift) 中看到實作詳細.

來源: 我第一次讀到這個演算法是在 1993 年 5 月 Dr. Dobb 雜誌中的 Algorithm Alley 專欄中.

*Written for Swift Algorithm Club by Ali Hafizji and Matthijs Hollemans*
