# Two-Sum Problem (兩兩相加問題)

<!--
You're given an array `a` with numbers. Write an algorithm that checks if there are any two entries in the array that add up to a given number `k`. In other words, is there any `a[i] + a[j] == k`?

There are a variety of solutions to this problem (some better than others). The following solutions both run in **O(n)** time.
-->

給定一個數值陣列 `a`. 提供一個演算法檢查有沒有元素兩兩相加的和會等於 `k`. 換句話說, 有沒有 `a[i] + a[j] == k`?

這個問題有很多種解法. 下列兩種解法的時間複雜度都是 **O(n)**.

<!--
# Solution 1

This solution uses a dictionary to store differences between each element in the array and the sum `k` that we're looking for. The dictionary also stores the indices of each element.

With this approach, each key in the dictionary corresponds to a new target value. If one of the successive numbers from the array is equal to one of the dictionary's keys, then we know there exist two numbers that sum to `k`.
-->

## 解法 1

這個解法用字典來儲存陣列中每個元素和 `k` 的差異, 同時也儲存了每個元素的索引.

在這裡, 字典中的每個鍵值對應到一個新的目標值. 如果陣列中的某個數值和字典中的某個鍵值相等, 那就知道存在兩個數值相加會等於 `k`.

```swift
func twoSumProblem(_ a: [Int], k: Int) -> ((Int, Int))? {
  var dict = [Int: Int]()

  for i in 0 ..< a.count {
    if let newK = dict[a[i]] {
      return (newK, i)
    } else {
      dict[k - a[i]] =  i
    }
  }

  return nil  // if empty array or no entries sum to target k
}
```

<!--
The `twoSumProblem()` function takes two parameters: the array `a` with the numbers, and `k`, the sum we're looking for. It returns the first set of indices `(i, j)` where `a[i] + a[j] == k`, or `nil` if no two numbers add up to `k`.

Let's take a look at an example and run through the algorithm to see how it works. Given is the array:
-->

`twoSumProblem()` 函式接收兩個參數: `a` 是數值陣列, `k` 是指定的和. 它會回傳找到的第一組數值的索引元組 `(i, j)`, 其中 `a[i] + a[j] = k`, 或者沒有找到任何配對合格了話則回傳 `nil`.

用例子來看流程是怎樣的吧. 給定以下陣列:

```swift
[ 7, 2, 23, 8, -1, 0, 11, 6  ]
```

<!--
Let's find out if there exist two entries whose sum is equal to 10.

Initially, our dictionary is empty. We begin looping through each element:

- **i = 0**: Is `7` in the dictionary? No. We add the difference between the target `k` and the current number to the dictionary. The difference is `10 - 7 = 3`, so the dictionary key is `3`. The value for that key is the current index, `0`. The dictionary now looks like this:
-->

假設要找有沒有任意兩個數值相加為 10.

初始時, 字典是空的. 走訪每個陣列元素:

- **i = 0**: `7` 有在字典中嗎? 沒有. 把它和 `k` 的差加入到字典中. `10 - 7 = 3`, 所以字典的鍵值是 `3`. 對應的內容直視該元素的索引, 也就是 `0`. 這時候字典像這樣:

```swift
[ 3: 0 ]
```
<!--
- **i = 1:** Is `2` in the dictionary? No. Let's do as above and add the difference (`10 - 2 = 8`) and the array index (`1`). The dictionary is:
-->

- **i = 1**: `2` 有在字典中嗎? 沒有. 像上一步那樣來處理, 把差 (`10 - 2 = 8`) 和元素索引 (`1`) 放到字典裡:

```swift
[ 3: 0, 8: 1 ]
```
<!--
- **i = 2:** Is `23` in the dictionary? No. Again, we add it to the dictionary. The difference is `10 - 23 = -13` and the index is `2`:
-->

- **i = 2**: `23` 在字典中嗎? 沒有. 再一次, 差是 `10 - 23 = -13`, 元素索引是 `2`:

```swift
[ 3: 0, 8: 1, -13: 2 ]
```

<!--
- **i = 3:** Is `8` in the dictionary? Yes! That means that we have found a pair of entries that sum to our target. Namely the current number `8` and `array[dict[8]]` because `dict[8] = 1`, `array[1] = 2`, and `8 + 2 = 10`. Therefore, we return the corresponding indices of these numbers. For `8` that is the current loop index, `3`. For `2` that is `dict[8]` or `1`. The tuple we return is `(1, 3)`.

The given array actually has multiple solutions: `(1, 3)` and `(4, 6)`. However, only the first solution is returned.

The running time of this algorithm is **O(n)** because it potentially may need to look at all array elements. It also requires **O(n)** additional storage space for the dictionary.
-->

- **i = 3**: `8` 在字典中嗎? 有耶! 表示我們找到了一組配對和起來是 10 了. 因為現在的數值是 `8`, 和 `array[dict[8]]`, 而 `dict[8] = 1`, `array[1] = 2`, `8 + 2 = 10`. 因此我們回傳這兩個數值的索引, 以 `8` 來說剛好是當前回權的索引, `3`. 而 `2` 是由 `dict[8]` 來的, 也就是 `1`. 所以回傳的元組是 `(1, 3)`.

雖然說給定的陣列實際上有不只一個解: `(1, 3)` 和 `(4, 6)`. 無論如何, 只有第一個解會被回傳.

這個演算法的時間複雜度是 **O(n)**, 因為他有可能會走訪陣列所有的元素. 而它也需要增加給字典 **O(n)** 的儲存空間.

<!--
# Solution 2

**Note**: This particular algorithm requires that the array is sorted, so if the array isn't sorted yet (usually it won't be), you need to sort it first. The time complexity of the algorithm itself is **O(n)** and, unlike the previous solution, it does not require extra storage. Of course, if you have to sort first, the total time complexity becomes **O(n log n)**. Slightly worse but still quite acceptable.

Here is the code in Swift:
-->

## 解法 2

**注意:** 這個演算法需要陣列是有序狀態的, 所以如果陣列尚未排序 (通常呢, 是不會排序的), 你會需要先做排序操作. 這個演算法本身的時間複雜度是 **O(n)**, 不過跟上一個不同的事, 它不需要增加儲存空間. 當然, 如果要把排序的時間算進去, 那整個動作的時間複雜度就會是 **O(n log n)**. 比較糟但還可以接受.

程式碼:

```swift
func twoSumProblem(_ a: [Int], k: Int) -> ((Int, Int))? {
  var i = 0
  var j = a.count - 1

  while i < j {
    let sum = a[i] + a[j]
    if sum == k {
      return (i, j)
    } else if sum < k {
      i += 1
    } else {
      j -= 1
    }
  }
  return nil
}
```

<!--
As in the first solution, the `twoSumProblem()` function takes as parameters the array `a` with the numbers and `k`, the sum we're looking for. If there are two numbers that add up to `k`, the function returns a tuple containing their array indices. If not, it returns `nil`. The main difference is that `a` is assumed to be sorted.

To test it, copy the code into a playground and add the following:
-->

和解法 1 相同, `twoSumProblem()` 函式接受兩個參數, 數值陣列 `a` 和指定總和 `k`. 如果有兩個數值和為 `k`, 此函式回傳該兩個數值索引的元組. 如果沒有找到, 回傳 `nil`. 主要的差異在於這裡的 `a` 是有序的.

複製這段程式碼在 playground 中試試看:

```swift
let a = [2, 3, 4, 4, 7, 8, 9, 10, 12, 14, 21, 22, 100]
if let (i, j) = twoSumProblem(a, k: 33) {
  a[i] + a[j]  // 33
}
```

<!--
This returns the tuple `(8, 10)` because `a[8] = 12` and `a[10] = 21`, and together they add up to `33`.

So how does this algorithm work? It takes advantage of the array being sorted. That's true for many algorithms, by the way. If you first sort the data, it's often easier to perform your calculations.

In the example, the sorted array is:
-->

回傳 `(8, 10)`, 因為 `a[8] = 12`, `a[10] = 21`, 加起來是 `33`.

所以這個演算法是怎麼運作的? 首先它的的優勢在於傳入的陣列是有序的. 這點在很多演算法中是真實的, 順帶一提. 如果你先將資料排序, 通常在計算上都會比較容易.

在此例中, 排序後的陣列為:

	[ 2, 3, 4, 4, 7, 8, 9, 10, 12, 14, 21, 22, 100 ]

<!--
The algorithm uses the two variables `i` and `j` to point to the beginning and end of the array, respectively. Then it increments `i` and decrements `j` until the two meet. While it's doing this, it checks whether `a[i]` and `a[j]` add up to `k`.

Let's step through this. Initially, we have:
-->

此演算法使用兩個變數 `i` 和 `j` 來指向陣列的起頭和結尾索引. 然後遞增 `i` 和遞減 `j` 直到兩數相遇. 在這之中, 每一步都會檢查 `a[i]` 和 `a[j]` 之和是否有達到 `k`.

讓我們一步步分解. 初始時, 我們有:

	[ 2, 3, 4, 4, 7, 8, 9, 10, 12, 14, 21, 22, 100 ]
      i                                        j

<!--
The sum of these two is `2 + 100 = 102`. That's obviously too much, since `k = 33` in this example. There is no way that `100` will ever be part of the answer, so decrement `j`.

We have:
-->

這兩個數的和是 `2 + 100 = 102`. 這再明顯不過了, 不過因為 `k = 33`. 所以 `100` 絕對不可能是答案之一, 所以遞減 `j`.

	[ 2, 3, 4, 4, 7, 8, 9, 10, 12, 14, 21, 22, 100 ]
      i                                    j

<!--
The sum is `2 + 22 = 24`. Now the sum is too small. We can safely conclude at this point that the number `2` will never be part of the answer. The largest number on the right is `22`, so we at least need `11` on the left to make `33`. Anything less than `11` is not going to cut it. (This is why we sorted the array!)

So, `2` is out and we increment `i` to look at the next small number.
-->

這時候和為 `2 + 22 = 24`. 現在和太小了. 我們可以安全的得到此時 `2` 不是答案的結論. 目前最大的數是 `22`, 所以至少也需要 `11` 來跟它組合成 `33`. 任何比 `11` 小的數都能跟 `22` 搭配. (這就是用有序陣列的原因)

所以 `2` 出局了, 我們遞增 `i`:


	[ 2, 3, 4, 4, 7, 8, 9, 10, 12, 14, 21, 22, 100 ]
         i                                 j

<!--
The sum is `3 + 22 = 25`. Still too small, so increment `i` again.
-->

現在和是 `3 + 22 = 25`. 還是太小, 所以遞增 `i`:

	[ 2, 3, 4, 4, 7, 8, 9, 10, 12, 14, 21, 22, 100 ]
            i                              j

<!--
In fact, we have to increment `i` a few more times, until we get to `12`:
-->

事實上, 我們需要連續遞增 `i` 個幾次, 直到我們到達 `12`:

	[ 2, 3, 4, 4, 7, 8, 9, 10, 12, 14, 21, 22, 100 ]
                               i           j

<!--
Now the sum is `12 + 22 = 34`. It's too high, which means we need to decrement `j`. This gives:
-->

現在和是 `12 + 22 = 34`. 超過了, 所以表示我們要遞減 `j`:

	[ 2, 3, 4, 4, 7, 8, 9, 10, 12, 14, 21, 22, 100 ]
                               i       j

<!--
And finally, we have the answer: `12 + 21 = 33`. Yay!

It's possible, of course, that there are no values for `a[i] + a[j]` that sum to `k`. In that case, eventually `i` and `j` will point at the same number. Then we can conclude that no answer exists and we return `nil`.

I'm quite enamored by this little algorithm. It shows that with some basic preprocessing on the input data -- sorting it from low to high -- you can turn a tricky problem into a very simple and beautiful algorithm.
-->

最後我們終於得到答案 `12 + 21 = 33`. 耶!

當然是有可能陣列中根本沒有組合可以讓 `a[i] + a[j] = k`. 在那種情況下, 最終 `i` 和 `j` 會到達同一個索引, 此時我們就可以得知陣列中沒有符合的組合, 結束迴圈並回傳 `nil`.

我還滿迷戀這個小演算法的. 它展現了如果預先整理資料 -- 做遞增排序 -- 你可以將一個棘手的問題變得很簡單而且很漂亮的演算法.

*Written for Swift Algorithm Club by Matthijs Hollemans and Daniel Speiser*
