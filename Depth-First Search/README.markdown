# Depth-First Search (深度優先搜尋)

<!--
Depth-first search (DFS) is an algorithm for traversing or searching [tree](../Tree/) or [graph](../Graph/) data structures. It starts at a source node and explores as far as possible along each branch before backtracking.

Depth-first search can be used on both directed and undirected graphs.
-->

深度優先 (DFS) 是拿來走訪或搜尋 [樹](../Tree/) 或 [圖](../Graph/) 結構的演算法. 從起始節點開始, 在回溯之前盡可能的走訪每個分支的最遠節點.

深度優先搜尋在有向和無向的圖中都可以使用.


<!--
## Animated example

Here's how depth-first search works on a graph:
-->

## 動畫範例:

這是一個深度優先搜尋的動作:

![Animated example](Images/AnimatedExample.gif)

<!--
Let's say we start the search from node `A`. In depth-first search we look at the starting node's first neighbor and visit that. In the example that is node `B`. Then we look at node `B`'s first neighbor and visit it. This is node `D`. Since `D` doesn't have any unvisited neighbors of its own, we backtrack to node `B` and go to its other neighbor `E`. And so on, until we've visited all the nodes in the graph.

Each time we visit the first neighbor and keep going until there's nowhere left to go, and then we backtrack to a point where there are again nodes to visit. When we've backtracked all the way to node `A`, the search is complete.

For the example, the nodes were visited in the order `A`, `B`, `D`, `E`, `H`, `F`, `G`, `C`.

The depth-first search process can also be visualized as a tree:
-->

讓我們從 `A` 開始. 在深度優先搜尋中我們先走訪節點的鄰節點, 也就是 `B`. 然後再走訪 `B` 的鄰節點 `D`. 因為 `D` 沒有任何鄰節點, 所以我們將走訪回溯到 `B`, 然後走訪另一個鄰節點 `E`. 以此類推, 直到走訪完全部的節點.

每次走訪一個鄰節點, 我們就會一直深入該節點的鄰節點走訪, 直到沒有節點為止. 然後我們會一個節點一個節點的回溯, 每次回溯一個節點就走訪該節點的其他鄰節點, 如果沒有其他鄰節點就繼續回溯. 當我們持續回溯到起點 `A` 時, 我們就可以知道全部節點已經走訪過了.

在上圖例子中, 走訪的順序是: `A`, `B`, `D`, `E`, `H`, `F`, `G`, `C`.

深度優先搜尋也可以用樹來表示:

![Traversal tree](Images/TraversalTree.png)

<!--
The parent of a node is the one that "discovered" that node. The root of the tree is the node you started the depth-first search from. Whenever there's a branch, that's where we backtracked.
-->

父節點是該節點的 "探索" 節點. 根節點是你在圖中的起始節點. 只要看到分支, 就表示在圖中有回溯的動作.

<!--
## The code

Simple recursive implementation of depth-first search:
-->

## 程式碼:

使用簡單的遞迴來實作深度優先搜尋:

```swift
func depthFirstSearch(_ graph: Graph, source: Node) -> [String] {
  var nodesExplored = [source.label]
  source.visited = true

  for edge in source.neighbors {
    if !edge.neighbor.visited {
      nodesExplored += depthFirstSearch(graph, source: edge.neighbor)
    }
  }
  return nodesExplored
}
```

<!--
Where a [breadth-first search](../Breadth-First Search/) visits all immediate neighbors first, a depth-first search tries to go as deep down the tree or graph as it can.

Put this code in a playground and test it like so:
-->

相較於 [廣度優先搜尋](../Breadth-First Search/) 會優先走訪所有的鄰節點, 深度優先搜尋會對每個鄰節點先走訪到最末端, 再換下一個鄰節點.

在 playground 中測試:

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

let nodesExplored = depthFirstSearch(graph, source: nodeA)
print(nodesExplored)
```

<!--
This will output: `["a", "b", "d", "e", "h", "f", "g", "c"]`
-->

這會打印出: `["a", "b", "d", "e", "h", "f", "g", "c"]`

<!--
## What is DFS good for?

Depth-first search can be used to solve many problems, for example:

* Finding connected components of a sparse graph
* [Topological sorting](../Topological Sort/) of nodes in a graph
* Finding bridges of a graph (see: [Bridges](https://en.wikipedia.org/wiki/Bridge_(graph_theory)#Bridge-finding_algorithm))
* And lots of others!
-->

## DFS 優點在哪?

深度優先搜尋可以解決很多問題, 譬如:

* 在稀疏的圖中找到連結的元素.
* 對圖中的節點做 [拓樸排序](../Topological Sort/).
* 在圖中找到 [橋](https://en.wikipedia.org/wiki/Bridge_(graph_theory)#Bridge-finding_algorithm)
* 還有很多其他!


*Written for Swift Algorithm Club by Paulo Tanaka and Matthijs Hollemans*
