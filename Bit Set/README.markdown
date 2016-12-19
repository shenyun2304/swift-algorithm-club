# Bit Set 位元集
<!--
A fixed-size sequence of *n* bits. Also known as bit array or bit vector.

To store whether something is true or false you use a `Bool`. Every programmer knows that... But what if you need to remember whether 10,000 things are true or not?

You could make an array of 10,000 booleans but you can also go hardcore and use 10,000 bits instead. That's a lot more compact because 10,000 bits fit in less than 160 `Int`s on a 64-bit CPU.

Since manipulating individual bits is a little tricky, you can use `BitSet` to hide the dirty work.
-->

一個固定長度為 *n* 的序列. 也叫做位元陣列或位元向量.

大部分的程式設計者都知道要儲存像 真 或 假 的資料時, 會使用 `Bool` 這種資料型態. 但如果你需要儲存 10,000 個布林值呢?

你可以創建一個含有 10,000 個布林值的陣列, 但你也可以單純使用 10,000 個位元. 這樣做會更省資源, 因為在 64-bit 的 CPU 上, 10,000 個位元比 160 個 ｀整數` 更小.

不過要針對每個位元來操作需要點小技巧, 可以使用 `BitSet`(位元集) 來處理這件事.


<!--
## The code

A bit set is simply a wrapper around an array. The array doesn't store individual bits but larger integers called the "words". The main job of `BitSet` is to map the bits to the right word.
-->

## 程式碼

位元集是一個陣列的容器, 這個趁列並不儲存每個單一位元, 而是儲存很大的整數, 叫做 "word". `BitSet` 的主要工作就是將位元對應到正確的 word.

```swift
public struct BitSet {
  private(set) public var size: Int

  private let N = 64
  public typealias Word = UInt64
  fileprivate(set) public var words: [Word]

  public init(size: Int) {
    precondition(size > 0)
    self.size = size

    // Round up the count to the next multiple of 64.
    let n = (size + (N-1)) / N
    words = [Word](repeating: 0, count: n)
  }
}
```

<!--
`N` is the bit size of the words. It is 64 because we store the bits in a list of unsigned 64-bit integers. (It's fairly easy to change `BitSet` to use 32-bit words instead.)

If you write,
-->
`N` 是 word 的位元數. 在此它會是 64 是因為我們將位元以 64 位元無號整數來儲存. (要把 `BitSet` 改成 32 位元來做也是非常容易)

如果你撰寫,

```swift
var bits = BitSet(size: 140)
```

<!--
then the `BitSet` allocates an array of three words. Each word has 64 bits and therefore three words can hold 192 bits. We only use 140 of those bits so we're wasting a bit of space (but of course we can never use less than a whole word).

> **Note:** The first entry in the `words` array is the least-significant word, so these words are stored in little endian order in the array.
-->

那 `BitSet` 就會配置一個含有三個 word 的陣列, 每個 word 有 64 個位元, 所以三個 word 可以儲存 192 個位元. 我們只需要 140 個所以我們會浪費一點空間 ( 當然我們不會使用少於一個 word ).

> **注意:** 最先進入 `words` 陣列的是最低有效 word, 所以這些 word 是以 little endian 的順序來儲存.

<!--
## Looking up the bits

Most of the operations on `BitSet` take the index of the bit as a parameter, so it's useful to have a way to find which word contains that bit.
-->

## 查找位元

大部分 `BitSet` 的操作是以位元的索引來當參數, 所以如果有個方法可以知道是哪個 word 包含該位元是非常有用的.

```swift
  private func indexOf(_ i: Int) -> (Int, Word) {
    precondition(i >= 0)
    precondition(i < size)
    let o = i / N
    let m = Word(i - o*N)
    return (o, 1 << m)
  }
```

<!--
The `indexOf()` function returns the array index of the word, as well as a "mask" that shows exactly where the bit sits inside that word.

For example, `indexOf(2)` returns the tuple `(0, 4)` because bit 2 is in the first word (index 0). The mask is 4. In binary the mask looks like the following:

	0010000000000000000000000000000000000000000000000000000000000000

That 1 points at the second bit in the word.

> **Note:** Remember that everything is shown in little-endian order, including the bits themselves. Bit 0 is on the left, bit 63 on the right.

Another example: `indexOf(127)` returns the tuple `(1, 9223372036854775808)`. It is the last bit of the second word. The mask is:

	0000000000000000000000000000000000000000000000000000000000000001

Note that the mask is always 64 bits because we look at the data one word at a time.
-->

`indexOf()` 函式回傳該 word 的索引, 就像 "遮罩" 一樣, 顯示該位元在該 word 中的哪一個位置.

舉例來說, `indexOf(2)` 回傳 (0, 4), 因為位元 2 在第一個 word (索引值 0) 之中. 遮罩 4 是因為在二進位中表示像這樣:

	0010000000000000000000000000000000000000000000000000000000000000

那個 1 指出該位元在 word 中的位置.

> **注意:** 這邊所有的表示是以 little endian 順序來呈現, 包含位元本身. 位元 0 在左側, 位元 63 在右側.

另一個例子: `indexOf(127)` 回傳 `(1, 9223372036854775808)`. 這表示是第二個 word 最後一個位元. 遮罩是這樣:

	0000000000000000000000000000000000000000000000000000000000000001

提醒一下遮罩永遠是 64 位元的形式, 因為我們都在一個 word 中來看位元.

<!--
## Setting and getting bits

Now that we know where to find a bit, setting it to 1 is easy:
-->

## 設定與獲得位元

現在我們知道如何到位元, 將他設定成 1 很簡單:

```swift
  public mutating func set(_ i: Int) {
    let (j, m) = indexOf(i)
    words[j] |= m
  }
```

<!--
This looks up the word index and the mask, then performs a bitwise OR between that word and the mask. If the bit was 0 it becomes 1. If it was already set, then it remains set.

Clearing the bit -- i.e. changing it to 0 -- is just as easy:
-->

先獲得該位元所在的 word 和 遮罩, 然後將該 word 和遮罩做 OR 的位元運算. 如果位元原本是 0, 就會變成 1. 如果原本就是 1, 那將保持 1.

重置位元 -- 即將該位元設定成 0 -- 也很簡單:

```swift
  public mutating func clear(_ i: Int) {
    let (j, m) = indexOf(i)
    words[j] &= ~m
  }
```
<!--
Instead of a bitwise OR we now do a bitwise AND with the inverse of the mask. So if the mask was `00100000...0`, then the inverse is `11011111...1`. All the bits are 1, except for the bit we want to set to 0. Due to the way `&` works, this leaves all other bits alone and only changes that single bit to 0.

To see if a bit is set we also use the bitwise AND but without inverting:
-->

跟設定一樣, 只是這邊以 AND 位元運算來處理. 所以如果遮罩是 `0010000...0`, 則反向為 `11011111...1`. 除了該位元以外所有的位元皆是 1. 因為使用 `&` 運算, 所以可以保持其他位元的狀態, 並且只改變指定的位元.

想知道某個位元是否為 1, 我們也可以使用 AND 位元運算但是不需要反向:

```swift
  public func isSet(_ i: Int) -> Bool {
    let (j, m) = indexOf(i)
    return (words[j] & m) != 0
  }
```
<!--
We can add a subscript function to make this all very natural to express:
-->

我們可以增加一個 subscript 函式來讓表示上自然一點:

```swift
  public subscript(i: Int) -> Bool {
    get { return isSet(i) }
    set { if newValue { set(i) } else { clear(i) } }
  }
```

<!--
Now you can write things like:
-->

現在可以這樣來操作了:

```swift
var bits = BitSet(size: 140)
bits[2] = true
bits[99] = true
bits[128] = true
print(bits)
```

<!--
This will print the three words that the 140-bit `BitSet` uses to store everything:
-->

這裡會打印出這樣:

```swift
0010000000000000000000000000000000000000000000000000000000000000
0000000000000000000000000000000000010000000000000000000000000000
1000000000000000000000000000000000000000000000000000000000000000
```

<!--
Something else that's fun to do with bits is flipping them. This changes 0 into 1 and 1 into 0. Here's `flip()`:
-->

將位元翻過來挺好玩的. 就是 0 變 1, 1 變 0. 這裡示範 `flip()`:

```swift
  public mutating func flip(_ i: Int) -> Bool {
    let (j, m) = indexOf(i)
    words[j] ^= m
    return (words[j] & m) != 0
  }
```
<!--
This uses the remaining bitwise operator, exclusive-OR, to do the flipping. The function also returns the new value of the bit.
-->

這裡用了 XOR 的位元操作, 這個函式會將該位元翻面, 並且回傳改變後的值.

<!--
## Ignoring the unused bits

A lot of the `BitSet` functions are quite easy to implement. For example, `clearAll()`, which resets all the bits to 0:
-->

## 清除無效的位元

大部分 `BitSet` 的函式是很容易實作的. 舉例來說, `clearAll()` 將所有的位元設為 0:

```swift
  public mutating func clearAll() {
    for i in 0..<words.count {
      words[i] = 0
    }
  }
```

<!--
There is also `setAll()` to make all the bits 1. However, this has to deal with a subtle issue.
-->

也有個 `setAll()` 將所有位元設為 1. 不過呢, 這方法倒是有點問題.

```swift
  public mutating func setAll() {
    for i in 0..<words.count {
      words[i] = allOnes
    }
    clearUnusedBits()
  }
```

<!--
First, we copy ones into all the words in our array. The array is now:
-->

首先, 將所有的位元設為 1. 像這樣:

```swift
1111111111111111111111111111111111111111111111111111111111111111
1111111111111111111111111111111111111111111111111111111111111111
1111111111111111111111111111111111111111111111111111111111111111
```

<!--
But this is incorrect... Since we don't use most of the last word, we should leave those bits at 0:
-->

但這樣是做的... 因為有效的使用位元並沒有這麼多, 應該要把那幾個位元設程 0:

```swift
1111111111111111111111111111111111111111111111111111111111111111
1111111111111111111111111111111111111111111111111111111111111111
1111111111110000000000000000000000000000000000000000000000000000
```

<!--
Instead of 192 one-bits we now have only 140 one-bits. The fact that the last word may not be completely filled up means that we always have to treat this last word specially.

Setting those "leftover" bits to 0 is what the `clearUnusedBits()` helper function does. If the `BitSet`'s size is not a multiple of `N` (i.e. 64), then we have to clear out the bits that we're not using. If we don't do this, bitwise operations between two differently sized `BitSet`s will go wrong (an example follows).

This uses some advanced bit manipulation, so pay close attention:
-->

相較於 192 個 1 位元, 現在是 140 個 1 位元. 這種情況表示總是要對最後那幾個沒使用(無效)的位元做處理.

`clearUnusedBits()` 是將那些 "剩餘" 的位元設為 0 的作用. 如果 `BitSet` 的大小不是 `N` (64) 的倍數, 那就需要去清除這些沒使用的位元. 如果不處理這件事. 對兩個不同大小的 `BitSet` 做位元運算時就會錯誤.

這邊使用了一點進階的位元操作, 注意囉:

```swift
  private func lastWordMask() -> Word {
    let diff = words.count*N - size       // 1
    if diff > 0 {
      let mask = 1 << Word(63 - diff)     // 2
      return mask | (mask - 1)            // 3
    } else {
      return ~Word()
    }
  }

  private mutating func clearUnusedBits() {
    words[words.count - 1] &= lastWordMask()   // 4
  }  
```

<!--
Here's what it does, step-by-step:

1) `diff` is the number of "leftover" bits. In the above example that is 52 because `3*64 - 140 = 52`.

2) Create a mask that is all 0's, except the highest bit that's still valid is a 1. In our example, that would be:

	0000000000010000000000000000000000000000000000000000000000000000

3) Subtract 1 to turn it into:

	1111111111100000000000000000000000000000000000000000000000000000

and add the high bit back in to get:

	1111111111110000000000000000000000000000000000000000000000000000

There are now 12 one-bits in this word because `140 - 2*64 = 12`.

4) Finally, turn all the higher bits off. Any leftover bits in the last word are now all 0.
-->

一步一步來看:

1) `diff` 是 "剩餘" 位元的數量. 在上述的例子中是 52, 因為 `3*64 - 140 = 52`.

2) 建立一個除了最高有效位元是 1 以外都是零的遮罩. 在例子中

	0000000000010000000000000000000000000000000000000000000000000000
	
3) 將他減 1 就會變成:

	1111111111100000000000000000000000000000000000000000000000000000
	
然後再將這兩個相加得到:

	1111111111110000000000000000000000000000000000000000000000000000
	
這樣在這個 word 中就有 12 個 1 位元, 因為 `140 - 2*64 = 12`.

4) 最後, 將最後一個 word 的剩餘位元設為 0.

<!--
An example of where this is important is when you combine two `BitSet`s of different sizes. For the sake of illustration, let's take the bitwise OR between two 8-bit values:
-->

這裡是組合兩個不同大小 `BitSet` 的例子. 來看看 OR 位元運算對於兩個 8 位元的值:

	10001111  size=4
	00100011  size=8

<!--
The first one only uses the first 4 bits; the second one uses 8 bits. The first one should really be `10000000` but let's pretend we forgot to clear out those 1's at the end. Then a bitwise OR between the two results in:
-->

第一個直應該只有前 4 個 位元有效; 第二個值 8 個位元都有效. 所以第一個直其實應該是 `10000000` 但是讓我們假裝忘記去清除無效位元. 所以這個運算會變成:

	10001111  
	00100011  
	-------- OR
	10101111

<!--
That is wrong since two of those 1-bits aren't supposed to be here. The correct way to do it is:
-->

當然, 這個直是錯誤的. 正確的解法是:

	10000000       unused bits set to 0 first!
	00100011  
	-------- OR
	10100011

<!--
Here's how the `|` operator is implemented:
-->

這裡是 `|` 運算子的實作:

```swift
public func |(lhs: BitSet, rhs: BitSet) -> BitSet {
  var out = copyLargest(lhs, rhs)
  let n = min(lhs.words.count, rhs.words.count)
  for i in 0..<n {
    out.words[i] = lhs.words[i] | rhs.words[i]
  }
  return out
}
```

<!--
Note that we `|` entire words together, not individual bits. That would be way too slow! We also need to do some extra work if the left-hand side and right-hand side have a different number of bits: we copy the largest of the two `BitSet`s into the `out` variable and then combine it with the words from the smaller `BitSet`.

Example:
-->

注意到我們 `|` 整個 word, 不是只有針對個別的位元, 那樣太慢了! 我們也要特別處理如果兩邊的值有不同數量的位元: 先複製最大個那個 `BitSet` 到變數 `out`, 然後再將它跟相對小的 `Bitset` 做組合.

例子:


```swift
var a = BitSet(size: 4)
a.setAll()
a[1] = false
a[2] = false
a[3] = false
print(a)

var b = BitSet(size: 8)
b[2] = true
b[6] = true
b[7] = true
print(b)

let c = a | b
print(c)        // 1010001100000000...0
```

<!--
Bitwise AND (`&`), exclusive-OR (`^`), and inversion (`~`) are implemented in a similar manner.
-->

其他運算子如 AND (`&`), XOR (`^`) 和 INV(`~`) 皆可用類似方法實作.
<!--
## Counting the number of 1-bits

To count the number of bits that are set to 1 we could scan through the entire array -- an **O(n)** operation -- but there's a more clever method:
-->

## 1 位元的數量

我們可以掃描整個陣列來計算 1 位元的數量 -- 一個 **O(n)** 操作 -- 但是有更聰明的方法:

```swift
  public var cardinality: Int {
    var count = 0
    for var x in words {
      while x != 0 {
        let y = x & ~(x - 1)  // find lowest 1-bit
        x = x ^ y             // and erase it
        ++count
      }
    }
    return count
  }
```
<!--
When you write `x & ~(x - 1)`, it gives you a new value with only a single bit set. This is the lowest bit that is one. For example take this 8-bit value (again, I'm showing this with the least significant bit on the left):
-->

當你執行 `x & ~(x - 1)`, 它回傳一個只有一個 1 個 1 位元的值. 其中的 1 位元, 就是最低 1 位元. 以一個 8 位元的值來說 (這裡一樣是用 little endian 來表示):

	00101101
<!--
First we subtract 1 to get:
-->
首先我們減 1:

	11001101

<!--
Then we invert it, flipping all the bits:
-->

然後我們反轉它:

	00110010

<!--
And take the bitwise AND with the original value:
-->

然後把它跟原本的值做 AND 運算:

	00101101
	00110010
	-------- AND
	00100000

<!--
The only value they have in common is the lowest (or least significant) 1-bit. Then we erase that from the original value using exclusive-OR:
-->

最後的結果是只有最小 1 位元的值, 我們藉由 XOR 運算來將它設為 0.

	00101101
	00100000
	-------- XOR
	00001101

<!--
This is the original value but with the lowest 1-bit removed.

We keep repeating this process until the value consists of all zeros. The time complexity is **O(s)** where **s** is the number of 1-bits.
-->

這就是原始值移除最小 1 位元後得結果.

我們一直重複做以上動作直到整個值都為 0. 這個時間複雜度是 **n(s)**, **s** 是 1 位元的數量.

<!--
## See also
-->

## 相關閱讀

[Bit Twiddling Hacks](http://graphics.stanford.edu/~seander/bithacks.html)

*Written for Swift Algorithm Club by Matthijs Hollemans*
