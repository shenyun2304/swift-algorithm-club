# Radix Sort (基數排序法)

<!--
Radix sort is a sorting algorithm that takes as input an array of integers and uses a sorting subroutine( that is often another efficient sorting algorith) to sort the integers by their radix, or rather their digit.  Counting Sort, and Bucket Sort are often times used as the subroutine for Radix Sort.
-->

對整數陣列依照其基數或位數執行子排序 (通常是其他效率的演算法). 子排序通常使用計數排序和桶排序.

	
<!--
##Example

* Input Array: [170, 45, 75, 90, 802, 24, 2, 66]
* Output Array (Sorted):  [2, 24, 45, 66, 75, 90, 170, 802]
-->

## 範例 

* 輸入陣列: [170, 45, 90, 802, 24, 2, 66]
* 輸出陣列 (有序): [2, 24, 45, 66, 75, 90, 170, 802]

<!--
###Step 1:
The first step in this algorithm is to define the digit or rather the "base" or radix that we will use to sort.
For this example we will let radix = 10, since the integers we are working with in the example are of base 10.
-->

### 步驟 1:

定義要用位數還是基數來當排序對象.

此例中我們讓基數 = 10, 因為我們這邊的整數都是 10 進位的.

<!--
###Step 2:
The next step is to simply iterate n times (where n is the number of digits in the largest integer in the input array), and upon each iteration perform a sorting subroutine on the current digit in question.
-->

### 步驟 2:

這一步中很簡單的迭代 n 次 (n 是陣列中最大數的總位數), 在每次的迭代中對該位數做子排序.

<!--
###Algorithm in Action

Let's take a look at our example input array.

The largest integer in our array is 802, and it has three digits (ones, tens, hundreds).  So our algorithm will iterate three times whilst performing some sorting algorithm on the digits of each integer.

* Iteration 1:  170, 90, 802, 2, 24, 45, 75, 66
* Iteration 2:  802, 2, 24, 45, 66, 170, 75, 90
* Iteration 3:  2, 24, 45, 66, 75, 90, 170, 802
-->

### 進行中的演算

陣列中最大的元素是 802, 它有三個位數 (個位數, 十位數, 百位數). 所以演算法會迭代三次, 同時對每個整數的該位數執行排序演算.

* 迭代 1 (個位數): 170, 90, 802, 2, 24, 45, 75, 66
* 迭代 2 (十位數): 802, 2, 24, 45, 66, 170, 75, 90
* 迭代 3 (百位數): 2, 24, 45, 66, 75, 90, 170, 802


See also [Wikipedia](https://en.wikipedia.org/wiki/Radix_Sort).

*Written for the Swift Algorithm Club by Christian Encarnacion*
