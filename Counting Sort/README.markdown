# Counting Sort (計數排序)

<!--
Counting sort is an algorithm for sorting a collection of objects according to keys that are small integers. It operates by counting the number of objects that have each distinct key values, and using arithmetic on those counts to determine the positions of each key value in the output sequence.
-->

使用整數鍵值來做排序依據. 鍵值為每個元素發生的次數, 然後將這些次數做累加後決定該元素排序後的索引位置.


<!--
## Example

To understand the algorithm let's walk through a small example.

Consider the array: `[ 10, 9, 8, 7, 1, 2, 7, 3 ]`

### Step 1:

The first step is to count the total number of occurrences for each item in the array. The output for the first step would be a new array that looks as follows:
-->

## 範例

讓我們經由例子來了解這個演算法.

考慮這個陣列: `[ 10, 9, 8 ,7 ,1 ,2, 7, 3 ]`

### 步驟 1:

第一個步驟是計算每個元素的發生次數. 結果如下:

```
Index 0 1 2 3 4 5 6 7 8 9 10
Count 0 1 1 1 0 0 0 2 1 1 1
```

<!--
Here is the code to accomplish this:
-->

步驟 1 程式碼:

```swift
  let maxElement = array.max() ?? 0

  var countArray = [Int](repeating: 0, count: Int(maxElement + 1))
  for element in array {
    countArray[element] += 1
  }
```

<!--
### Step 2:

In this step the algorithm tries to determine the number of elements that are placed before each element. Since, you already know the total occurrences for each element you can use this information to your advantage. The way it works is to sum up the previous counts and store them at each index.

The count array would be as follows:
-->

### 步驟 2:

將步驟 1 的陣列的每個元素的發生次數依序做累加後得到新的 Count 陣列, 因為已經知道陣列中有幾個元素, 所以可以知道最後累加的數字是多少 (此例總共有 8 個元素, 所以 Count 最後一個元素一定是 8):

```
Index 0 1 2 3 4 5 6 7 8 9 10
Count 0 1 2 3 3 3 3 5 6 7 8
```

<!--
The code for step 2 is:
-->

步驟 2 程式碼:

```swift
  for index in 1 ..< countArray.count {
    let sum = countArray[index] + countArray[index - 1]
    countArray[index] = sum
  }
```

<!--
### Step 3:

This is the last step in the algorithm. Each element in the original array is placed at the position defined by the output of step 2. For example, the number 10 would be placed at an index of 7 in the output array. Also, as you place the elements you need to reduce the count by 1 as those many elements are reduced from the array.

The final output would be:
-->

### 步驟 3:

最後一步. 把原始陣列中的元素值當作索引, 從步驟 2 中的 Count 陣列中獲得重新排序後的索引值, 這邊注意 Count 是累加次數, 所以要減 1 才是索引值. 舉例來說:

```
Origin 10 9 8 7 1 2 7 3

Index  0 1 2 3 4 5 6 7 8 9 10
Count  0 1 2 3 3 3 3 5 6 7 8
Output 0 0 0 0 0 0 0 0
```

原始陣列 Origin 第 1 個元素 10, 而步驟 2 中 Count[10] = 8, 8 - 1 = 7, 所以元素 10 會放在新陣列索引 7 的位置, 並把 Count[10] 設為 7.

```
Origin 10 9 8 7 1 2 7 3
		|
		
Index  0 1 2 3 4 5 6 7 8 9 10
                           |
Count  0 1 2 3 3 3 3 5 6 7 8
                          (7)

Index  0 1 2 3 4 5 6 7
                     |
Output 0 0 0 0 0 0 0 10
```

第 2 個元素 9, Count[9] = 7, 7 - 1 = 6, 所以將元素 9 放置到新陣列索引 6 的位置, 且 Count[9] = 6.

```
Origin 10 9 8 7 1 2 7 3
          |
		
Index  0 1 2 3 4 5 6 7 8 9 10
                         |
Count  0 1 2 3 3 3 3 5 6 7 7
                        (6)

Index  0 1 2 3 4 5 6 7
                   |
Output 0 0 0 0 0 0 9 10
```

第 3 個元素 8, Count[8] = 6, 6 - 1 = 5, 元素 8 放到索引 5 的位置, Count[8] = 5.

```
Origin 10 9 8 7 1 2 7 3
            |
		
Index  0 1 2 3 4 5 6 7 8 9 10
                       |
Count  0 1 2 3 3 3 3 5 6 6 7
                      (5)

Index  0 1 2 3 4 5 6 7
                 |
Output 0 0 0 0 0 8 9 10
```

第 4 個元素 7, Count[7] = 5, 5 - 1 = 4, 元素 7 放到索引 4 的位置, Count[7] = 4.

```
Origin 10 9 8 7 1 2 7 3
              |
		
Index  0 1 2 3 4 5 6 7 8 9 10
                     |
Count  0 1 2 3 3 3 3 5 5 6 7
                    (4)

Index  0 1 2 3 4 5 6 7
               |
Output 0 0 0 0 7 8 9 10
```

第 5 個元素 1, Count[1] = 1, 1 - 1 = 0, 元素 1 放到索引 0 的位置, Count[1] = 0.

```
Origin 10 9 8 7 1 2 7 3
                |
		
Index  0 1 2 3 4 5 6 7 8 9 10
         |
Count  0 1 2 3 3 3 3 4 5 6 7
        (0)

Index  0 1 2 3 4 5 6 7
       |
Output 1 0 0 0 7 8 9 10
```

第 6 個元素 2, Count[2] = 2, 2 - 1 = 1, 元素 2 放到索引 1 的位置, Count[2] = 1.

```
Origin 10 9 8 7 1 2 7 3
                  |
		
Index  0 1 2 3 4 5 6 7 8 9 10
           |
Count  0 0 2 3 3 3 3 4 5 6 7
          (1)

Index  0 1 2 3 4 5 6 7
         |
Output 1 2 0 0 7 8 9 10
```

第 7 個元素 7, Count[7] = 4, 4 - 1 = 3, 元素 7 放到索引 3 的位置, Count[7] = 3.

```
Origin 10 9 8 7 1 2 7 3
                    |
		
Index  0 1 2 3 4 5 6 7 8 9 10
                     |
Count  0 0 1 3 3 3 3 4 5 6 7
                    (3)

Index  0 1 2 3 4 5 6 7
             |
Output 1 2 0 7 7 8 9 10
```

第 8 個元素 3, Count[3] = 3, 3 - 1 = 2, 元素 3 放到索引 2 的位置, Count[3] = 2.

```
Origin 10 9 8 7 1 2 7 3
                      |
		
Index  0 1 2 3 4 5 6 7 8 9 10
             |
Count  0 0 1 3 3 3 3 4 5 6 7
            (2)

Index  0 1 2 3 4 5 6 7
           |
Output 1 2 3 7 7 8 9 10
```

結果:

```
Index  0 1 2 3 4 5 6 7 8 9 10
Count  0 0 1 2 3 3 3 4 5 6 7


Index  0 1 2 3 4 5 6 7
Output 1 2 3 7 7 8 9 10
```



<!--
Here is the code for this final step:
-->

這是最後步驟的程式碼:

```swift
  var sortedArray = [Int](repeating: 0, count: array.count)
  for element in array {
    countArray[element] -= 1
    sortedArray[countArray[element]] = element
  }
  return sortedArray
```

<!--
## Performance

The algorithm uses simple loops to sort a collection. Hence, the time to run the entire algorithm is **O(n+k)** where **O(n)** represents the loops that are required to initialize the output arrays and **O(k)** is the loop required to create the count array.

The algorithm uses arrays of length **n + 1** and **n**, so the total space required is **O(2n)**. Hence for collections where the keys are scattered in a dense area along the number line it can be space efficient.
-->

## 效能表現

這演算法使用很簡單的迴圈來做排序. 因此整個演算法的時間是 **O(n+k)**, 其中 **O(n)** 是初始化 Output 陣列, 而 **O(k)** 是建立 Count 陣列.

空間上則分別使用了 **n + 1** 和 **n**, 所以整體空間使用了 **O(2n)**. 因此如果集合中的鍵值分佈的很集中, 那在空間上會更節省.


*Written for Swift Algorithm Club by Ali Hafizji*
