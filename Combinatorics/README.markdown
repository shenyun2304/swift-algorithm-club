# Permutations (排列)
<!--
A *permutation* is a certain arrangement of the objects from a collection. For example, if we have the first five letters from the alphabet, then this is a permutation:
-->

*permutation* (排列) 是指集合中元素排序的狀況. 舉裡來說, 如果我們英文字母中前五個字, 而他們的排序像這樣:


	a, b, c, d, e

<!--
This is another permutation:
-->

其他種排序:

	b, e, d, a, c

<!--
For a collection of `n` objects, there are `n!` possible permutations, where `!` is the "factorial" function. So for our collection of five letters, the total number of permutations you can make is:
-->

對一個內涵 `n` 個元素的集合, 總共有 `n!` 種不同的排序, `!` 是指 `階乘` 函式. 所以對於我們這個含有五個元素的集合, 總共會有這麼多種排列順序:

	5! = 5 * 4 * 3 * 2 * 1 = 120

<!--
A collection of six items has `6! = 720` permutations. For ten items, it is `10! = 3,628,800`. That adds up quick!

Where does this `n!` come from? The logic is as follows: we have a collection of five letters that we want to put in some order. To do this, you need to pick up these letters one-by-one. Initially, you have the choice of five letters: `a, b, c, d, e`. That gives 5 possibilities.

After picking the first letter, you only have four letters left to choose from. That gives `5 * 4 = 20` possibilities:
-->

一個擁有 6 個元素的集合會有 `6! = 720` 種排列. 10 個元素就會有  `10! = 3,628,800` 種.

為什麼是 `n!`? 邏輯是這樣: 我們有五個字母要做排序. 我們一個一個拿出來排. 開始時, 你有 `a, b, c, d, e` 5種選擇. 這時候你有 5 種結果.

選完第一個字母後, 你剩下 4 個字母可選擇. 這表示在每個第一種選擇之下, 第二個都有 4 種選擇. 所以這時候總共有 `5 * 4 = 20` 種結果.

	a+b    b+a    c+a    d+a    e+a
	a+c    b+c    c+b    d+b    e+b
	a+d    b+d    c+d    d+c    e+c
	a+e    b+e    c+e    d+e    e+d

<!--
After picking the second letter, there are only three letters left to choose from. And so on... When you get to the last letter, you don't have any choice because there is only one letter left. That's why the total number of possibilities is `5 * 4 * 3 * 2 * 1`.

To calculate the factorial in Swift:
-->

選完第二個字母後, 就剩下 3 個字母可選. 以此類推... 所以當你拿到最後一個字母時, 你已經沒有任何其他選擇了. 這就是為什麼總共會有 `5 * 4 * 3 * 2 * 1` 總組合.

在 Swift 中的階層函式:

```swift
func factorial(_ n: Int) -> Int {
  var n = n
  var result = 1
  while n > 1 {
    result *= n
    n -= 1
  }
  return result
}
```

<!--
Try it out in a playground:
-->

在 playground 中試試看:

```swift
factorial(5)   // returns 120
```

<!--
Note that `factorial(20)` is the largest number you can calculate with this function, or you'll get integer overflow.

Let's say that from that collection of five letters you want to choose only 3 elements. How many possible ways can you do this? Well, that works the same way as before, except that you stop after the third letter. So now the number of possibilities is `5 * 4 * 3 = 60`.

The formula for this is:
-->

這邊注意到 `factorial(20)` 是你能用這個函式計算的最大值了, 在大下就去會發生整數溢位.

讓我們我們只想選三個字母出來會怎樣? 這樣了話會有多少種排列? 其實跟之前一樣, 只是你在取完第三個字母後, 就停止動作. 所以總共會有 `5 * 4 * 3 = 60` 種排列順序.

公式是這樣的:

	             n!
	P(n, k) = --------
	          (n - k)!

<!--
where `n` is the size of your collection and `k` is the size of the group that you're selecting. In our example, `P(5, 3) = 5! / (5 - 3)! = 120 / 2 = 60`.

You could implement this in terms of the `factorial()` function from earlier, but there's a problem. Remember that `factorial(20)` is the largest possible number it can handle, so you could never calculate `P(21, 3)`, for example.

Here is an algorithm that can deal with larger numbers:
-->

`n` 是集合元素個樹, `k` 是你想要選擇的元素個樹. 在我們的例子中 `P(5, 3) = 5! / (5 - 3)! = 120 / 2 = 60`.

你可以將這個公式寫進 `factorial()` 中, 但是有個問題, 記得 `factorial(20)` 會產生溢位嗎? 所以你無法計算 `P(21, 3)`.

這是個可以用來處理大數字的演算法:

```swift
func permutations(_ n: Int, _ k: Int) -> Int {
  var n = n
  var answer = n
  for _ in 1..<k {
    n -= 1
    answer *= n
  }
  return answer
}
```

試試看:

```swift
permutations(5, 3)   // returns 60
permutations(50, 6)  // returns 11441304000
permutations(9, 4)   // returns 3024
```

<!--
This function takes advantage of the following algebra fact:
-->

這個函式其實上是這樣來避免溢位:

	          9 * 8 * 7 * 6 * 5 * 4 * 3 * 2 * 1
	P(9, 4) = --------------------------------- = 9 * 8 * 7 * 6 = 3024
	                          5 * 4 * 3 * 2 * 1

<!--
The denominator cancels out part of the numerator, so there's no need to perform a division and you're not dealing with intermediate results that are potentially too large.

However, there are still limits to what you can calculate; for example the number of groups of size 15 that you can make from a collection of 30 objects -- i.e. `P(30, 15)` -- is ginormous and breaks Swift. Huh, you wouldn't think it would be so large but combinatorics is funny that way.
-->

分母消去了一些分子, 所以不需要真的去乘出很大個數字後再進行除法. 

無論如何, 還是有些計算上的限制; 舉個例子, 如果你對一個擁有 30 個元素的集合取出 15 個元素來排列 -- 即 `P(30, 15)` -- 就把 Swift 搞壞了.
你可覺得這不是很大的數, 但是排列組合就是這樣有趣.

<!--
## Generating the permutations

So far we've counted how many permutations exist for a given collection, but how can we actually create a list of all these permutations?

Here's a recursive algorithm by Niklaus Wirth:
-->

## 一般化排列

到目前為止我們對一個已經存在的集合計算他有多少種元素排列, 但我們如何真的創造一個含有所有組合的列表呢?

這是一個 Niklaus Writh 利用遞迴撰寫的一個演算法:

```swift
func permuteWirth<T>(_ a: [T], _ n: Int) {
  if n == 0 {
    print(a)   // display the current permutation
  } else {
    var a = a
    permuteWirth(a, n - 1)
    for i in 0..<n {
      swap(&a[i], &a[n])
      permuteWirth(a, n - 1)
      swap(&a[i], &a[n])
    }
  }
}
```

<!--
Use it as follows:
-->

這樣用:

```swift
let letters = ["a", "b", "c", "d", "e"]
permuteWirth(letters, letters.count - 1)
```

<!--
This prints all the permutations of the input array to the debug output:
-->

他會將所有的排列順序都打印出來:

```swift
["a", "b", "c", "d", "e"]
["b", "a", "c", "d", "e"]
["c", "b", "a", "d", "e"]
["b", "c", "a", "d", "e"]
["a", "c", "b", "d", "e"]
...
```

<!--
As we've seen before, there will be 120 of them.

How does the algorithm work? Good question! Let's step through a simple example with just three elements. The input array is:
-->

照我們之前看到的, 這將會有 120 種排列順序.

那個演算法是怎麼運作的? 好問題! 讓我們以一個只有三個元素的集合當例子一步一步來看:

	[ "x", "y", "z" ]

<!--
We're calling it like so:
-->

我們這樣呼叫他:

```swift
permuteWirth([ "x", "y", "z" ], 2)
```

<!--
Note that the `n` parameter is one less than the number of elements in the array!

After calling `permuteWirth()` it immediately calls itself recursively with `n = 1`. And that immediately calls itself recursively again with `n = 0`. The call tree looks like this:
-->

注意到 `n` 是比陣列長度還少一的數字.

在呼叫 `permuteWirth()` 之後, 馬上再次呼叫自己並帶入 `n = 1`. 而這次的呼叫又會再一次的呼叫自己並帶入 `n = 0`. 這三次的遞迴呼叫像這樣:

```swift
permuteWirth([ "x", "y", "z" ], 2)
	permuteWirth([ "x", "y", "z" ], 1)
		permuteWirth([ "x", "y", "z" ], 0)   // prints ["x", "y", "z"]
```

<!--
When `n` is equal to 0, we print out the current array, which is still unchanged at this point. The recursion has reached the base case, so now we go back up one level and enter the `for` loop.
-->

當 `n` 為 0, 就會打印出當前的陣列, 在這時候陣列沒有任何變化. 遞迴剛觸碰到最基本的狀況, 所以我們回到上一層迴圈並且進入 `for` 迴圈.


```swift
permuteWirth([ "x", "y", "z" ], 2)
	permuteWirth([ "x", "y", "z" ], 1)   <--- back to this level
	    swap a[0] with a[1]
        permuteWirth([ "y", "x", "z" ], 0)   // prints ["y", "x", "z"]
	    swap a[0] and a[1] back
```

This swapped `"y"` and `"x"` and printed the result. We're done at this level of the recursion and go back to the top. This time we do two iterations of the `for` loop because `n = 2` here. The first iteration looks like this:

```swift
permuteWirth([ "x", "y", "z" ], 2)   <--- back to this level
    swap a[0] with a[2]
	permuteWirth([ "z", "y", "x" ], 1)
        permuteWirth([ "z", "y", "x" ], 0)   // prints ["z", "y", "x"]
	    swap a[0] with a[1]
        permuteWirth([ "y", "z", "x" ], 0)   // prints ["y", "z", "x"]
	    swap a[0] and a[1] back
    swap a[0] and a[2] back
```

And the second iteration:

```swift
permuteWirth([ "x", "y", "z" ], 2)
    swap a[1] with a[2]                 <--- second iteration of the loop
	permuteWirth([ "x", "z", "y" ], 1)
        permuteWirth([ "x", "z", "y" ], 0)   // prints ["x", "z", "y"]
	    swap a[0] with a[1]
        permuteWirth([ "z", "x", "y" ], 0)   // prints ["z", "x", "y"]
	    swap a[0] and a[1] back
    swap a[1] and a[2] back
```

To summarize, first it swaps these items:

	[ 2, 1, - ]

Then it swaps these:

	[ 3, -, 1 ]

Recursively, it swaps the first two again:

	[ 2, 3, - ]

Then it goes back up one step and swaps these:

	[ -, 3, 2 ]

And finally the first two again:

	[ 3, 1, - ]

Of course, the larger your array is, the more swaps it performs and the deeper the recursion gets.

If the above is still not entirely clear, then I suggest you give it a go in the playground. That's what playgrounds are great for. :-)

For fun, here is an alternative algorithm, by Robert Sedgewick:

```swift
func permuteSedgewick(_ a: [Int], _ n: Int, _ pos: inout Int) {
  var a = a
  pos += 1
  a[n] = pos
  if pos == a.count - 1 {
    print(a)              // display the current permutation
  } else {
    for i in 0..<a.count {
      if a[i] == 0 {
        permuteSedgewick(a, i, &pos)
      }
    }
  }
  pos -= 1
  a[n] = 0
}
```

You use it like this:

```swift
let numbers = [0, 0, 0, 0]
var pos = -1
permuteSedgewick(numbers, 0, &pos)
```

The array must initially contain all zeros. 0 is used as a flag that indicates more work needs to be done on each level of the recursion.

The output of the Sedgewick algorithm is:

```swift
[1, 2, 3, 0]
[1, 2, 0, 3]
[1, 3, 2, 0]
[1, 0, 2, 3]
[1, 3, 0, 2]
...
```

It can only deal with numbers, but these can serve as indices into the actual array you're trying to permute, so it's just as powerful as Wirth's algorithm.

Try to figure out for yourself how this algorithm works!

## Combinations

A combination is like a permutation where the order does not matter. The following are six different permutations of the letters `k` `l` `m` but they all count as the same combination:

	k, l, m      k, m, l      m, l, k
	l, m, k      l, k, m      m, k, l

So there is only one combination of size 3. However, if we're looking for combinations of size 2, we can make three:

	k, l      (is the same as l, k)
	l, m      (is the same as m, l)
	k, m      (is the same as m, k)

The `C(n, k)` function counts the number of ways to choose `k` things out of `n` possibilities. That's why it's also called "n-choose-k". (A fancy mathematical term for this number is "binomial coefficient".)

The formula for `C(n, k)` is:

	               n!         P(n, k)
	C(n, k) = ------------- = --------
	          (n - k)! * k!      k!

As you can see, you can derive it from the formula for `P(n, k)`. There are always more permutations than combinations. You divide the number of permutations by `k!` because a total of `k!` of these permutations give the same combination.

Above I showed that the number of permutations of `k` `l` `m` is 6, but if you pick only two of those letters the number of combinations is 3. If we use the formula we should get the same answer. We want to calculate `C(3, 2)` because we choose 2 letters out of a collection of 3.

	          3 * 2 * 1    6
	C(3, 2) = --------- = --- = 3
	           1! * 2!     2

Here's a simple function to calculate `C(n, k)`:

```swift
func combinations(_ n: Int, choose k: Int) -> Int {
  return permutations(n, k) / factorial(k)
}
```

Use it like this:

```swift
combinations(28, choose: 5)    // prints 98280
```

Because this uses the `permutations()` and `factorial()` functions under the hood, you're still limited by how large these numbers can get. For example, `combinations(30, 15)` is "only" `155,117,520` but because the intermediate results don't fit into a 64-bit integer, you can't calculate it with the given function.

There's a faster approach to calculate `C(n, k)` in **O(k)** time and **O(1)** extra space. The idea behind it is that the formula for `C(n, k)` is:

                   n!                      n * (n - 1) * ... * 1
    C(n, k) = ------------- = ------------------------------------------
              (n - k)! * k!      (n - k) * (n - k - 1) * ... * 1 * k!

After the reduction of fractions, we get the following formula:

                   n * (n - 1) * ... * (n - k + 1)         (n - 0) * (n - 1) * ... * (n - k + 1)
    C(n, k) = --------------------------------------- = -----------------------------------------
                               k!                          (0 + 1) * (1 + 1) * ... * (k - 1 + 1)

We can implement this formula as follows:

```swift
func quickBinomialCoefficient(_ n: Int, choose k: Int) -> Int {
  var result = 1
  for i in 0..<k {
    result *= (n - i)
    result /= (i + 1)
  }
  return result
}
```

This algorithm can create larger numbers than the previous method. Instead of calculating the entire numerator (a potentially huge number) and then dividing it by the factorial (also a very large number), here we already divide in each step. That causes the temporary results to grow much less quickly.

Here's how you can use this improved algorithm:

```swift
quickBinomialCoefficient(8, choose: 2)     // prints 28
quickBinomialCoefficient(30, choose: 15)   // prints 155117520
```

This new method is quite fast but you're still limited in how large the numbers can get. You can calculate `C(30, 15)` without any problems, but something like `C(66, 33)` will still cause integer overflow in the numerator.

Here is an algorithm that uses dynamic programming to overcome the need for calculating factorials and doing divisions. It is based on Pascal's triangle:

	0:               1
	1:             1   1
	2:           1   2   1
	3:         1   3   3   1
	4:       1   4   6   4   1
	5:     1   5  10   10  5   1
	6:   1   6  15  20   15  6   1

Each number in the next row is made up by adding two numbers from the previous row. For example in row 6, the number 15 is made by adding the 5 and 10 from row 5. These numbers are called the binomial coefficients and as it happens they are the same as `C(n, k)`.

For example, for row 6:

	C(6, 0) = 1
	C(6, 1) = 6
	C(6, 2) = 15
	C(6, 3) = 20
	C(6, 4) = 15
	C(6, 5) = 6
	C(6, 6) = 1

The following code calculates Pascal's triangle in order to find the `C(n, k)` you're looking for:

```swift
func binomialCoefficient(_ n: Int, choose k: Int) -> Int {
  var bc = Array(repeating: Array(repeating: 0, count: n + 1), count: n + 1)

  for i in 0...n {
    bc[i][0] = 1
    bc[i][i] = 1
  }

  if n > 0 {
    for i in 1...n {
      for j in 1..<i {
        bc[i][j] = bc[i - 1][j - 1] + bc[i - 1][j]
      }
    }
  }

  return bc[n][k]
}
```

The algorithm itself is quite simple: the first loop fills in the 1s at the outer edges of the triangle. The other loops calculate each number in the triangle by adding up the two numbers from the previous row.

Now you can calculate `C(66, 33)` without any problems:

```swift
binomialCoefficient(66, choose: 33)   // prints a very large number
```

You may wonder what the point is in calculating these permutations and combinations, but many algorithm problems are really combinatorics problems in disguise. Often you may need to look at all possible combinations of your data to see which one gives the right solution. If that means you need to search through `n!` potential solutions, you may want to consider a different approach -- as you've seen, these numbers become huge very quickly!

## References

Wirth's and Sedgewick's permutation algorithms and the code for counting permutations and combinations are based on the Algorithm Alley column from Dr.Dobb's Magazine, June 1993. The dynamic programming binomial coefficient algorithm is from The Algorithm Design Manual by Skiena.

*Written for Swift Algorithm Club by Matthijs Hollemans and [Kanstantsin Linou](https://github.com/nuts23)*
