# Breadth-First Search (廣度優先搜尋)

<!--
Breadth-first search (BFS) is an algorithm for traversing or searching [tree](../Tree/) or [graph](../Graph/) data structures. It starts at a source node and explores the immediate neighbor nodes first, before moving to the next level neighbors.

Breadth-first search can be used on both directed and undirected graphs.
-->

廣度優先搜尋 (BFS) 演算法是用來走訪或搜尋 [樹](../Tree/) 或 [圖](../Graph/) 結構. 它先從源頭節點開始先探索最接近自己第一層的鄰節點, 之後再往下一層移動.

廣度優先搜尋適用於有向及無向的圖結構.

<!--
## Animated example

Here's how breadth-first search works on a graph:
-->


## 動畫範例

這裡展示廣度優先搜尋是如何工作的:

![Animated example of a breadth-first search](Images/AnimatedExample.gif)

<!--
When we visit a node, we color it black. We also put its neighbor nodes into a [queue](../Queue/). In the animation the nodes that are enqueued but not visited yet are shown in gray.

Let's follow the animated example. We start with the source node `A` and add it to a queue. In the animation this is shown as node `A` becoming gray.
-->

當我們走訪一個節點的時候, 我們把它塗成黑色. 同時也把它的鄰節點加入一個 [佇列](../Queue/) 中. 在動畫中灰色的節點就是已經進入佇列但是尚未走訪時的狀態.

讓我們看看範例. 首先我們初始節點是 `A`, 所以先把 `A` 放到佇列中. 這時候 `A` 是灰色的.

```swift
queue.enqueue(A)
```

<!--
The queue is now `[ A ]`. The idea is that, as long as there are nodes in the queue, we visit the node that's at the front of the queue, and enqueue its immediate neighbor nodes if they have not been visited yet.

To start traversing the graph, we pull the first node off the queue, `A`, and color it black. Then we enqueue its two neighbor nodes `B` and `C`. This colors them gray.
-->

現在佇列是 `[ A ]`. 想法是這樣, 只要佇列裡有節點, 我們走訪佇列最前端的節點, 同時將該節點尚未走訪的麟節點加入到佇列中.

開始走訪圖, 我們先把佇列中的第一個節點取出, `A`, 然後將他塗成黑色. 然後將它的兩個鄰節點 `B` 和 `C` 加入到佇列中. 並將它們塗成灰色.

```swift
queue.dequeue()   // A
queue.enqueue(B)
queue.enqueue(C)
```
<!--
The queue is now `[ B, C ]`. We dequeue `B`, and enqueue `B`'s neighbor nodes `D` and `E`.
-->

現在佇列中是 `[ B, C ]`. 然後我們取出 `B` 並把它的鄰節點 `D` 和 `E` 加入到佇列中.


```swift
queue.dequeue()   // B
queue.enqueue(D)
queue.enqueue(E)
```

<!--
The queue is now `[ C, D, E ]`. Dequeue `C`, and enqueue `C`'s neighbor nodes `F` and `G`.
-->

佇列現在是 `[ C, D, E ]`. 取出 `C`, 然後加入 `F` 和  `G`.

```swift
queue.dequeue()   // C
queue.enqueue(F)
queue.enqueue(G)
```

<!--
The queue is now `[ D, E, F, G ]`. Dequeue `D`, which has no neighbor nodes.
-->

佇列現在是 `[ D, E, F, G]`. 取出 `D`, 因為 `D` 沒有鄰節點, 所以沒有節點要插入佇列.

```swift
queue.dequeue()   // D
```

<!--
The queue is now `[ E, F, G ]`. Dequeue `E` and enqueue its single neighbor node `H`. Note that `B` is also a neighbor for `E` but we've already visited `B`, so we're not adding it to the queue again.
-->

現在佇列是 `[ E, F, G ]`. 取出 `E` 然後加入它唯一的鄰節點 `H`. 注意到 `B` 同時也是 `E` 的麟節點, 但是 `B` 已經被走訪過了, 所以就不加入佇列中.

```swift
queue.dequeue()   // E
queue.enqueue(H)
```

<!--
The queue is now `[ F, G, H ]`. Dequeue `F`, which has no unvisited neighbor nodes.
-->

佇列現在是 `[ F, G, H ]`. 取出 `F`, 它沒有未走訪鄰節點.

```swift
queue.dequeue()   // F
```

<!--
The queue is now `[ G, H ]`. Dequeue `G`, which has no unvisited neighbor nodes.
-->

佇列現在是 `[ G, H ]`. 取出 `G`, 它沒有未走訪鄰節點.


```swift
queue.dequeue()   // G
```

<!--
The queue is now `[ H ]`. Dequeue `H`, which has no unvisited neighbor nodes.
-->

佇列現在是 `[ G, H ]`. 取出 `G`, 它沒有未走訪鄰節點.

```swift
queue.dequeue()   // H
```

<!--
The queue is now empty, meaning that all nodes have been explored. The order in which the nodes were explored is `A`, `B`, `C`, `D`, `E`, `F`, `G`, `H`.

We can show this as a tree:
-->

佇列現在空了, 表示所有節點都被走訪過了. 這個走訪的順序是: `A`, `B`, `C`, `D`, `E`, `F`, `G`, `H`.

我們也能用樹狀結構來表示:

![The BFS tree](Images/TraversalTree.png)

<!--
The parent of a node is the one that "discovered" that node. The root of the tree is the node you started the breadth-first search from.

For an unweighted graph, this tree defines a shortest path from the starting node to every other node in the tree. So breadth-first search is one way to find the shortest path between two nodes in a graph.
-->

每個節點的父節點都是 "發現" 它們的節點. 根節點是開始執行廣度優先搜尋的初始節點.

對於無權重的圖來說, 這個樹定義了兩節點之間的最短路徑. 所以廣度優先搜尋是在圖中找到兩點之間最短路徑的方法之一.

<!--
## The code

Simple implementation of breadth-first search using a queue:
-->

## 程式碼:

簡單的廣度優先搜尋: (使用佇列)

```swift
func breadthFirstSearch(_ graph: Graph, source: Node) -> [String] {
  var queue = Queue<Node>()
  queue.enqueue(source)

  var nodesExplored = [source.label]
  source.visited = true

  while let node = queue.dequeue() {
    for edge in node.neighbors {
      let neighborNode = edge.neighbor
      if !neighborNode.visited {
        queue.enqueue(neighborNode)
        neighborNode.visited = true
        nodesExplored.append(neighborNode.label)
      }
    }
  }

  return nodesExplored
}
```

<!--
While there are nodes in the queue, we visit the first one and then enqueue its immediate neighbors if they haven't been visited yet.

Put this code in a playground and test it like so:
-->

當佇列中還有節點, 我們走訪第一個元素並同時將它的第一層未走訪的鄰節點加入到佇列中.

在 playground 中試試看這段程式:


```swift
let graph = Graph()

let nodeA = graph.addNode("a")
let nodeB = graph.addNode("b")
let nodeC = graph.addNode("c")
let nodeD = graph.addNode("d")
let nodeE = graph.addNode("e")
let nodeF = graph.addNode("f")
let nodeG = graph.addNode("g")
let nodeH = graph.addNode("h")

graph.addEdge(nodeA, neighbor: nodeB)
graph.addEdge(nodeA, neighbor: nodeC)
graph.addEdge(nodeB, neighbor: nodeD)
graph.addEdge(nodeB, neighbor: nodeE)
graph.addEdge(nodeC, neighbor: nodeF)
graph.addEdge(nodeC, neighbor: nodeG)
graph.addEdge(nodeE, neighbor: nodeH)
graph.addEdge(nodeE, neighbor: nodeF)
graph.addEdge(nodeF, neighbor: nodeG)

let nodesExplored = breadthFirstSearch(graph, source: nodeA)
print(nodesExplored)
```

<!--
This will output: `["a", "b", "c", "d", "e", "f", "g", "h"]`
   
## What is BFS good for?

Breadth-first search can be used to solve many problems. A small selection:

* Computing the [shortest path](../Shortest Path/) between a source node and each of the other nodes (only for unweighted graphs).
* Calculating the [minimum spanning tree](../Minimum Spanning Tree (Unweighted)/) on an unweighted graph.
-->

這將會印出: `["a", "b", "c", "d", "e", "f", "g", "h"]`

## BFS有什麼好?

廣度優先搜尋可以解決很多問題. 列舉一小部分:

* 計算兩個節點之間的 [最短路徑](../Shortest Path/) (只能是無權重圖).
* 在無權重圖中計算 [最小生成樹](../Minimum Spanning Tree (Unweighted)/).


*Written by [Chris Pilcher](https://github.com/chris-pilcher) and Matthijs Hollemans*
