# k-Means Clustering (k-Means 叢集)

<!--
Goal: Partition data into two or more clusters.

The idea behind k-Means Clustering is to take a bunch of data and determine if there are any natural clusters (groups of related objects) within the data.

The k-Means algorithm is a so-called *unsupervised* learning algorithm. We don't know in advance what patterns exist in the data -- it has no formal classification to it -- but we would like to see if we can divide the data into groups somehow.

For example, you can use k-Means to find what are the 3 most prominent colors in an image by telling it to group pixels into 3 clusters based on their color value. Or you can use it to group related news articles together, without deciding beforehand what categories to use. The algorithm will automatically figure out what the best groups are.

The "k" in k-Means is a number. The algorithm assumes that there are **k** centers within the data that the various data elements are scattered around. The data that is closest to these so-called **centroids** become classified or grouped together.

k-Means doesn't tell you what the classifier is for each particular data group. After dividing news articles into groups, it doesn't say that group 1 is about science, group 2 is about celebrities, group 3 is about the upcoming election, etc. You only know that related news stories are now together, but not necessarily what that relationship signifies. k-Means only assists in trying to find what clusters potentially exist.
-->

目標: 將資料分割成兩個以上的叢集.

k-Means 的概念是將一堆大資料, 決定他們內部資料是否有自然的叢集 (相關物件的組別).

k-Means 演算法也被稱為 *無監督* 學習演算法. 我們事先並不知道資料中有什麼形式存在 -- 並沒有正式的分類 -- 但我們想試試看是否能將資料分群.

舉例來說, 你可以用 k-Means 將一張圖中的每個像素分成 3 個叢集, 就能找到該圖中最突出的 3 個顏色. 或也可以在不知道文章分類之前, 將新文章做分群. 這個演算法會自動地找出哪個群組是最適合的.

"k" 在 k-Means 裡面是一個數字. 這個演算法假設資料中總共會有 **k** 個中心點, 而其他點會在這些中心點附近. 最靠近這些中心點的資料稱為 **質心**, 就會變成類別或群組.

k-Means 無法告訴你每個資料群的分類方式是什麼. 在分完最新文章之後, 無法說群組 1 是科學, 群組 2 是名人, 群組 3 是最近選舉, 等等. 你只能知道相關的文章會在一起, 但並不表示文章間會有很明顯的關係. k-Means 只是嘗試去尋找是否有潛在群組的存在.

<!--
## The algorithm

The k-Means algorithm is really quite simple at its core.

First, we choose **k** random data points to be the initial centroids. Then, we repeat the following two steps until we've found our clusters:

1. For each data point, find which centroid it is closest to. We assign each point to its nearest centroid.
2. Update the centroid to the mean (i.e. the average) of its nearest data points. We move the centroid so that it really sits in the center of the cluster.

We need to repeat this multiple times because moving the centroid changes which data points belong to it. This goes back and forth for a bit until everything stabilizes. That's when we reach "convergence", i.e. when the centroids no longer move around.

A few of the parameters that are required for k-Means:

- **k**: This is the number of centroids to attempt to locate. If you want to group news articles, this is the number of groups to look for.
- **convergence distance**: If all the centroids move less than this distance after a particular update step, we're done.
- **distance function**: This calculates how far data points are from the centroids, to find which centroid they are closest to. There are a number of distance functions that can be used, but most commonly the Euclidean distance function is adequate (you know, Pythagoras). But often that can lead to convergence not being reached in higher dimensionally.

Let's look at an example.

-->

## 演算法

k-Means 演算法的核心其實滿簡單的.

首先, 我們隨機選擇 **k** 個資料點當作起始的中心點. 接下來我們重複的執行兩個步驟直到我們找到我們要的叢集:

1. 針對每個資料點, 找到最靠近的中心點. 然後將該資料點歸於該中心點的群.
2. 重新計算每群的平均值當作新的中心點. 我們將原本隨機選擇的中心點移動到群組中真的中心點.

我們需要重複以上步驟很多次直到所有的資料點和歸屬的群組穩定下來. 這時候我們就達到 "收斂", 即. 中心點不在移動.

k-Means 需要的一些參數:

- **k**: 這是中心點的數量. 如果你想要將新文章分群, 這個就是總共有多少群的數量.
- **收斂距離**: 如果每個中心點再重新計算後移動的距離小於這個數值, 那我們就完成了.
- **距離函式**: 找到資料點的所屬中心點, 計算資料點到中心點的距離. 有很多距離函式可以使用, 一般而言歐幾里德距離函式 (畢氏定理, 你懂的!) 就足夠了. 但是這高維度資料下有可能無法收斂.

<!--
#### Good clusters

This first example shows k-Means finding all three clusters. In all these examples the circles represent the data points and the stars represent the centroids.

In the first iteration, we choose three data points at random and put our centroids on top of them. Then in each subsequent iteration, we figure out which data points are closest to these centroids, and move the centroids to the average position of those data points. This repeats until we reach equilibrium and the centroids stop moving.
-->

#### 好的叢集

第一個例子 k-Means 選擇 3 個叢集中心. 在這些圖中 圓點表示資料點, 星星表示中心點.

在第一次迭代, 我們隨機選擇 3 個資料點來當中心點. 然後再接下來每次的迭代, 我們找到靠近這些中心點的其他資料點, 然後將中心點移動到平均的地方. 一直重複直到中心點停止移動.

![Good Clustering](Images/k_means_good.png)

<!--
The selection of initial centroids was fortuitous! We found the lower left cluster (indicated by red) and did pretty good on the center and upper left clusters.

> **Note:** These examples are contrived to show the exact nature of k-Means and finding clusters. The clusters in these examples are very easily identified by human eyes: we see there is one in the lower left corner, one in the upper right corner, and maybe one in the middle. In practice, however, data may have many dimensions and may be impossible to visualize. In such cases, k-Means is much better at this job than  human eyes!
-->


初始中心點是隨機選的! 我們發現左下角, 中間和右上角的中心點選得很好.

> **注意:** 這些範例是為了展示自然的 k-Means 和找到叢集設計的. 在這些範例中的叢集用眼睛就可以輕易地用人工辨識看出來: 我們可以看到有左下, 中間和右上都有資料點集中. 實際上, 資料可能有很多維度而非常難以視覺化. 在那些狀況下, k-Means 比人工辨識好多了.

<!--
#### Bad clustering

The next two examples highlight the unpredictability of k-Means and how it does not always find the best clustering.
-->

#### 壞叢集

接下來的範例突顯了 k-Means 的不可預測性, 不是每次都能找到最好的叢集.

![Bad Clustering 1](Images/k_means_bad1.png)

<!--
As you can see in this example, the initial centroids were all a little too close to one another, and the blue one didn't quite get to a good place. By adjusting the convergence distance we should be able to improve the fit of our centroids to the data.
-->

可以看到在這個範例中, 初始中心點的選擇太接近彼此了, 藍色的中心點無法取得較好的空間. 在調整收斂距離之後, 我們應該可以改進資料點歸屬中心點的問題.

![Bad Clustering 1](Images/k_means_bad2.png)

<!--
In this example, the blue cluster never really could separate from the red cluster and as such sort of got stuck down there.
-->

而這個範例中, 藍色和紅色的範圍幾乎無法清楚分隔, 最後就是卡在最極端的部分.

<!--
#### Improving bad clustering 

In these examples of "bad" clustering, the algorithm got stuck in a local optimum. It does find clusters but they're not the best way to divide up the data. To increase your chances of success, you can run the algorithm several times, each time with different points as the initial centroids. You choose the clustering that gives the best results.

To calculate how "good" the clustering is, you find the distance of each data point to its cluster, and add up all these distances. The lower this number, the better! That means each cluster is really in the center of a group of data points, and all clusters are roughly the same size and are spaced evenly apart.
-->

#### 改善壞叢集

在這些 "壞"叢集的例子中, 此演算法在局部優化中卡住了. 它的確找到了叢集, 但是並不是他們最好的分割方式. 為了增加成功的機率, 可以將演算法執行幾次, 每次都有不同的初始中心點. 然後從之中選擇最好的結果.

為了計算叢集分得多 "好", 你可以找到每個資料點和它中心點的距離, 然後將這些距離加起來. 這個加總越小, 就表示越好! 這表示每個叢集的中心點真的有在資料點的附近, 而每個叢集大概會擁有相同的大小和空間.

<!--
## The code

This is what the algorithm could look like in Swift. The `points` array contains the input data as `Vector` objects. The output is an array of `Vector` objects representing the clusters that were found.
-->

## 程式碼

這是這個演算法在 Swift 中的展現. `points` 是內含 `Vector` 元素的陣列. 回傳代表找到的叢集 `Vector` 陣列.

```swift
func kMeans(numCenters: Int, convergeDistance: Double, points: [Vector]) -> [Vector] {
 
  // Randomly take k objects from the input data to make the initial centroids.
  var centers = reservoirSample(points, k: numCenters)

  // This loop repeats until we've reached convergence, i.e. when the centroids
  // have moved less than convergeDistance since the last iteration.
  var centerMoveDist = 0.0
  repeat {
    // In each iteration of the loop, we move the centroids to a new position.
    // The newCenters array contains those new positions.
    let zeros = [Double](count: points[0].length, repeatedValue: 0)
    var newCenters = [Vector](count: numCenters, repeatedValue: Vector(zeros))

    // We keep track of how many data points belong to each centroid, so we
    // can calculate the average later.
    var counts = [Double](count: numCenters, repeatedValue: 0)

    // For each data point, find the centroid that it is closest to. We also 
    // add up the data points that belong to that centroid, in order to compute
    // that average.
    for p in points {
      let c = indexOfNearestCenter(p, centers: centers)
      newCenters[c] += p
      counts[c] += 1
    }
    
    // Take the average of all the data points that belong to each centroid.
    // This moves the centroid to a new position.
    for idx in 0..<numCenters {
      newCenters[idx] /= counts[idx]
    }

    // Find out how far each centroid moved since the last iteration. If it's
    // only a small distance, then we're done.
    centerMoveDist = 0.0
    for idx in 0..<numCenters {
      centerMoveDist += centers[idx].distanceTo(newCenters[idx])
    }
    
    centers = newCenters
  } while centerMoveDist > convergeDistance

  return centers
}
```

<!--
> **Note:** The code in [KMeans.swift](KMeans.swift) is slightly more advanced than the above listing. It also assigns labels to the clusters and has a few other tricks up its sleeve. Check it out!
-->

> **注意:** 在 [KMeans.swift](KMeans.swift) 中的程式碼比上面顯示的還要更進階. 它將每個叢集設定標籤, 而且還有一些其他的技巧. 去看看吧!

<!--
## Performance

k-Means is classified as an NP-Hard type of problem. That means it's almost impossible to find the optimal solution. The selection of the initial centroids has a big effect on how the resulting clusters may end up. Finding an exact solution is not likely -- even in 2 dimensional space.

As seen from the steps above the complexity really isn't that bad -- it is often considered to be on the order of **O(kndi)**, where **k** is the number of centroids, **n** is the number of **d**-dimensional vectors, and **i** is the number of iterations for convergence.

The amount of data has a linear effect on the running time of k-Means, but tuning how far you want the centroids to converge can have a big impact how many iterations will be done. As a general rule, **k** should be relatively small compared to the number of vectors.

Often times as more data is added certain points may lie in the boundary between two centroids and as such those centroids would continue to bounce back and forth and the convergence distance would need to be tuned to prevent that.
-->

## 表現

k-Means 被分類為 NP-Hard 的問題. 這表示幾乎不可能去找到最佳解. 最初的隨機中心點對於結果的叢集有非常大的影響. 找到唯一解是不太可能的 -- 就算只是 2 維空間.

上面步驟的複雜度其實沒有很糟 -- 它通常被考慮是 **O(kndi)** 時間, **k** 是中心點數量, **n** 是有多少個資料點, **d** 是每個資料點的維度, **i** 是收斂的迭代次數.

資料量對於 k-Means 的處理時間具有線性的影響, 但是收斂距離的調整對於迭代次數有巨大的影響. 一般而言, **k** 對於資料點的數量應該要相對小.

通常, 當資料一直加入的時候, 某些資料會落在兩個中心點之間的邊界上, 這樣會讓中心點不斷的移動, 需要調整收斂距離來防止這種事情.

<!--
## See Also
-->

## 相關閱讀

[K-Means Clustering on Wikipedia](https://en.wikipedia.org/wiki/K-means_clustering)

*Written by John Gill and Matthijs Hollemans*
