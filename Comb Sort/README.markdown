# Comb Sort (梳排序)

<!--
A common issue for Bubble Sort is when small values are located near the end of an array. 
This problem severely slows down Bubble Sort, as it must move the small value -- or _turtle_ -- 
through nearly the entire array. Bubble Sort works by checking the current index of an array 
against the next index, and when those two values are unsorted, they are swapped into place. 
As a result, the values bubble into their rightful place within the array. 

Comb Sort improves upon Bubble Sort by dealing with these turtles near the end of the array. 
The value of the current index of the array is compared against one a set distance away. This 
removes a worst-case scenario of Bubble Sort, and greatly improves on the time complexity of Bubble Sort. 
-->

當小的值在陣列的尾端時, 氣泡排序會非常緩慢. 

梳排序優化了氣泡排序在這方面的問題. 當前索引是和一段距離後的元素比較. 這樣解決了氣泡排序的最糟情形, 也大幅度的改善了時間複雜度.

<!--
## Example 

A step-by-step example of how Comb Sort works, and differs from Bubble Sort, can be seen [here](http://www.exforsys.com/tutorials/c-algorithms/comb-sort.html). 

Here is a visual to see Comb Sort in effect: 
-->

## 範例 

[這裡](http://www.exforsys.com/tutorials/c-algorithms/comb-sort.html) 可以看到梳排序一步步的流程, 還有跟氣泡排去不同的方.

這個動畫可以看到梳排序的影響:

![](https://upload.wikimedia.org/wikipedia/commons/4/46/Comb_sort_demo.gif)

<!--
## Algorithm 

Similar to Bubble Sort, two values within an array are compared. When the lower index value 
is larger than the higher index value, and thus out of place within the array, they are 
swapped. Unlike Bubble Sort, the value being compared against is a set distance away. This 
value -- the _gap_ -- is slowly decreased through iterations. 
-->

## 演算法

跟氣泡排序很像, 兩個陣列中的元素互相比較. 如果比較前端的元素比後端的大, 那兩個元素就互換位置. 跟氣泡排序不同社, 比較的元素是一段距離以後的元素, 並不是下一個元素. 這個距離 -- _gap_ -- 會慢慢的隨著每次迴圈而漸漸縮小.

<!--
## The Code 

Here is a Swift implementation of Comb Sort: 
-->

## 程式碼:

Swift 實作梳排序:

```swift
func combSort (input: [Int]) -> [Int] {
    var copy: [Int] = input
    var gap = copy.count
    let shrink = 1.3

    while gap > 1 {
        gap = (Int)(Double(gap) / shrink)
        if gap < 1 {
            gap = 1
        }
    
        var index = 0
        while !(index + gap >= copy.count) {
            if copy[index] > copy[index + gap] {
                swap(&copy[index], &copy[index + gap])
            }
            index += 1
        }
    }
    return copy
}
```

<!--
This code can be tested in a playground by calling this method with a paramaterized array to sort: 
-->

這段程式碼可以在 playground 中這樣測試:

```swift
combSort(example_array_of_values)
```

<!--
This will sort the values of the array into ascending order -- increasing in value.  
-->

這會把陣列以遞增規則排序.

<!--
## Performance

Comb Sort was created to improve upon the worst case time complexity of Bubble Sort. With Comb 
Sort, the worst case scenario for performance is exponential -- O(n^2). At best though, Comb Sort 
performs at O(n logn) time complexity. This creates a drastic improvement over Bubble Sort's performance. 

Similar to Bubble Sort, the space complexity for Comb Sort is constant -- O(1). 
This is extremely space efficient as it sorts the array in place. 
-->

## 效能表現

梳排序被發明出來是為了改善氣泡排序最糟的狀況. 在梳排序之下, 最糟糕的情況是指數時間 -- O(n^2). 最好的情況是 O(n log n). 這對氣泡排序而言是極大的改進.

和氣泡排序一樣, 梳排序的記憶體表現是常數時間 -- O(1)


<!--
## Additional Resources
-->

## 其他資源

[Comb Sort Wikipedia](https://en.wikipedia.org/wiki/Comb_sort)


*Written for the _Swift Algorithm Club_ by [Stephen Rutstein](https://github.com/srutstein21)*
