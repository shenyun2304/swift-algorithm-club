# Graph (圖)

<!--
A graph is something that looks like this:
-->

圖結構就像這樣:

![A graph](Images/Graph.png)

<!--
In computer-science lingo, a graph is a set of *vertices* paired with a set of *edges*. The vertices are the round things and the edges are the lines between them. Edges connect a vertex to other vertices.

> **Note:** Vertices are sometimes also called "nodes" and edges are also called "links".

For example, a graph can represent a social network. Each person is a vertex, and people who know each other are connected by edges. A somewhat historically inaccurate example:
-->

在電腦科學中, 圖就是一個 *頂點集合* 搭配 *邊集合* 的集合. 節點就是上圖中圓圈的東西, 而邊就是連接兩個節點之間的線.

> **注意:** 頂點有時被稱為 "節點", 而邊有時被稱為 "連結".

舉例來說, 一個圖可以拿來表示社群網站. 每個人都是一個節點, 而每個人互相認識則由邊來表示. 一個有一點點不精準的例子:

![Social network](Images/SocialNetwork.png)


<!--
Graphs come in all kinds of shapes and sizes. The edges can have a *weight*, where a positive or negative numeric value is assigned to each edge. Consider an example of a graph representing airplane flights. Cities can be vertices, and flights can be edges. Then an edge weight could describe flight time or the price of a ticket.
-->

圖形有各種形狀和大小. 邊可以擁有 *權重*, 可以對邊賦予正值或負值. 下面是一個航線圖. 城市是節點, 航道是邊. 邊的權重就可以是票價或飛行時間.	

![Airplane flights](Images/Flights.png)

<!--
With this hypothetical airline, flying from San Francisco to Moscow is cheapest by going through New York.

Edges can also be *directed*. So far the edges you've seen have been undirected, so if Ada knows Charles, then Charles also knows Ada. A directed edge, on the other hand, implies a one-way relationship. A directed edge from vertex X to vertex Y connects X to Y, but *not* Y to X.

Continuing from the flights example, a directed edge from San Francisco to Juneau in Alaska would indicate that there is a flight from San Francisco to Juneau, but not from Juneau to San Francisco (I suppose that means you're walking back).
-->

在這假設的航線上, 從 San Francisco 飛到 Moscow 經過 New York 是最便宜的路徑.

邊也可以是 *有向* 的. 到目前為止的例子中邊都是雙向的, 例如 Ada 認識 Charles, 那 Charles 也認識 Ada. 一個有向的邊, 表示一種單向的關係. 一個從節點 X 指向節點 Y 只表示 X 連結到 Y, 並 *不表示* Y 到 X.

![One-way flights](Images/FlightsDirected.png)

<!--
The following are also graphs:
-->

以下這些也是圖結構:

![Tree and linked list](Images/TreeAndList.png)


<!--
On the left is a [tree](../Tree/) structure, on the right a [linked list](../Linked List/). Both can be considered graphs, but in a simpler form. After all, they have vertices (nodes) and edges (links).

The very first graph I showed you contained *cycles*, where you can start off at a vertex, follow a path, and come back to the original vertex. A tree is a graph without such cycles.

Another very common type of graph is the *directed acyclic graph* or DAG:
-->

左邊的是叫做 [樹](../Tree/) 的結構, 右邊的結構叫做 [鏈結列表](../Linked List/).兩者都可以被稱為圖結構, 但是相對簡單. 總而言之, 他們就是節點與邊的集合.

在這篇第一張圖我呈現給你的圖中, 是有包含 *環* 的, *環* 是指你可以從一個節點出發, 循著某個路徑, 最後可以走回原本出發的節點. 像樹的結構就不包含迴圈.

另一種常見的圖是 *有向無環圖* 或 DAG.

![DAG](Images/DAG.png)

<!--
Like a tree this does not have any cycles in it (no matter where you start, there is no path back to the starting vertex), but unlike a tree the edges are directional and the shape doesn't necessarily form a hierarchy.
-->

就像樹一樣, 這個例子也不包含任何的環 (無論你從哪邊開始, 沒有可以回到原點的路徑), 跟樹不同的是, 在樹狀階層結構中, 邊的有向性並不是很重要.

<!--
## Why use graphs?

Maybe you're shrugging your shoulders and thinking, what's the big deal? Well, it turns out that graphs are an extremely useful data structure.

If you have some programming problem where you can represent some of your data as vertices and some of it as edges between those vertices, then you can draw your problem as a graph and use well-known graph algorithms such as [breadth-first search](../Breadth-First Search/) or [depth-first search](../Depth-First Search) to find a solution.

For example, let's say you have a list of tasks where some tasks have to wait on others before they can begin. You can model this using an acyclic directed graph:
-->

## 為什麼要用圖?

或許你正聳著肩膀想, 這結構有什麼了不起? 嗯, 事實上, 圖在資料結構中有極大的幫助.

如果你可以把你的資料整理成節點和邊的結構, 那當你遇到某些程式撰寫上的問題, 你就可以將問題劃成圖, 然後用一些知名的圖狀結構演算法來處理問題, 像是 [廣度優先搜尋](../Breadth-First Search/) 或 [深度優先搜尋](../Depth-First Search).

舉例來說, 假設你有一個待辦事項列表, 而這裡面的項目都需要等其他某些項目完成才可以執行, 那你就可以使用有向無環圖的結構:


![Tasks as a graph](Images/Tasks.png)

<!--
Each vertex represents a task. Here, an edge between two vertices means that the source task must be completed before the destination task can start. So task C cannot start before B and D are finished, and B nor D can start before A is finished.

Now that the problem is expressed using a graph, you can use a depth-first search to perform a [topological sort](../Topological Sort/). This will put the tasks in an optimal order so that you minimize the time spent waiting for tasks to complete. (One possible order here is A, B, D, E, C, F, G, H, I, J, K.)

Whenever you're faced with a tough programming problem, ask yourself, "how can I express this problem using a graph?" Graphs are all about representing relationships between your data. The trick is in how you define "relationship".

If you're a musician you might appreciate this graph:
-->

每個節點表示一個工作事項. 這裡的邊表示該事項必須等到其他事項完成後才可以開始. 所以 C 在 B 和 D 完成之前無法啟動, 但 B 和 D 只要等 A 完成就可以開始執行的.

那這個問題就用圖來表示了, 你可以使用深度搜尋來做一個 [拓樸排序](../Topological Sort/). 這個方法會把事項作最佳排序來最小化總體執行時間. (一個可能的順序是 A, B, D, E, C, F, G, H, I, J, K.)

當你遇到難以處理的程式困難, 問一下自己, "我如何用圖來表示這個問題?" 圖可以表示你資料之間的關係. 技巧在於你如何定義 "關係".


![Chord map](Images/ChordMap.png)

<!--
The vertices are chords from the C major scale. The edges -- the relationships between the chords -- represent how [likely one chord is to follow another](http://mugglinworks.com/chordmaps/genmap.htm). This is a directed graph, so the direction of the arrows shows how you can go from one chord to the next. It's also a weighted graph, where the weight of the edges -- portrayed here by line thickness -- shows a strong relationship between two chords. As you can see, a G7-chord is very likely to be followed by a C chord, and much less likely by a Am chord.

You're probably already using graphs without even knowing it. Your data model is also a graph (from Apple's Core Data documentation):
-->

這張圖的節點代表 C 大調的和弦. 邊 -- 和弦之間的關聯 -- 表示 [和弦之間切換關聯](http://mugglinworks.com/chordmaps/genmap.htm), 這是一個有向的圖, 邊的指向表示你可以從某個和弦轉換到哪一個. 這也是個加權的圖, 邊的權重 -- 以線條粗細來分 -- 表示兩個和弦關聯的強度. 可以看到 G7 和 C 和弦關聯非常強, 而到 Am 就很弱.

你可能已經在使用圖了可是卻不知道. 這是一個 Core Data 的 data model 以圖來表示:

![Core Data model](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreDataVersioning/Art/recipe_version2.0.jpg)

<!--
Another common graph that's used by programmers is the state machine, where edges depict the conditions for transitioning between states. Here is a state machine that models my cat:
-->

另一個程式設計師常用的圖是狀態機, 邊描繪了狀態轉換的條件. 這裡是我的貓的狀態機模型:

![State machine](Images/StateMachine.png)

<!--
Graphs are awesome. Facebook made a fortune from their social graph. If you're going to learn any data structure, it should be the graph and the vast collection of standard graph algorithms.
-->

圖結構超讚啊. Facebook 靠著他們的社群圖賺了一大筆財富. 如果你要開始學資料結構, 那將會一大堆的圖和相關的演算法等著你.

<!--
## Vertices and edges, oh my!

In theory, a graph is just a bunch of vertex objects and a bunch of edge objects. But how do you describe these in code?

There are two main strategies: adjacency list and adjacency matrix.

**Adjacency List.** In an adjacency list implementation, each vertex stores a list of edges that originate from that vertex. For example, if vertex A has an edge to vertices B, C, and D, then vertex A would have a list containing 3 edges.
-->

## 節點和邊, 我的天!

理論上, 圖就只是一堆節點物件和邊物件. 但你如何用程式碼來表示它?

有兩個主要的策略: 鄰接列表和鄰接矩陣.

**鄰接列表:** 每個節點物件內儲存一個由該節點為起點的邊列表. 舉個例子, 如果 A 到 B, C, D 之間有邊, 那 A 就有個含有三個邊的列表.

![Adjacency list](Images/AdjacencyList.png)

鄰接列表描述了從節點出去的邊. 如果說 A 個邊到 B 可是 B 沒有到 A, 那 A 就不會出現在 B 的鄰接列表. 要找到某個邊或者某兩個節點之間的的權重是很耗資源的, 因為你需要走訪整個鄰接列表直到找到.

**鄰接矩陣:** 矩陣的列與行表示節點和節點之間的權重. 舉個例子, 假如有一個有向權重為 5.6 的邊從 A 指向 B, 那 A 的那列在對應 B 的欄位上就應該是 5.6:

![Adjacency matrix](Images/AdjacencyMatrix.png)

<!--
Adding another vertex to the graph is expensive, because a new matrix structure must be created with enough space to hold the new row/column, and the existing structure must be copied into the new one.

So which one should you use? Most of the time, the adjacency list is the right approach. What follows is a more detailed comparison between the two.

Let *V* be the number of vertices in the graph, and *E* the number of edges.  Then we have:
-->

要再圖上新增節點, 就要依照新的節點數來創建一個新的矩陣, 並且將原矩陣的內容複製到新矩陣中, 這動作還滿耗資源的.

所以你應該用哪一個呢? 大部分的時候, 鄰接列表比較常被選擇. 接下來會有更多兩者的比較.

我們把 *V* 當作圖中節點的數量, *E* 當作邊的數量. 那會得到:


| Operation       | Adjacency List | Adjacency Matrix |
|-----------------|----------------|------------------|
| Storage Space   | O(V + E)       | O(V^2)           |
| Add Vertex      | O(1)           | O(V^2)           |
| Add Edge        | O(1)           | O(1)             |
| Check Adjacency | O(V)           | O(1)             |


<!--
"Checking adjacency" means that we try to determine that a given vertex is an immediate neighbor of another vertex. The time to check adjacency for an adjacency list is **O(V)**, because in the worst case a vertex is connected to *every* other vertex.

In the case of a *sparse* graph, where each vertex is connected to only a handful of other vertices, an adjacency list is the best way to store the edges. If the graph is *dense*, where each vertex is connected to most of the other vertices, then a matrix is preferred.

We'll show you sample implementations of both adjacency list and adjacency matrix.
-->

"鄰接性檢查" 指嘗試去決定某個節點是否是其他某個節點的直接鄰居. 這個操作對於鄰接列表來說, 時間是  **O(V)**, 因為最糟的情況是某個節點對其他所有的節點都有連結. 

如果是很稀鬆的圖, 每個節點只和某些節點有連結, 那鄰接列表是最好的結構. 但如果是很稠密的圖, 每個節點和其他節點之間大部分都有連結, 那鄰接矩陣就比較合適.


我們將會呈現一些鄰接列表和鄰接陣列得實作範例.

<!--
## The code: edges and vertices

The adjacency list for each vertex consists of `Edge` objects:
-->

## 程式碼: 邊與節點

在鄰接列表中, 每個節點都有 `Edge` 物件:

```swift
public struct Edge<T>: Equatable where T: Equatable, T: Hashable {

  public let from: Vertex<T>
  public let to: Vertex<T>

  public let weight: Double?

}
```

<!--
This struct describes the "from" and "to" vertices and a weight value. Note that an `Edge` object is always directed, a one-way connection (shown as arrows in the illustrations above). If you want an undirected connection, you also need to add an `Edge` object in the opposite direction. Each `Edge` optionally stores a weight, so they can be used to describe both weighted and unweighted graphs.

The `Vertex` looks like this:
-->

這個結構描述了 "起點" 與 "終點" 的節點, 還有權重. 注意到 `Edge` 物件總是有向的, 單向連結 (就像上面圖中的箭頭). 如果你想要的是無相性的連結, 那你需要增加另外方向的 `Edge` 物件. 每個 `Edge` 的權重並非必要, 所以可以拿來表示有權重和無權重的圖.

而 `Vertex` 像這樣:

```swift
public struct Vertex<T>: Equatable where T: Equatable, T: Hashable {

  public var data: T
  public let index: Int

}
```

<!--
It stores arbitrary data with a generic type `T`, which is `Hashable` to enforce uniqueness, and also `Equatable`. Vertices themselves are also `Equatable`.
-->

節點的 `值` 是泛型 `T`, `T` 強制要實作 `Hashable` 和 `Equatable` 來保證唯一性.

<!--
## The code: graphs

> **Note:** There are many, many ways to implement graphs. The code given here is just one possible implementation. You probably want to tailor the graph code to each individual problem you're trying to solve. For instance, your edges may not need a `weight` property, or you may not have the need to distinguish between directed and undirected edges.

Here's an example of a very simple graph:
-->

## 程式碼: 圖

> **注意:** 有非常多的方法可以實作圖. 這裡展示的程式碼只是其中之一的解法而已. 你可能需要針對你遇到的問題個別的設計出圖狀結構.  舉例來說, 你的邊並不一定需要 `權重` 屬性, 或者你的邊也不需要分有向或無向.

這裡是一個非常簡單的圖結構:

![Demo](Images/Demo1.png)

我們可以用鄰接矩陣或鄰接列表來表示它. 實作這些概念的類別是從 `AbstractGraph` 這個 API 中獲得的, 
We can represent it as an adjacency matrix or adjacency list. The classes implementing those concept both inherit a common API from `AbstractGraph`, 因此可以以相同的方式創建, 並在幕後使用不同的資料結構去優化.

讓我們實作 `有向`, `加權` 的圖吧:


```swift
for graph in [AdjacencyMatrixGraph<Int>(), AdjacencyListGraph<Int>()] {

  let v1 = graph.createVertex(1)
  let v2 = graph.createVertex(2)
  let v3 = graph.createVertex(3)
  let v4 = graph.createVertex(4)
  let v5 = graph.createVertex(5)

  graph.addDirectedEdge(v1, to: v2, withWeight: 1.0)
  graph.addDirectedEdge(v2, to: v3, withWeight: 1.0)
  graph.addDirectedEdge(v3, to: v4, withWeight: 4.5)
  graph.addDirectedEdge(v4, to: v1, withWeight: 2.8)
  graph.addDirectedEdge(v2, to: v5, withWeight: 3.2)

}
```

<!--
As mentioned earlier, to create an undirected edge you need to make two directed edges. If we wanted undirected graphs, we'd call this method instead, which takes care of that work for us:
-->

稍早提過, 要製作一個無向的邊, 需要使用兩個方向相反的單向邊來實作. 如果需要的是無向圖, 我們會使用這個方法:

```swift
  graph.addUndirectedEdge(v1, to: v2, withWeight: 1.0)
  graph.addUndirectedEdge(v2, to: v3, withWeight: 1.0)
  graph.addUndirectedEdge(v3, to: v4, withWeight: 4.5)
  graph.addUndirectedEdge(v4, to: v1, withWeight: 2.8)
  graph.addUndirectedEdge(v2, to: v5, withWeight: 3.2)
```
<!--
We could provide `nil` as the values for the `withWeight` parameter in either case to make unweighted graphs.
-->

我們可以對 `withWeight` 參數傳入 `nil` 表示無權重.

<!--
## The code: adjacency list

To maintain the adjacency list, there is a class that maps a list of edges to a vertex. The graph simply maintains an array of such objects and modifies them as necessary.
-->

## 程式碼: 鄰接列表


用一個 class 將邊的列表和節點儲存起來. 圖結構中很單純的用一個陣列儲存好幾個這種 class 的物件, 並根據需求修改它們.

```swift
private class EdgeList<T> where T: Equatable, T: Hashable {

  var vertex: Vertex<T>
  var edges: [Edge<T>]? = nil

  init(vertex: Vertex<T>) {
    self.vertex = vertex
  }

  func addEdge(_ edge: Edge<T>) {
    edges?.append(edge)
  }

}
```

<!--
They are implemented as a class as opposed to structs so we can modify them by reference, in place, like when adding an edge to a new vertex, where the source vertex already has an edge list:
-->

以 class 不用 struct 來做, 所以我們可以用參考來修改他們, 就像如果要對一個新的節點加入一個邊時, 此節點物件已經擁有邊的陣列在裡面.

```swift
open override func createVertex(_ data: T) -> Vertex<T> {
  // check if the vertex already exists
  let matchingVertices = vertices.filter() { vertex in
    return vertex.data == data
  }

  if matchingVertices.count > 0 {
    return matchingVertices.last!
  }

  // if the vertex doesn't exist, create a new one
  let vertex = Vertex(data: data, index: adjacencyList.count)
  adjacencyList.append(EdgeList(vertex: vertex))
  return vertex
}
```

<!--
The adjacency list for the example looks like this:
-->

範例中的鄰接陣列會像這樣:

```
v1 -> [(v2: 1.0)]
v2 -> [(v3: 1.0), (v5: 3.2)]
v3 -> [(v4: 4.5)]
v4 -> [(v1: 2.8)]
```

<!--
where the general form `a -> [(b: w), ...]` means an edge exists from `a` to `b` with weight `w` (with possibly more edges connecting `a` to other vertices as well).
-->

這個 `a -> [(b: w), ...]` 的格式表示有個邊是從 `a` 到 `b` 並且權重是 `w` (也可能有更多 `a` 連結的節點).

<!--
## The code: adjacency matrix

We'll keep track of the adjacency matrix in a two-dimensional `[[Double?]]` array. An entry of `nil` indicates no edge, while any other value indicates an edge of the given weight. If `adjacencyMatrix[i][j]` is not nil, then there is an edge from vertex `i` to vertex `j`.

To index into the matrix using vertices, we use the `index` property in `Vertex`, which is assigned when creating the vertex through the graph object. When creating a new vertex, the graph must resize the matrix:
-->

## 程式碼: 鄰接矩陣

我們使用二維陣列 `[[Double?]]` 來實作. 一個內容為 `nil` 的元素表示沒有邊連接, 如果有值則表示邊的權重. 如果 `adjacencyMatrix[i][j]` 不是 `nil`, 表示有個邊從 `i` 到 `j`.

要將節點和矩陣的索引做連結, 使用 `Vertex` 中的 `index` 屬性, 這屬性會在創建節點物件時指派. 當加入一個新的節點時, 就需要重新調整矩陣的大小:

```swift
open override func createVertex(_ data: T) -> Vertex<T> {
  // check if the vertex already exists
  let matchingVertices = vertices.filter() { vertex in
    return vertex.data == data
  }

  if matchingVertices.count > 0 {
    return matchingVertices.last!
  }

  // if the vertex doesn't exist, create a new one
  let vertex = Vertex(data: data, index: adjacencyMatrix.count)

  // Expand each existing row to the right one column.
  for i in 0 ..< adjacencyMatrix.count {
    adjacencyMatrix[i].append(nil)
  }

  // Add one new row at the bottom.
  let newRow = [Double?](repeating: nil, count: adjacencyMatrix.count + 1)
  adjacencyMatrix.append(newRow)

  _vertices.append(vertex)

  return vertex
}
```

<!--
Then the adjacency matrix looks like this:
-->

然後鄰接矩陣就會像這樣:

	[[nil, 1.0, nil, nil, nil]    v1
	 [nil, nil, 1.0, nil, 3.2]    v2
	 [nil, nil, nil, 4.5, nil]    v3
	 [2.8, nil, nil, nil, nil]    v4
	 [nil, nil, nil, nil, nil]]   v5

	  v1   v2   v3   v4   v5

<!--
## See also

This article described what a graph is and how you can implement the basic data structure. But we have many more articles on practical uses for graphs, so check those out too!
-->

## 相關閱讀

這篇文章描述了什麼是圖狀結構和可以如何實作基本的資料結構. 我們還有更多關於圖狀結構的特殊應用, 也去看看吧!

*Written by Donald Pinckney and Matthijs Hollemans*
