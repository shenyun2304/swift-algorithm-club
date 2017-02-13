# All-Pairs Shortest Paths (全點成對最短路徑)

<!--
The All-Pairs shortest path problem simultaneously computes the shortest path from each node in the graph to each other node, provded a path exists for each pair. In the naive approach, we could simply compute a single-source shortest path from each node to each other node. The number of shortest paths computed is then bound by `O(V^2)`, where `V` is the number of vertices in the graph. Because SSSP is also bounded by `O(V^2)`, the total running time for the naive approach would be `O(V^4)`.

However, by applying a dynamic approach on the adjacency matrix of the graph, a running time of `Θ(V^3)` is achievable, using the `Floyd-Warshall` algorithm. Floyd-Warshall iterates through an adjacency matrix, and for each pair of start(`i`) and end(`j`) vertices it considers if the current distance between them is greater than a path taken through another vertex(`k`) in the graph (if paths `i` ~> `k` and `k` ~> `j` exist). It moves through an adjacency matrix for every vertex `k` applying its comparison for each pair (`i`, `j`), so for each `k` a new adjacency matrix `D(k)` is derived, where each value `d(k)[i][j]` is defined as:
-->

此演算法同事計算圖中任意兩點間最短的連結路徑. 直觀上, 我們可以簡單的對每個頂點做單源最短路徑. 因為單源最短路徑時間複雜度為 `O(V^2)`, `V` 是頂點個數, 這樣的做會讓全點城隊最短路徑的時間複雜度為 `O(V^4)`.

無論如何, 使用 `Floyd-Warshall` 演算法, 透過鄰接矩陣, 可以達到 `Θ(V^3)` 的時間. 概念是這樣的, 對於每個從起點(`i`)到終點(`j`)的路徑, 考慮當前的距離是否比經過另一個頂點 `k` (如果 `i` ~> `k` 然後 `k` ~> `j` 路徑存在) 的距離還要長, 如果經過 `k` 的距離比較短, 那就更新路線. 此演算法把每個頂點輪流當作 `k` 來計算 `i` 到 `j` 的距離 (`i`, `j`), 所以對於每一個 `k`, 都會有一個新的鄰接矩陣 `D(k)` 產生, 而每個 `d(k)[i][j]` 的值定義為: 

<img src="img/weight_comparison_formula.png" width="400px" />

<!--
where `w[i][j]` is the weight of the edge connecting vertex `i` to vertex `j` in the graph's original adjacency matrix. 

When the algorithm memoizes each refined adjacency and predecessor matrix, its space complexity is `Θ(V^3)`, which can be optimised to `Θ(V^2)` by only memoizing the latest refinements. Reconstructing paths is a recursive procedure which requires `Θ(V)` time and `Θ(V^2)` space.
-->

`w[i][j] 是指 `i` 到 `j` 之間有直接路徑時, 該路徑的權數.

當演算法在儲存每個縮減後的鄰接矩陣和上一個矩陣, 空間複雜度是 `Θ(V^3)`, 但可以只儲存最新的鄰接矩陣來讓空間複雜度為 `Θ(V^2)`. 重新建立一條路徑是一個遞迴的過程, 會需要 `Θ(V)` 和 `Θ(V^2)` 的空間.

<!--
# Example

For the following weighted directed graph
-->
## 範例

針對以下的加權有向圖

<img src="img/example_graph.png" width="200px" />

<!--
the adjacency matrix representation `w` is
-->

鄰接矩陣 `w` 為	

<img src="img/original_adjacency_matrix.png" width="200px" />

<!--
### Calculating shortest paths' weights

At the beginning of the algorithm, `D(0)` is the same as `w`, with the following exceptions to accomodate the comparison function:

1. vertices with no path connecting them have the `ø` replaced with `∞` 
2. the diagonal has all `0`s

Here are all the adjacency matrices derived when perform Floyd-Warshall on the above graph:
-->

### 計算最短路徑

演算法一開始, `D(0)` 和 `w` 基本上是相同的, 只是插在一些調整:

1. 沒有相連的節點由 `ø` 被取代為 `∞`.
2. 對角線全為 `0`.

<img src="img/d0.png" width="200px" />k = 1

<img src="img/d1.png" width="200px" />k = 2

<img src="img/d2.png" width="200px" />k = 3

<img src="img/d3.png" width="200px" />k = 4

<!--
with the last being the result, which tells for each pair of starting and ending vertices, the total weight of the shortest path connecting them. During the step where `k = 2`, the comparison that winds up changing the top right value from `2` to `-4` goes like this:
-->

最後的結果呈現每個起點到終點的路徑搭配, 和相對的路徑權數. 在 `k = 2` 的步驟中, 把右上角的值由 `2` 改成 `-4` 是這樣的:

	k = 2, i = 0, j = 3
	d(k-1)[i][j] => d(1)[0][3] = 2
	d(k-1)[i][k] => d(1)[0][2] = 1
	d(k-1)[j][k] => d(1)[2][3] = -5

<!--
therefore `min(2, 2 + -5) => min(2, -4)` produces a new weight of `-4` for the element at `d(2)[0][3]`, meaning that the shortest known path 1 ~> 4 before this step was from 1 -> 2 -> 4 with a total weight of -2, and afterwards we discovered a shorter path from 1 -> 3 -> 4 with a total weight of -4.
-->

因此 `min(2, 2 + -5) => min(2, -4)` 對於 `d(2)[0][3]` 來說產生了一個新的權數 `-4`, 表示在經過 k = 2 之前, 從 1 ~> 4 的最短路徑是 1 -> 2 -> 4 權數是 2, 而在這個步驟我們發現了一個更短的路徑 1 -> 3 -> 4 權數是 -4.

<!--
### Reconstructing shortest paths

This algorithm finds only the lengths of the shortest paths between all pairs of nodes; a separate bookkeeping structure must be maintained to track predecessors' indices and reconstruct the shortest paths afterwards. The predecessor matrices at each step for the above graph follow:
-->

### 重建最短路徑

這樣的演算法只找到每種節點搭配最短的路徑權數 (長度); 必須保持每個單獨的簿記結構以跟踪先前索引並隨後重建最短路徑. 每個步驟的先前索引像這樣:

<img src="img/pi0.png" width="200px" />k=1

<img src="img/pi1.png" width="200px" />k=2

<img src="img/pi2.png" width="200px" />k=3

<img src="img/pi3.png" width="200px" />k=4

<!--
# Project Structure

The provided xcworkspace allows working in the playground, which imports the APSP framework target from the xcodeproj. Build the framework target and rerun the playground to get started. There is also a test target in the xcodeproj. 

In the framework:

- protocols for All-Pairs Shortest Paths algorithms and results structures
- an implementation of the Floyd-Warshall algorithm
-->
## 專案結構

提供的 xcworkspace 檔在 playground 中可以執行, 它從 xcodeproj 中匯入了 APSP 框架. 建立框架目標後回到 playground 練習. xccodeproj 中也有測試目標.

框架中含有:

- 全點成對最短路徑所需要的協定和結果結構
- Floyd-Warshall 演算法的實現


# TODO

- Implement naive `Θ(V^4)` method for comparison
- Implement Johnson's algorithm for sparse graphs
- Implement other cool optimized versions

<!--
# References
-->

## 參考資源

Chapter 25 of Introduction to Algorithms, Third Edition by Cormen, Leiserson, Rivest and Stein [https://mitpress.mit.edu/books/introduction-algorithms](https://mitpress.mit.edu/books/introduction-algorithms)

*Written for Swift Algorithm Club by [Andrew McKnight](https://github.com/armcknight)*