# Minimum Spanning Tree (Unweighted Graph) (最小生成樹 - 無權數)

<!--
A minimum spanning tree describes a path that contains the smallest number of edges that are needed to visit every node in the graph.

Take a look at the following graph:
-->

最小生成樹是在一個圖中, 使用最少邊走訪每個節點的路徑.

來看看這個圖:

![Graph](Images/Graph.png)

<!--
If we start from node `a` and want to visit every other node, then what is the most efficient path to do that? We can calculate this with the minimum spanning tree algorithm.

Here is the minimum spanning tree for the graph. It is represented by the bold edges:
-->

假設我們從節點 `a` 開始, 要走訪每個節點, 哪條路徑是最有效率的呢? 我們可以利用最小生成樹演算法來找到.

這就是這個圖的最小生成樹, 用粗線來表示:

![Minimum spanning tree](Images/MinimumSpanningTree.png)

<!--
Drawn as a more conventional tree it looks like this:
-->

把它畫得像傳統的樹就像這樣:

![An actual tree](Images/Tree.png)

<!--
To calculate the minimum spanning tree on an unweighted graph, we can use the [breadth-first search](../Breadth-First Search/) algorithm. Breadth-first search starts at a source node and traverses the graph by exploring the immediate neighbor nodes first, before moving to the next level neighbors. If we tweak this algorithm by selectively removing edges, then it can convert the graph into the minimum spanning tree.

Let's step through the example. We start with the source node `a`, add it to a queue and mark it as visited.
-->

在無權數圖中要計算最小生成樹, 我們可以使用 [廣度優先搜尋](../Breadth-First Search/) 演算法. 廣度優先搜尋從起點開始優先走訪完鄰節點後再深入下一層. 如果我們通過選擇性的去除邊來調整一下這個演算法, 那就可以把圖轉變成最小生成樹.

讓我們一步步解析範例. 從 `a` 開始, 把他加入佇列中並標記成已走訪.

```swift
queue.enqueue(a)
a.visited = true
```

<!--
The queue is now `[ a ]`. As is usual with breadth-first search, we dequeue the node at the front of the queue, `a`, and enqueue its immediate neighbor nodes `b` and `h`. We mark them as visited too.
-->

現在佇列是 `[ a ]`. 通常這時候廣度優先搜尋, 會把佇列最前端的元素 dequeue 出來, 也就是 `a`. 然後馬上 enqueue 鄰節點 `b` 和 `h`. 也把他們標記成已走訪.


```swift
queue.dequeue()   // a
queue.enqueue(b)
b.visited = true
queue.enqueue(h)
h.visited = true
```

<!--
The queue is now `[ b, h ]`. Dequeue `b` and enqueue the neighbor node `c`. Mark it as visited. Remove the edge from `b` to `h` because `h` has already been visited.
-->

現在佇列是 `[ b, h ]`. Dequeue `b` 後 enqueue 鄰節點 `c`, 標記成已走訪. 移除 `b` 到 `h` 的邊, 因為 `h` 已經走訪過了.


```swift
queue.dequeue()   // b
queue.enqueue(c)
c.visited = true
b.removeEdgeTo(h)
```

<!--
The queue is now `[ h, c ]`. Dequeue `h` and enqueue the neighbor nodes `g` and `i`, and mark them as visited.
-->

佇列現在是 `[ h, c ]`. Dequeue `h` 後 enqueue 鄰節點 `g` 和 `i`, 然後標記成已走訪.

```swift
queue.dequeue()   // h
queue.enqueue(g)
g.visited = true
queue.enqueue(i)
i.visited = true
```

<!--
The queue is now `[ c, g, i ]`. Dequeue `c` and enqueue the neighbor nodes `d` and `f`, and mark them as visited. Remove the edge between `c` and `i` because `i` has already been visited.
-->

佇列現在是 `[ c, g, i ]`. Dequeue `c` 後 enqueue 鄰節點 `d` 和 `f`, 然後標記成已走訪. 移除 `c` 到 `i` 的邊, 因為 `i` 已經走訪過了.

```swift
queue.dequeue()   // c
queue.enqueue(d)
d.visited = true
queue.enqueue(f)
f.visited = true
c.removeEdgeTo(i)
```

<!--
The queue is now `[ g, i, d, f ]`. Dequeue `g`. All of its neighbors have been discovered already, so there is nothing to enqueue. Remove the edges from `g` to `f`, as well as `g` to `i`, because `f` and `i` have already been discovered.
-->

佇列現在是 `[ g, i, d, f ]`. Dequeue `g`. 它的所有鄰節點都被走訪過了, 所以沒有節點要 enqueue 了. 移除 `g` 到 `f` 和 `g` 到 `i` 的邊, 因為 `f` 和 `i` 已經被走訪過了.

```swift
queue.dequeue()   // g
g.removeEdgeTo(f)
g.removeEdgeTo(i)
```

<!--
The queue is now `[ i, d, f ]`. Dequeue `i`. Nothing else to do for this node.
-->

佇列現在是 `[ i, d, f ]`. Dequeue `i`. 這個節點已經沒事做了.


```swift
queue.dequeue()   // i
```

<!--
The queue is now `[ d, f ]`. Dequeue `d` and enqueue the neighbor node `e`. Mark it as visited. Remove the edge from `d` to `f` because `f` has already been visited.
-->

佇列現在是 `[ d, f ]`. Dequeue `d` 後 enqueue 鄰節點 `e`. 標記成已走訪. 移除 `d` 到 `f` 的邊, 因為 `f` 已經被走訪過了.

```swift
queue.dequeue()   // d
queue.enqueue(e)
e.visited = true
d.removeEdgeTo(f)
```

<!--
The queue is now `[ f, e ]`. Dequeue `f`. Remove the edge between `f` and `e` because `e` has already been visited.
-->

佇列現在是 `[ f, e ]`. Dequeue `f`. 移除 `f` 到 `e` 的邊, 因為 `e` 已經被走訪過了.


```swift
queue.dequeue()   // f
f.removeEdgeTo(e)
```

<!--
The queue is now `[ e ]`. Dequeue `e`.
-->

佇列現在是 `[ e ]`. Dequeue `e`.

```swift
queue.dequeue()   // e
```

<!--
The queue is empty, which means the minimum spanning tree has been computed.

Here's the code:
-->

佇列現在空了, 表示最小生成樹已經完成了.

程式碼:


```swift
func breadthFirstSearchMinimumSpanningTree(graph: Graph, source: Node) -> Graph {
  let minimumSpanningTree = graph.duplicate()

  var queue = Queue<Node>()
  let sourceInMinimumSpanningTree = minimumSpanningTree.findNodeWithLabel(source.label)
  queue.enqueue(sourceInMinimumSpanningTree)
  sourceInMinimumSpanningTree.visited = true

  while let current = queue.dequeue() {
    for edge in current.neighbors {
      let neighborNode = edge.neighbor
      if !neighborNode.visited {
        neighborNode.visited = true
        queue.enqueue(neighborNode)
      } else {
        current.remove(edge)
      }
    }
  }

  return minimumSpanningTree
}
```

<!--
This function returns a new `Graph` object that describes just the minimum spanning tree. In the figure, that would be the graph containing just the bold edges.

Put this code in a playground and test it like so:
-->

這個函式回傳一個新的 `圖` 物件, 在上圖中留下的邊用粗體展示, 這個圖物件就是最小生成樹.

在 playground 中測試看看:


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
let nodeI = graph.addNode("i")

graph.addEdge(nodeA, neighbor: nodeB)
graph.addEdge(nodeA, neighbor: nodeH)
graph.addEdge(nodeB, neighbor: nodeA)
graph.addEdge(nodeB, neighbor: nodeC)
graph.addEdge(nodeB, neighbor: nodeH)
graph.addEdge(nodeC, neighbor: nodeB)
graph.addEdge(nodeC, neighbor: nodeD)
graph.addEdge(nodeC, neighbor: nodeF)
graph.addEdge(nodeC, neighbor: nodeI)
graph.addEdge(nodeD, neighbor: nodeC)
graph.addEdge(nodeD, neighbor: nodeE)
graph.addEdge(nodeD, neighbor: nodeF)
graph.addEdge(nodeE, neighbor: nodeD)
graph.addEdge(nodeE, neighbor: nodeF)
graph.addEdge(nodeF, neighbor: nodeC)
graph.addEdge(nodeF, neighbor: nodeD)
graph.addEdge(nodeF, neighbor: nodeE)
graph.addEdge(nodeF, neighbor: nodeG)
graph.addEdge(nodeG, neighbor: nodeF)
graph.addEdge(nodeG, neighbor: nodeH)
graph.addEdge(nodeG, neighbor: nodeI)
graph.addEdge(nodeH, neighbor: nodeA)
graph.addEdge(nodeH, neighbor: nodeB)
graph.addEdge(nodeH, neighbor: nodeG)
graph.addEdge(nodeH, neighbor: nodeI)
graph.addEdge(nodeI, neighbor: nodeC)
graph.addEdge(nodeI, neighbor: nodeG)
graph.addEdge(nodeI, neighbor: nodeH)

let minimumSpanningTree = breadthFirstSearchMinimumSpanningTree(graph, source: nodeA)

print(minimumSpanningTree) // [node: a edges: ["b", "h"]]
                           // [node: b edges: ["c"]]
                           // [node: c edges: ["d", "f"]]
                           // [node: d edges: ["e"]]
                           // [node: h edges: ["g", "i"]]
```

<!--
> **Note:** On an unweighed graph, any spanning tree is always a minimal spanning tree. This means you can also use a [depth-first search](../Depth-First Search) to find the minimum spanning tree.
-->

> **注意:** 在無權數圖中, 每個生成樹都是最小生成樹. 這表示你也可以使用 [深度優先搜尋](../Depth-First Search) 來找最小生成樹.

*Written by [Chris Pilcher](https://github.com/chris-pilcher) and Matthijs Hollemans*
