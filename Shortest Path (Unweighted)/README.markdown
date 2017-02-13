# Shortest Path (Unweighted Graph) (最短路徑-無加權圖)

<!--
Goal: find the shortest route to go from one node to another in a graph.

Suppose we have to following graph:
-->

目標: 找到兩節點間最短的路線.

假設我們的圖是這樣:

![Example graph](Images/Graph.png)


<!--
We may want to find out what the shortest way is to get from node `A` to node `F`.

If the [graph is unweighed](../Graph/), then finding the shortest path is easy: we can use the breadth-first search algorithm. For a weighted graph, we can use Dijkstra's algorithm.
-->

我們想找到 `A` 到 `F` 的最短路徑.

如果是 [無加權圖](../Graph/), 找到最段路徑是挺簡單的: 我們可以利用廣度優先搜尋演算法. 對於加權圖, 可以使用 Dijkstra 演算法.

<!--
## Unweighted graph: breadth-first search

[Breadth-first search](../Breadth-First Search/) is a method for traversing a tree or graph data structure. It starts at a source node and explores the immediate neighbor nodes first, before moving to the next level neighbors. As a convenient side effect, it automatically computes the shortest path between a source node and each of the other nodes in the tree or graph.

The result of the breadth-first search can be represented with a tree:
-->

## 無加權圖: 廣度優先搜尋

[廣度優先搜尋](../Breadth-First Search/) 是一種走訪樹狀或圖狀結構的方法. 它從某個起始節點開始, 先走訪完每個鄰節點後再進入更深一層的節點. 這個走法自動的計算了起始節點到其他節點的最短路徑.

廣度優先搜尋可以用樹狀結構來呈現:

![The BFS tree](../Breadth-First Search/Images/TraversalTree.png)

<!--
The root of the tree is the node you started the breadth-first search from. To find the distance from node `A` to any other node, we simply count the number of edges in the tree. And so we find that the shortest path between `A` and `F` is 2. The tree not only tells you how long that path is, but also how to actually get from `A` to `F` (or any of the other nodes).

Let's put breadth-first search into practice and calculate the shortest path from `A` to all the other nodes. We start with the source node `A` and add it to a queue with a distance of `0`.
-->

樹的根節點就是廣度優先搜尋的起始節點. 要找 `A` 到其他節點的距離, 只需要簡單的計算邊的數量就好. 所以可以知道 `A` 到 `F` 的最短距離是 2. 這個樹狀結構不只有多距離的訊息, 整個從 `A` 到 `F` (或其他節點) 的路徑都有.

實際把廣度優先搜尋用來計算 `A` 到其他節點的最短距離吧. 從起始節點 `A` 然後將他的距離設為 `0`.

```swift
queue.enqueue(element: A)
A.distance = 0
```

<!--
The queue is now `[ A ]`. We dequeue `A` and enqueue its two immediate neighbor nodes `B` and `C` with a distance of `1`.
-->

佇列現在是 `[ A ]`, 我們將 `A` dequeue 然後 enqueue `A` 的鄰節點 `B` 和 `C`, 距離是 `1`.

```swift
queue.dequeue()   // A
queue.enqueue(element: B)
B.distance = A.distance + 1   // result: 1
queue.enqueue(element: C)
C.distance = A.distance + 1   // result: 1
```

<!--
The queue is now `[ B, C ]`. Dequeue `B` and enqueue `B`'s neighbor nodes `D` and `E` with a distance of `2`.
-->

佇列現在是 `[ B, C ]`. 把 `B` dequeue 然後加入 `B` 的鄰節點 `D`, `E`, 距離是 `2`.

```swift
queue.dequeue()   // B
queue.enqueue(element: D)
D.distance = B.distance + 1   // result: 2
queue.enqueue(element: E)
E.distance = B.distance + 1   // result: 2
```

<!--
The queue is now `[ C, D, E ]`. Dequeue `C` and enqueue `C`'s neighbor nodes `F` and `G`, also with a distance of `2`.
-->

佇列現在是 `[ C, D, E ]`. 把 `C` dequeue 然後加入 `C` 的鄰節點 `F`, `G`, 距離是 `2`.

```swift
queue.dequeue()   // C
queue.enqueue(element: F)
F.distance = C.distance + 1   // result: 2
queue.enqueue(element: G)
G.distance = C.distance + 1   // result: 2
```

<!--
This continues until the queue is empty and we've visited all the nodes. Each time we discover a new node, it gets the `distance` of its parent plus 1. As you can see, this is exactly what the [breadth-first search](../Breadth-First Search/) algorithm does, except that we now also keep track of the distance travelled.

Here's the code:
-->

這個流程持續到佇列清空, 我們就走訪了所有的節點. 每次我們走訪一個新節點, 它的 `distance` 是他父節點的 `distance` 加 1. 這正是 [廣度優先搜尋](../Breadth-First Search/) 在做的事情, 不同的是我們現在也追蹤走訪距離.

程式碼:

```swift
func breadthFirstSearchShortestPath(graph: Graph, source: Node) -> Graph {
  let shortestPathGraph = graph.duplicate()

  var queue = Queue<Node>()
  let sourceInShortestPathsGraph = shortestPathGraph.findNodeWithLabel(label: source.label)
  queue.enqueue(element: sourceInShortestPathsGraph)
  sourceInShortestPathsGraph.distance = 0

  while let current = queue.dequeue() {
    for edge in current.neighbors {
      let neighborNode = edge.neighbor
      if !neighborNode.hasDistance {
        queue.enqueue(element: neighborNode)
        neighborNode.distance = current.distance! + 1
      }
    }
  }

  return shortestPathGraph
}
```

<!--
Put this code in a playground and test it like so:
-->

在 playground 中測試測試:

```swift
let shortestPathGraph = breadthFirstSearchShortestPath(graph: graph, source: nodeA)
print(shortestPathGraph.nodes)
```

<!--
This will output:
-->

這會印出:

	Node(label: a, distance: 0), Node(label: b, distance: 1), Node(label: c, distance: 1),
	Node(label: d, distance: 2), Node(label: e, distance: 2), Node(label: f, distance: 2),
	Node(label: g, distance: 2), Node(label: h, distance: 3)

<!--
> **Note:** This version of `breadthFirstSearchShortestPath()` does not actually produce the tree, it only computes the distances. See [minimum spanning tree](../Minimum Spanning Tree (Unweighted)/) on how you can convert the graph into a tree by removing edges.
-->

> **注意:** 這裡的 `breadthFirstSearchShortestPath()` 並沒有真的建立樹狀結構, 他只計算距離. 參考 [最小生成樹](../Minimum Spanning Tree (Unweighted)/), 可以利用移除邊來將圖狀結構轉換成樹狀結構.


*Written by [Chris Pilcher](https://github.com/chris-pilcher) and Matthijs Hollemans*
