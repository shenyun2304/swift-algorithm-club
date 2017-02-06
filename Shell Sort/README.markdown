# Shell Sort (薛爾排序)

<!--
Shell sort is based on [insertion sort](../Insertion%20Sort/) as a general way to improve its performance, by breaking the original list into smaller sublists which are then individually sorted using insertion sort.

[There is a nice video created at Sapientia University](https://www.youtube.com/watch?v=CmPA7zE8mx0) which shows the process as a Hungarian folk dance.
-->

薛爾排序是以 [插入排序](../Insertion Sort/) 為基礎來加強它的表現, 把原始陣列切割成很多小陣列, 然後個別使用插入排序.

[Sapientia 大學一個很棒的視頻](https://www.youtube.com/watch?v=CmPA7zE8mx0) 用匈牙利民間舞展示了整個排序過程.

<!--
## How it works

Instead of comparing elements that are side-by-side and swapping them if they are out of order, the way insertion sort does it, the shell sort algorithm compares elements that are far apart.

The distance between elements is known as the *gap*. If the elements being compared are in the wrong order, they are swapped across the gap. This eliminates many in-between copies that are common with insertion sort.

The idea is that by moving the elements over large gaps, the array becomes partially sorted quite quickly. This makes later passes faster because they don't have to swap so many items anymore.

Once a pass has been completed, the gap is made smaller and a new pass starts.  This repeats until the gap has size 1, at which point the algorithm functions just like  insertion sort. But since the data is already fairly well sorted by then, the final pass can be very quick.
-->

## 如何運作

插入排序是將元素與其旁邊的元素比較, 然後如果它們順序不對則交換位置, 而薛爾排序比較的元素之間距離大多了.

元素之間的距離稱為 *gap*. 如果兩個元素比較後順序是錯誤的, 它們會跨越這個 gap 來交換位置. 這做法消除了許多插入排序在執行中的暫存的情形.

這個在大範圍之間移動元素可以讓陣列很快的部分有序化. 這讓之後的過程更快, 因為元素不再需要一直交換了.

一但走完一次流程, gap 就會變小然後重新啟動一次迴圈. 流程會一直重複到 gap 是 1, 當這情形發生時, 就變得跟插入排序一樣了. 但因為資料已經幾乎有序了, 所以最後這階段會非常快速.

<!--
## An example

Suppose we want to sort the array `[64, 20, 50, 33, 72, 10, 23, -1, 4]` using shell sort.

We start by dividing the length of the array by 2:
-->
## 範圍

假設我們要用薛爾排序的陣列是 `[64, 20, 50, 33, 72, 10, 23, -1, 4]`.

    n = floor(9/2) = 4

<!--
This is the gap size.

We create `n` sublists. In each sublist, the items are spaced apart by a gap of size `n`. In our example, we need to make four of these sublists. The sublists are sorted by the `insertionSort()` function.

That may not have made a whole lot of sense, so let's take a closer look at what happens.

The first pass is as follows. We have `n = 4`, so we make four sublists:
-->

這個值就是 gap.

我們建立 `n` 個子陣列. 在每個字陣列中, 元素之間的距離是 gap 值 `n`. 在我們的例子中, 我們要建立 4 個這種子陣列. 這些子陣列使用 `insertionSort()` 來排序.

這沒有很直觀, 所以我們詳細的觀察一下發生什麼事.

第一次執行流程. 我們得到 `n = 4`, 所以子陣列是:

	sublist 0:  [ 64, xx, xx, xx, 72, xx, xx, xx, 4  ]
	sublist 1:  [ xx, 20, xx, xx, xx, 10, xx, xx, xx ]
	sublist 2:  [ xx, xx, 50, xx, xx, xx, 23, xx, xx ]
	sublist 3:  [ xx, xx, xx, 33, xx, xx, xx, -1, xx ]

<!--
As you can see, each sublist contains only every 4th item from the original array. The items that are not in a sublist are marked with `xx`. So the first sublist is `[ 64, 72, 4 ]` and the second is `[ 20, 10 ]`, and so on. The reason we use this "gap" is so that we don't have to actually make new arrays. Instead, we interleave them in the original array.

We now call `insertionSort()` once on each sublist.

This particular version of [insertion sort](../Insertion Sort/) sorts from the back to the front. Each item in the sublist is compared against the others. If they're in the wrong order, the value is swapped and travels all the way down until we reach the start of the sublist.

So for sublist 0, we swap `4` with `72`, then swap `4` with `64`. After sorting, this sublist looks like:
-->

可以看到, 每個子陣列只會有原始陣列間隔 4 的元素. 沒有在子陣列的元素標記成 `xx`. 所以第一個子陣列是 `[ 64, 72, 4 ]`, 而第二個子陣列是 `[ 20, 10 ]`, 等等... 我們之所以使用 "gap" 的原因就是不需要真的建立這些子陣列. 相反的, 在原始陣列中相互的交織在一起.

現在對每個子陣列呼叫 `insertionSort()`.

這個 [插入排序](../Insertion Sort/) 的版本是從後往前. 每個在子陣列中的元素相互比較. 如果他們順序錯了, 這個元素就會被交換, 然後持續到我們到達子陣列的首項.

對 sublist 0  來說, 我們將 `4` 和 `72` 交換, 然後再將 `4` 和 `64` 交換. 排序結束後, 子陣列像這樣:


    sublist 0:  [ 4, xx, xx, xx, 64, xx, xx, xx, 72 ]

<!--
The other three sublists after sorting:
-->

另外三個子陣列排序後像這樣:

	sublist 1:  [ xx, 10, xx, xx, xx, 20, xx, xx, xx ]
	sublist 2:  [ xx, xx, 23, xx, xx, xx, 50, xx, xx ]
	sublist 3:  [ xx, xx, xx, -1, xx, xx, xx, 33, xx ]
    
<!--
The total array looks like this now:
-->

整個陣列現在像這樣:

	[ 4, 10, 23, -1, 64, 20, 50, 33, 72 ]

<!--
It's not entirely sorted yet but it's more sorted than before. This completes the first pass.

In the second pass, we divide the gap size by two:
-->

並沒有完全排序但是比之前有序了一點. 這樣第一次流程就結束了.

第二次流程, 我們把 gap 除以 2:

	n = floor(4/2) = 2

<!--
That means we now create only two sublists:
-->

這樣我們會有兩個子陣列:

	sublist 0:  [  4, xx, 23, xx, 64, xx, 50, xx, 72 ]
	sublist 1:  [ xx, 10, xx, -1, xx, 20, xx, 33, xx ]

<!--
Each sublist contains every 2nd item. Again, we call `insertionSort()` to sort these sublists. The result is:
-->

每個子陣列擁有隔一個的元素. 我們再一次呼叫 `insertionSort()`, 結果是:

	sublist 0:  [  4, xx, 23, xx, 50, xx, 64, xx, 72 ]
	sublist 1:  [ xx, -1, xx, 10, xx, 20, xx, 33, xx ]

<!--
Note that in each list only two elements were out of place. So the insertion sort is really fast. That's because we already sorted the array a little in the first pass.

The total array looks like this now:
-->

注意到每個子陣列只有兩個元素順序不對 (`50, 64` 和 `10, -1`). 所以做插入排序非常快. 因為我們已經稍微排序過一些元素了.

現在整個陣列像這樣:


	[ 4, -1, 23, 10, 50, 20, 64, 33, 72 ]

<!--
This completes the second pass. The gap size of the final pass is:
-->

這樣第二次流程就結束了. 接下來的 gap 是:

	n = floor(2/2) = 1

<!--
A gap size of 1 means we only have a single sublist, the array itself, and once again we call `insertionSort()` to sort it. The final sorted array is:
-->

gap 值是 1 表示我們只有 1 個子陣列, 就是當前陣列, 而在呼叫一次 `insertionSort()`. 最後陣列就像這樣:


	[ -1, 4, 10, 20, 23, 33, 50, 64, 72 ]

<!--
The performance of shell sort is **O(n^2)** in most cases or **O(n log n)** if you get lucky. This algorithm produces an unstable sort; it may change the relative order of elements with equal values.
 -->
 
效能上薛爾排序一般情況是 **O(n^2)** 或運氣好一點可以達到 **O(n log n)**. 這個排序是不穩定的; 對於相同值的元素排序後可能和原本順序不同. 

 
<!--
## The gap sequence

The "gap sequence" determines the initial size of the gap and how it is made smaller with each iteration. A good gap sequence is important for shell sort to perform well.

The gap sequence in this implementation is the one from Shell's original version: the initial value is half the array size and then it is divided by 2 each time. There are other ways to calculate the gap sequence.
-->

## gap 的序列

"gap 序列" 決定了初始時的 gap 值和每次重新迭代後變小的比例. 一個好的 gap 序列對薛爾排序的效能是非常重要的.

本範例中的 gap 序列是薛爾排序原始的版本: 初始值是陣列長度的一半, 而每次迭代除以 2. 還有其他計算 gap 序列的方法.

<!--
## Just for fun...

This is an old Commodore 64 BASIC version of shell sort that Matthijs used a long time ago and ported to pretty much every programming language he ever used:
-->

## Just for fun...

這是 Matthijs 很久以前使用的一個舊的 Commodore 64 BASIC 版本的薛爾排序，並且幾乎移植到了他曾經使用過的每一種編程語言.

	61200 REM S is the array to be sorted
	61205 REM AS is the number of elements in S
	61210 W1=AS
	61220 IF W1<=0 THEN 61310
	61230 W1=INT(W1/2): W2=AS-W1
	61240 V=0
	61250 FOR N1=0 TO W2-1
	61260 W3=N1+W1
	61270 IF S(N1)>S(W3) THEN SH=S(N1): S(N1)=S(W3): S(W3)=SH: V=1
	61280 NEXT N1
	61290 IF V>0 THEN 61240
	61300 GOTO 61220
	61310 RETURN

<!--
## See also
-->

## 相關閱讀

[Shellsort on Wikipedia](https://en.wikipedia.org/wiki/Shellsort)

[Shell sort at Rosetta code](http://rosettacode.org/wiki/Sorting_algorithms/Shell_sort)

*Written for Swift Algorithm Club by [Mike Taghavi](https://github.com/mitghi) and Matthijs Hollemans*
