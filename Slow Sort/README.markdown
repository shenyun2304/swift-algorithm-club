# Slow Sort (慢排序)

<!--
Goal: Sort an array of numbers from low to high (or high to low).

You are given an array of numbers and need to put them in the right order. The insertion sort algorithm works as follows:

We can decompose the problem of sorting n numbers in ascending order into

1. find the maximum of the numbers
 1. find the maximum of the first n/2 elements
 2. find the maximum of the remaining n/2 elements
 3. find the largest of those two maxima
2. sorting the remaining ones
-->

目標: 將陣列遞增或遞減排序

給定一個數值的陣列, 將元素正確排序. 插入排序演算法的流程如下:

我們可以將 n 個元素作遞增排序分解成幾個小任務

1. 找到最大的數值
	1. 找到前 n/2 個元素中的最大值
	2. 找到後 n/2 個元素中的最大值
	3. 取出上述兩數的最大值
2. 排序剩下的元素


<!--
## The code

Here is an implementation of slow sort in Swift:
-->

## 程式碼

在 Swift 中的實作:

```swift
public func slowsort(_ i: Int, _ j: Int) {
    if i>=j {
        return
    }
    let m = (i+j)/2
    slowsort(i,m)
    slowsort(m+1,j)
    if numberList[j] < numberList[m] {
        let temp = numberList[j]
        numberList[j] = numberList[m]
        numberList[m] = temp
    }
    slowsort(i,j-1)
}
```

<!--
## Performance
-->

## 表現

| Case  | Performance |
|:-------------: |:---------------:|
| Worst       |  slow |
| Best      | 	O(n^(log(n)/(2+e))))        |
|  Average | 	O(n^(log(n)/2))       | 

<!--
## See also
-->

## 相關閱讀

[Slow Sort explanation in the Internet](http://c2.com/cgi/wiki?SlowSort)

*Written for Swift Algorithm Club by Lukas Schramm*

(used the Insertion Sort Readme as template)