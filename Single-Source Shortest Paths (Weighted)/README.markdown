# Single-Source Shortest Paths (單源最短路徑)

<!--
The Single-Source shortest path problem finds the shortest paths from a given source vertex to all other vertices in a directed weighted graph. Many variations exist on the problem, specifying whether or not edges may have negative values, whether cycles exist, or whether a path between a specific pair of vertices.
-->

找出有向圖中從某一個節點到其他節點的最短路徑. 這個問題存在很多變化, 例如邊是否有負值權數, 是否有循環的存在, 是否在某些節點之間存在特別的路徑.

<!--
## Bellman-Ford

The Bellman-Ford shortest paths algorithm finds the shortest paths to all vertices from a given source vertex `s` in a directed graph that may contain negative edge weights. It iterates over all edges for each other vertex in the graph, applying a relaxation to the current state of known shortest path weights. The intuition here is that a path will not contain more vertices than are present in the graph, so performing a pass over all edges `|V|-1` number of times is sufficient to compare all possible paths. 

At each step, a value is stored for each vertex `v`, which is the weight of the current known shortest path `s`~>`v`. This value remains 0 for the source vertex itself, and all others are initially `∞`. Then, they are "relaxed" by applying the following test to each edge `e = (u, v)` (where `u` is a source vertex and `v` is a destination vertex for the directed edge):
-->

## Bellman-Ford 演算法

此演算法用來找到給定起始節點 `s` 在有向圖中到其他節點的最短路徑, 包含可能有負權值的邊. 它走訪圖中其他每個節點的所有邊, 持續放鬆當前最短路徑的權數. 直觀上來說, 在圖中的路徑最多就經過所有節點, 所以走訪每個節點時最多比對 `|V|-1` 條邊的權數, 這種做法比比對圖中所有路徑然後取最小來得有效率.

在每個步驟中, 每個節點 `v` 都會儲存一個權值, 其代表到目前步驟為止從 `s`~>`v` 的最短路徑權數. 對於起始節點這個值是 0, 其他節點在初始時是 `∞`. 之後這些值都會被 `e = (u, v)` (`u` 是起始節點, `v` 是目標節點) 來測試放鬆.

	if weights[v] > weights[u] + e.weight {
		weights[v] = weights[u] + e.weight
	}

<!--
Bellman-Ford in essence only computes the lengths of the shortest paths, but can optionally maintain a structure that memoizes the predecessor of each vertex on its shortest path from the source vertex.  Then the paths themselves can be reconstructed by recursing through this structure from a destination vertex to the source vertex. This is maintained by simply adding the statement
-->

Bellman-Ford 的本質其實就是計算這些最短路徑的總權數(長度), 但也可以同時儲存在最短路徑中, 連到本身節點的前一個節點. 這樣路徑本身就可以通過這樣的結構從目標節點到起始節點來重建. 這個結構只需要簡單的在上述的 `if` 宣告裡面加入:

	predecessors[v] = u
	
<!--
### Example

For the following weighted directed graph:
-->

### 範例

針對這個有向圖:

<img src="img/example_graph.png" width="200px" />

<!--
let's compute the shortest paths from vertex `s`. First, we prepare our `weights` and `predecessors` structures thusly:
-->

讓我們來計算從 `s` 開始的最短路徑吧. 首先, 我們先初始化我們的 `weights` 和 `predecessors` 結構:

| weights | predecessors |
| ------------- |:-------------:|
| `weights[s] = 0` | `predecessors[s] = 1` |
| `weights[t] = ∞` | `predecessors[t] = ø` |
| `weights[x] = ∞` | `predecessors[x] = ø` |
| `weights[y] = ∞` | `predecessors[y] = ø` |
| `weights[z] = ∞` | `predecessors[z] = ø` |

<!--
Here are their states after each relaxation iteration (each iteration is a pass over all edges, and there are 4 iterations total for this graph):
-->

接下來是每個放鬆的階段 (每個步驟都會走訪所有的邊, 這個圖狀結構總共會走訪 4 次)

###### Iteration 1:

| weights | predecessors |
| ------------- |:-------------:|
| `weights[s] = 0` | `predecessors[s] = s` |
| `weights[t] = 6` | `predecessors[t] = s` |
| `weights[x] = 4` | `predecessors[x] = y` |
| `weights[y] = 7` | `predecessors[y] = s` |
| `weights[z] = 2` | `predecessors[z] = t` |

###### Iteration 2:

| weights | predecessors |
| ------------- |:-------------:|
| `weights[s] = 0` | `predecessors[s] = s` |
| `weights[t] = 2` | `predecessors[t] = x` |
| `weights[x] = 4` | `predecessors[x] = y` |
| `weights[y] = 7` | `predecessors[y] = s` |
| `weights[z] = 2` | `predecessors[z] = t` |

###### Iteration 3:

| weights | predecessors |
| ------------- |:-------------:|
| `weights[s] = 0` | `predecessors[s] = s` |
| `weights[t] = 2` | `predecessors[t] = x` |
| `weights[x] = 4` | `predecessors[x] = y` |
| `weights[y] = 7` | `predecessors[y] = s` |
| `weights[z] = -2` | `predecessors[z] = t` |

###### Iteration 4:

| weights | predecessors |
| ------------- |:-------------:|
| `weights[s] = 0` | `predecessors[s] = s` |
| `weights[t] = 2` | `predecessors[t] = x` |
| `weights[x] = 4` | `predecessors[x] = y` |
| `weights[y] = 7` | `predecessors[y] = s` |
| `weights[z] = -2` | `predecessors[z] = t` |

<!--
#### Negative weight cycles

An additional useful property of the solution structure is that it can answer whether or not a negative weight cycle exists in the graph and is reachable from the source vertex. A negative weight cycle is a cycle whose sum of edge weights is negative. This means that shortest paths are not well defined in the graph from the specified source, because you can decrease the weight of a path by reentering the cycle, pushing the path's weight towards `-∞`. After fully relaxing the paths, simply running a check over each edge `e = (u, v)` to see if the weight of the shortest path to `v` is greater than the path to `u`, plus the edge weight itself, signals that the edge has a negative weight and would decrease the shortest path's weight further. Since we know we've already performed the relaxations enough times according to the intuition stated above, we can safely assume this further decrease of weight will continue infinitely.
-->

#### 負權數循環

這個結構有另一個好用的特性是可以知道是否有負權數循環存在和是否可以從起始節點到達. 負權數循環是一個總權數小於 0 的循環. 這表示最短路徑沒辦法很好的定義出來, 因為你可以靠重新進入循環來降低路徑權數, 可以一直重複進入到路徑的權數接近 `-∞`. 在結束放鬆路徑後, 簡單的檢查每個邊的 `e = (u, v)` 看看到 `v` 最短路徑的權數是否大於到 `u` 的最短路徑權數加上 `e` 本身的權數, 這表示邊具有負權數並且將進一步減小最短路徑的權數. 在上述中我們已經做了足夠次數的放鬆步驟, 所以可以很安全的假設這個進一步地縮小權數將會無止境的延續.

<!--
##### Example

For this example, we try to compute the shortest paths from `a`:
-->

##### 範例

此範例中, 我們嘗試計算從 `a` 開始的最短路徑:

<img src="img/negative_cycle_example.png" width="200px" />

<!--
The cycle `a`->`t`->`s`->`a` has a total edge weight of -9, therefore shortest paths for `a`~>`t` and `a`~>`s` are not well-defined. `a`~>`b` is also not well-defined because `b`->`t`->`s` is also a negative weight cycle.

This is confirmed after running the relaxation loop, and checking all the edges as mentioned above. For this graph, we would have after relaxation:
-->

循環是 `a`->`t`->`s`->`a` 總權數是 -9, 所以 `a`~>`t` 和 `a`~>`s` 的最短路徑是很難定義的. `a`~>`b` 也是很難定義的因為 `b`->`t`->`s` 也是一個負權數循環.

這在執行放鬆迴圈和檢查所有上述的邊之後得到確認. 針對這張圖, 我們可以有這個放鬆後的結果:


| weights |
| ------------- |
| `weights[a] = -5` |
| `weights[b] = -5` |
| `weights[s] = -18` |
| `weights[t] = -3` |

<!--
One of the edge checks we would perform afterwards would be the following:
-->

其中一個邊的檢查是這樣的:

	e = (s, a)
	e.weight = 4
	weight[a] > weight[s] + e.weight => -5 > -18 + 4 => -5 > -14 => true

<!--	
Because this check is true, we know the graph has a negative weight cycle reachable from `a`.
-->

因為這項檢查結果是 true, 所以我們知道圖中從 `a` 出發的路徑中包含有負權數的循環.

<!--
#### Complexity

The relaxation step requires constant time (`O(1)`) as it simply performs comparisons. That step is performed once per edge (`Θ(|E|)`), and the edges are iterated over `|V|-1` times. This would mean a total complexity of `Θ(|V||E|)`, but there is an optimization we can make: if the outer loop executes and no changes are made to the recorded weights, we can safely terminate the relaxation phase, which means it may execute in `O(|V|)` steps instead of `Θ(|V|)` steps (that is, the best case for any size graph is actually a constant number of iterations; the worst case is still iterating `|V|-1` times).

The check for negative weight cycles at the end is `O(|E|)` if we return once we find a hit. To find all negative weight cycles reachable from the source vertex, we'd have to iterate `Θ(|E|)` times (we currently do not attempt to report the cycles, we simply return a `nil` result if such a cycle is present).

The total running time of Bellman-Ford is therefore `O(|V||E|)`.
-->

#### 複雜度

放鬆的步驟佔用常數時間 (`O(1)`), 因為只是簡單的 `if` 比對.  這個步驟在每個邊都執行一次 (`Θ(|E|)`), 而每個節點比對 `|V|-1` 邊. 表示總共佔用 `Θ(|V||E|)`, 但我還是可以做優化: 如果外部迴圈在執行的時候發現權數沒有改變, 我們可以安全地結束放鬆階段, 這表示可以用 `O(|V|)` 步驟完成而不是 `Θ(|V|)` 次步驟 (也就是說, 對任意大小的圖, 最好的狀況是一個常數時間的迭代次數; 最糟的是 `|V|-1` 次的迭代次數.)

要在最後檢查是否有負權數循環, 在每次找到就回傳一次了話, 佔用 `O(|E|)` 時間. 要找到所有從起始節點開始會到達的負權數循環, 我們需要迭代 `Θ(|E|)` 次 (目前我們沒有要報告每個循環, 對於有類似的循環出現這裡只是簡單的回傳 `nil`).

因此 Bellman-Ford 總和的執行間是 `O(|V||E|)`.

## TODO

- Dijkstra's algorithm for computing SSSP on a directed non-negative weighted graph

<!--
# References
-->

# 參考資源

- Chapter 24 of Introduction to Algorithms, Third Edition by Cormen, Leiserson, Rivest and Stein [https://mitpress.mit.edu/books/introduction-algorithms](https://mitpress.mit.edu/books/introduction-algorithms)
- Wikipedia: [Bellman–Ford algorithm](https://en.wikipedia.org/wiki/Bellman–Ford_algorithm)

*Written for Swift Algorithm Club by [Andrew McKnight](https://github.com/armcknight)*