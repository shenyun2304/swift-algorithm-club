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

## Ignoring the unused bits

A lot of the `BitSet` functions are quite easy to implement. For example, `clearAll()`, which resets all the bits to 0:

```swift
  public mutating func clearAll() {
    for i in 0..<words.count {
      words[i] = 0
    }
  }
```

There is also `setAll()` to make all the bits 1. However, this has to deal with a subtle issue.

```swift
  public mutating func setAll() {
    for i in 0..<words.count {
      words[i] = allOnes
    }
    clearUnusedBits()
  }
```

First, we copy ones into all the words in our array. The array is now:

```swift
1111111111111111111111111111111111111111111111111111111111111111
1111111111111111111111111111111111111111111111111111111111111111
1111111111111111111111111111111111111111111111111111111111111111
```

But this is incorrect... Since we don't use most of the last word, we should leave those bits at 0:

```swift
1111111111111111111111111111111111111111111111111111111111111111
1111111111111111111111111111111111111111111111111111111111111111
1111111111110000000000000000000000000000000000000000000000000000
```

Instead of 192 one-bits we now have only 140 one-bits. The fact that the last word may not be completely filled up means that we always have to treat this last word specially.

Setting those "leftover" bits to 0 is what the `clearUnusedBits()` helper function does. If the `BitSet`'s size is not a multiple of `N` (i.e. 64), then we have to clear out the bits that we're not using. If we don't do this, bitwise operations between two differently sized `BitSet`s will go wrong (an example follows).

This uses some advanced bit manipulation, so pay close attention:

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

An example of where this is important is when you combine two `BitSet`s of different sizes. For the sake of illustration, let's take the bitwise OR between two 8-bit values:

	10001111  size=4
	00100011  size=8

The first one only uses the first 4 bits; the second one uses 8 bits. The first one should really be `10000000` but let's pretend we forgot to clear out those 1's at the end. Then a bitwise OR between the two results in:

	10001111  
	00100011  
	-------- OR
	10101111

That is wrong since two of those 1-bits aren't supposed to be here. The correct way to do it is:

	10000000       unused bits set to 0 first!
	00100011  
	-------- OR
	10100011

Here's how the `|` operator is implemented:

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

Note that we `|` entire words together, not individual bits. That would be way too slow! We also need to do some extra work if the left-hand side and right-hand side have a different number of bits: we copy the largest of the two `BitSet`s into the `out` variable and then combine it with the words from the smaller `BitSet`.

Example:

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

Bitwise AND (`&`), exclusive-OR (`^`), and inversion (`~`) are implemented in a similar manner.

## Counting the number of 1-bits

To count the number of bits that are set to 1 we could scan through the entire array -- an **O(n)** operation -- but there's a more clever method:

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

When you write `x & ~(x - 1)`, it gives you a new value with only a single bit set. This is the lowest bit that is one. For example take this 8-bit value (again, I'm showing this with the least significant bit on the left):

	00101101

First we subtract 1 to get:

	11001101

Then we invert it, flipping all the bits:

	00110010

And take the bitwise AND with the original value:

	00101101
	00110010
	-------- AND
	00100000

The only value they have in common is the lowest (or least significant) 1-bit. Then we erase that from the original value using exclusive-OR:

	00101101
	00100000
	-------- XOR
	00001101

This is the original value but with the lowest 1-bit removed.

We keep repeating this process until the value consists of all zeros. The time complexity is **O(s)** where **s** is the number of 1-bits.

## See also

[Bit Twiddling Hacks](http://graphics.stanford.edu/~seander/bithacks.html)

*Written for Swift Algorithm Club by Matthijs Hollemans*
