# Greatest Common Divisor (最大公因數)

<!--
The *greatest common divisor* (or Greatest Common Factor) of two numbers `a` and `b` is the largest positive integer that divides both `a` and `b` without a remainder.

For example, `gcd(39, 52) = 13` because 13 divides 39 (`39/13 = 3`) as well as 52 (`52/13 = 4`). But there is no larger number than 13 that divides them both.

You've probably had to learn about this in school at some point. :-)

The laborious way to find the GCD of two numbers is to first figure out the factors of both numbers, then take the greatest number they have in common. The problem is that factoring numbers is quite difficult, especially when they get larger. (On the plus side, that difficulty is also what keeps your online payments secure.)

There is a smarter way to calculate the GCD: 歐幾里德's algorithm. The big idea here is that,
-->

`a` 和 `b` 的 *最大公因數* 是一個可以整除兩個數的公因數中, 最大的那個.

舉個例子, `gcd(39 ,52) = 13`, 因為 13 可以整除 39 (`39/13 = 3)` 和 52 (`52/13 = 4)`.  而沒有比 13 更大個數可以同時整除它們.

你可能已經在學校學過這些了. :-)


找到最大公因數比較費力的是, 要先找出兩個數的公因數們, 再來只需要拿出最大的那個就好. 問題在找到因數挺難的, 而且數字越大越難. (往好處想, 這個難度也讓你線上購物更安全.)

有個比聰明的方法: 歐幾里德演算法. 概念是這樣:

	gcd(a, b) = gcd(b, a % b)

<!--
where `a % b` calculates the remainder of `a` divided by `b`.

Here is an implementation of this idea in Swift:
-->

這裡 `a % b` 是指 `a` 除以 `b` 後的餘數.

實作:

```swift
func gcd(_ a: Int, _ b: Int) -> Int {
  let r = a % b
  if r != 0 {
    return gcd(b, r)
  } else {
    return b
  }
}
```

<!--
Put it in a playground and try it out with these examples:
-->

在 playground 中嘗試這些例子:

```swift
gcd(52, 39)        // 13
gcd(228, 36)       // 12
gcd(51357, 3819)   // 57
```

<!--
Let's step through the third example:
-->

讓我們來看看第三個例子:

	gcd(51357, 3819)

<!--
According to 歐幾里德's rule, this is equivalent to,
-->

依照歐幾里德演算法, 這等同於:

	gcd(3819, 51357 % 3819) = gcd(3819, 1710)

<!--
because the remainder of `51357 % 3819` is `1710`. If you work out this division you get `51357 = (13 * 3819) + 1710` but we only care about the remainder part.

So `gcd(51357, 3819)` is the same as `gcd(3819, 1710)`. That's useful because we can keep simplifying:
-->

因為 `51357 = (13 * 3819) + 1710` 所以 `51357 % 3819 = 1710`.

所以 `gcd(51357, 3819)` 和 `gcd(3819, 1710)` 是一樣的, 那我們就可以以此類推:

	gcd(3819, 1710) = gcd(1710, 3819 % 1710) = 
	gcd(1710, 399)  = gcd(399, 1710 % 399)   = 
	gcd(399, 114)   = gcd(114, 399 % 114)    = 
	gcd(114, 57)    = gcd(57, 114 % 57)      = 
	gcd(57, 0)

<!--
And now can't divide any further. The remainder of `114 / 57` is zero because `114 = 57 * 2` exactly. That means we've found the answer:
-->

這時候我們沒辦法再除下去了. `114 % 57 = 0` 是因為 `114 = 57 * 2`. 這表示我們找到答案了:

	gcd(3819, 51357) = gcd(57, 0) = 57

<!--
So in each step of 歐幾里德's algorithm the numbers become smaller and at some point it ends when one of them becomes zero.

By the way, it's also possible that two numbers have a GCD of 1. They are said to be *relatively prime*. This happens when there is no number that divides them both, for example:
-->

歐幾里德演算法的每一步都會讓兩個數字越來越小, 最終有某一個會變成 0.

順帶一提, 有可能兩個數的最大公因數是 1. 這時候稱這兩個數 *互值*. 這發生在沒有公因數的情況下:

```swift
gcd(841, 299)     // 1
```

<!--
Here is a slightly different implementation of 歐幾里德's algorithm. Unlike the first version this doesn't use recursion but only a basic `while` loop.
-->

這裡用 `while` 迴圈來實作歐幾里德演算法:

```swift
func gcd(_ m: Int, _ n: Int) -> Int {
  var a = 0
  var b = max(m, n)
  var r = min(m, n)

  while r != 0 {
    a = b
    b = r
    r = a % b
  }
  return b
}
```

<!--
The `max()` and `min()` at the top of the function make sure we always divide the larger number by the smaller one.
-->

`max()` 和 `min()` 的方法是在確保一定是用小的數去除大的數.

<!--
## Least Common Multiple

An idea related to the GCD is the *least common multiple* or LCM.

The least common multiple of two numbers `a` and `b` is the smallest positive integer that is a multiple of both. In other words, the LCM is evenly divisible by `a` and `b`. 

For example: `lcm(2, 3) = 6` because 6 can be divided by 2 and also by 3.

We can calculate the LCM using 歐幾里德's algorithm too:
-->

## Least Common Multiple (最小公倍數)

跟最大公因數相關概念有 *最小公倍數*

`a` 和 `b` 的最小公倍數是可以同時被這兩個數整除的數中, 最小的那個.

舉個例子: `lcm(2, 3) = 6`, 因為 6 可以同時被 2 和 3 整除.

我們也可以使用歐幾里德演算法, 因為:

	              a * b
	lcm(a, b) = ---------
	            gcd(a, b)

<!--
In code:
-->

程式碼:

```swift
func lcm(_ m: Int, _ n: Int) -> Int {
  return m*n / gcd(m, n)
}
```

<!--
And to try it out in a playground:
-->
在 playground 中試試看吧:

```swift
lcm(10, 8)    // 40
```

<!--
You probably won't need to use the GCD or LCM in any real-world problems, but it's cool to play around with this ancient algorithm. It was first described by 歐幾里德 in his [Elements](http://publicdomainreview.org/collections/the-first-six-books-of-the-elements-of-歐幾里德-1847/) around 300 BC. Rumor has it that he discovered this algorithm while he was hacking on his Commodore 64.
-->

在現實情況中, 你可能不會使用到最大公因數或最小公倍數, 但是玩玩這些古老的演算法挺有趣的. 西元前 300 年左右歐幾里德在[幾何原本](http://publicdomainreview.org/collections/the-first-six-books-of-the-elements-of-euclid-1847/) 記載這些演算法.

*Written for Swift Algorithm Club by Matthijs Hollemans*
