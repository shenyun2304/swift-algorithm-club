# Minimum Edit Distance (最小編輯距離)

<!--
The minimum edit distance is a possibility to measure the similarity of two strings *w* and *u* by counting costs of operations which are necessary to transform *w* into *u* (or vice versa).
-->

最小編輯距離是一種利用計算將 *w* 轉換成 *u* 所需的操作成本來測量 *w* 和 *u* 的相似程度.

<!--
### Algorithm using Levenshtein distance

A common distance measure is given by the *Levenshtein distance*, which allows the following three transformation operations:

* **Insertion** (*ε→x*) of a single symbol *x* with **cost 1**,
* **Deletion** (*x→ε*) of a single symbol *x* with **cost 1**, and
* **Substitution** (*x→y*) of two single symbols *x, y* with **cost 1** if *x≠y* and with **cost 0** otherwise.

When transforming a string by a sequence of operations, the costs of the single operations are added to obtain the (minimal) edit distance. For example, the string *Door* can be transformed by the operations *o→l*, *r→l*, *ε→s* to the string *Dolls*, which results in a minimum edit distance of 3.

To avoid exponential time complexity, the minimum edit distance of two strings in the usual is computed using *dynamic programming*. For this in a matrix
-->

### 使用 Levenshtein 距離演算法

常見的編輯距離是 *Levenshtein 距離*, 它允許三個轉換的操作:

* **插入** (*ε→x*) 一個單一字元 *x* 是 **成本 1**,
* **刪除** (*x→ε*) 一個單一字元 *x* 是 **成本 1**,
* **代換** (*x→y*) 兩個字元 *x, y* 如果 *x≠y* 是 **成本 1** 其他情況是 **成本 0**.

當字串在轉換時, 把每個單一操作的成本加起來獲得最小編輯成本. 舉例來說, 字串 *Door* 可以做 *o→l*, *r→l*, *ε→s* 的操作來變成 *Dolls*, 整個操作的最小編輯距離是 3.

為了避免指數時間複雜度, 最小編輯距離通常使用 *動態規劃* 來實現. 以下的矩陣:

```swift
var matrix = [[Int]](repeating: [Int](repeating: 0, count: n + 1), count: m + 1)
```

以上述 *Door* 轉換到 *Dolls* 為例, 此時矩陣為:

```matrix
   n 0 1 2 3 4 5 
       D o l l s
m    -----------
0  | 0 0 0 0 0 0
1 D| 0 0 0 0 0 0
2 o| 0 0 0 0 0 0
3 o| 0 0 0 0 0 0
4 r| 0 0 0 0 0 0

```

<!--
already computed minimal edit distances of prefixes of *w* and *u* (of length *m* and *n*, respectively) are used to fill the matrix. In a first step the matrix is initialized by filling the first row and the first column as follows:
-->

已經計算完 *w* 和 *u* 前綴詞的最小編輯距離 (長度分別是 *m* 和 *n*) 並填滿這個矩陣. 初始化時先填滿陣列的第一行和第一列:


```swift
// initialize matrix
for index in 1...m {
    // the distance of any first string to an empty second string
    matrix[index][0] = index
}

for index in 1...n {
    // the distance of any second string to an empty first string
    matrix[0][index] = index
}
```
<!--
Then in each cell the minimum of the cost of insertion, deletion, or substitution added to the already computed costs in the corresponding cells is chosen. In this way the matrix is filled iteratively:
-->


此時矩陣為:

```matrix
   n 0 1 2 3 4 5 
       D o l l s
m    -----------
0  | 0 0 1 2 3 4
1 D| 0 0 0 0 0 0
2 o| 1 0 0 0 0 0
3 o| 2 0 0 0 0 0
4 r| 3 0 0 0 0 0

```

接下來矩陣中的其他元素使用插入, 刪除, 或代換的操作去計算每個位置的值. 然後迭代的填滿矩陣:

```swift
// compute Levenshtein distance
for (i, selfChar) in self.characters.enumerated() {
    for (j, otherChar) in other.characters.enumerated() {
        if otherChar == selfChar {
            // substitution of equal symbols with cost 0
            matrix[i + 1][j + 1] = matrix[i][j]
        } else {
            // minimum of the cost of insertion, deletion, or substitution 
            // added to the already computed costs in the corresponding cells
            matrix[i + 1][j + 1] = min(matrix[i][j] + 1, matrix[i + 1][j] + 1, matrix[i][j + 1] + 1)
        } 
    }
}
```

<!--
After applying this algorithm, the minimal edit distance can be read from the rightmost bottom cell and is returned.
-->




此時矩陣為:

```matrix
   j 0 1 2 3 4 5
       D o l l s
i    -----------
0  | 0 0 1 2 3 4
1 D| 0 0 1 2 3 4
2 o| 1 1 0 1 2 3
3 o| 2 2 1 1 2 3
4 r| 3 3 2 2 2 3*

```

經過演算, 最小編輯距離就是矩陣的最右下方元素. 所以 *Door* 轉換到 *Dolls* 的最小編輯距離是 3.

```swift
return matrix[m][n]
```

<!--
This algorithm has a time complexity of Θ(*mn*).
-->

這個演算法時間複雜度是 O(*mn*)



**TODO**: Other distance measures.

*Written for Swift Algorithm Club by Luisa Herrmann*
