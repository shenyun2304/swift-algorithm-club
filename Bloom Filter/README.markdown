# Bloom Filter (布隆過濾器)

<!--
## Introduction

A Bloom Filter is a space-efficient data structure that tells you whether or not an element is present in a set.

This is a probabilistic data structure: a query to a Bloom filter either returns `false`, meaning the element is definitely not in the set, or `true`, meaning that the element *might* be in the set.

There is a small probability of false positives, where the element isn't actually in the set even though the query returned `true`. But there will never any false negatives: you're guaranteed that if the query returns `false`, then the element really isn't in the set.

So a Bloom Filter tells you, "definitely not" or "probably yes".

At first, this may not seem too useful. However, it's important in applications like cache filtering and data synchronization.

An advantage of the Bloom Filter over a hash table is that the former maintains constant memory usage and constant-time insert and search. For sets with a large number of elements, the performance difference between a hash table and a Bloom Filter is significant, and it is a viable option if you do not need the guarantee of no false positives.

> **Note:** Unlike a hash table, the Bloom Filter does not store the actual objects. It just remembers what objects you’ve seen (with a degree of uncertainty) and which ones you haven’t.
-->



## 介紹

布隆過濾器是一個節省空間的資料結構, 可以判定哪些元素是否在集合中.

這是一個可能性的資料結構: 對於布隆過濾器的查詢不是回傳 `false` 表示該元素不再集合中, 就是回傳 `true` 表示元素存在於集合.

是有可能有假陽性的回傳結果, 意思是有可能元素不存在於集合之中, 但是回傳 `true`. 但是絕對不會有假陰性: 當回傳 `false` 的時候絕對可以確定元素不存在集合中.

所以布隆過濾器可以告訴你 "絕對沒有" 或 "可能有".

乍看之下, 這好像不是很有用. 不過這在快取過濾跟資料同步議題中很重要.

如果把布隆過濾器和雜湊表來比較, 前者可以保持常數的記憶體資源和常數的插入和搜尋時間. 當集合中的元素數量越來越大時, 布羅過濾器和雜湊表在效能上的表現會越來越明顯, 如果對於假陽性結果不是很在意, 那這結構是個可行的選項.

> **注意:** 和雜湊表不同的是, 布隆過濾器並不會儲存真正的物件. 他只會記得哪些元素你曾經看過（有一點不確定性) 和哪些不曾.

<!--
## Inserting objects into the set

A Bloom Filter is essentially a fixed-length [bit vector](../Bit Set/), an array of bits. When we insert objects, we set some of these bits to `1`, and when we query for objects we check if certain bits are `0` or `1`. Both operations use hash functions.

To insert an element in the filter, the element is hashed with several different hash functions. Each hash function returns a value that we map to an index in the array. We then set the bits at these indices to `1` or true.

For example, let's say this is our array of bits. We have 17 bits and initially they are all `0` or false:

	[ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ]

Now we want to insert the string `"Hello world!"` into the Bloom Filter. We apply two hash functions to this string. The first one gives the value 1999532104120917762. We map this hash value to an index into our array by taking the modulo of the array length: `1999532104120917762 % 17 = 4`. This means we set the bit at index 4 to `1` or true:

	[ 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ]

Then we hash the original string again but this time with a different hash function. It gives the hash value 9211818684948223801. Modulo 17 that is 12, and we set the bit at index 12 to `1` as well:

	[ 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0 ]

These two 1-bits are enough to tell the Bloom Filter that it now contains the string `"Hello world!"`. Of course, it doesn't contain the actual string, so you can't ask the Bloom Filter, "give me a list of all the objects you contain". All it has is a bunch of ones and zeros.
-->

## 插入物件到集合

布隆過濾器基本上是一個固定長度的位元陣列 -- [位元集](../Bit Set). 當插入一個物件時, 我們將某些位元設定成 `1`, 而查詢一個陣列是否存在於集合中其實是查詢某些位元是 `0` 或 `1`. 無論插入或查詢都使用雜湊函式.

要插入一元素時, 該元素已經被好幾個不同的雜湊函式雜湊化了. 每個雜湊函式回傳一個映射的陣列索引. 然後我們將這些索引所指向的值設定為 `1` 或 `true`.

舉個例子, 這是我們的位元陣列. 我們有 17 個位元而且初始值為 `0` 或者 `false`:

	[ 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ]

現在把字串 `"Hello world!"` 插入到布隆過濾器. 我們應用兩個雜湊函式來處理. 第一個回傳 1999532104120917762. 我們將這個值當作索引取餘數 `1999532104120917762 % 17 = 4` 映射到陣列中. 這表示我們將陣列中索引 4 的值設為 `1` 或 `true`:

	[ 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ]

然後我在使用另一個雜湊函式來雜湊我麼的字串. 這次得到 9211818684948223801. 取餘數後得到 12. 同樣的將索引 12 的值設為 `1`:

	[ 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0 ]

這兩個 1 位元已經足夠讓布隆過濾器知道它現在含有 `"Hello world!"`. 當然, 這裡並不包含真正的字串內容. 所以無法向布隆過濾器做 "給我你所有物件的列表" 這種操作. 因為裡面只有 0 和 1.

<!--
## Querying the set

Querying, similarly to inserting, is accomplished by first hashing the expected value, which gives several array indices, and then checking to see if all of the bits at those indices are `1`. If even one of the bits is not `1`, the element could not have been inserted and the query returns `false`. If all the bits are `1`, the query returns `true`.

For example, if we query for the string `"Hello WORLD"`, then the first hash function returns 5383892684077141175, which modulo 17 is 12. That bit is `1`. But the second hash function gives 5625257205398334446, which maps to array index 9. That bit is `0`. This means the string `"Hello WORLD"` is not in the filter and the query returns `false`.

The fact that the first hash function mapped to a `1` bit is a coincidence (it has nothing to do with the fact that both strings start with `"Hello "`). Too many such coincidences can lead to "collisions". If there are collisions, the query may erroneously return `true` even though the element was not inserted -- bringing about the issue with false positives mentioned earlier.

Let's say we insert some other element, `"Bloom Filterz"`, which sets bits 7 and 9. Now the array looks like this:

	[ 0, 0, 0, 0, 1, 0, 0, 1, 0, 1, 0, 0, 1, 0, 0, 0, 0 ]

If you query for `"Hello WORLD"` again, the filter sees that bit 12 is true and bit 9 is now true as well. It reports that `"Hello WORLD"` is indeed present in the set, even though it isn't... because we never inserted that particular string. It's a false positive. This example shows why a Bloom Filter will never say, "definitely yes", only "probably yes".

You can fix such issues by using an array with more bits and using additional hash functions. Of course, the more hash functions you use the slower the Bloom Filter will be. So you have to strike a balance.

Deletion is not possible with a Bloom Filter, since any one bit might belong to multiple elements. Once you add an element, it's in there for good.

Performance of a Bloom Filter is **O(k)** where **k** is the number of hashing functions.
-->

## 查詢

就像插入一樣, 經過每個雜湊函式依序取餘數後得到每個索引值, 然後檢查這些索引在陣列中的值, 如果全部都是 `1` 則回傳 `true`, 如果其中有任何一個不是 `1`, 就回傳 `false`.

舉例來說, 如果查詢 `"Hello WORLD"`, 那第一個雜湊函式回傳 5383892684077141175, 對 17 取餘數後得到 12. 在陣列索引 12 的值是 `1`. 而第二個雜湊函式是 5625257205398334446, 取餘數後得到 9. 在陣列中第 9 個值是 `0`. 這表示 `"Hello WORLD"` 這個字串不存在集合中, 所以查詢結果會回傳 `false`.

事實上第一個雜湊函式映射後得到 `1` 是一個巧合 (這跟兩個字串開頭都是 `"Hello "` 是無關的). 太多的這種巧合可能會發生 "碰撞". 當有碰撞發生的時候, 查詢動作就會錯誤的回傳 `true` 就算該元素並沒有在陣列中 -- 這就是假陽性回傳的情況.

讓我們插入其他元素看看, `"Bloom Filterz"`, 雜湊處理後得到 7 和 9. 所以現在陣列變這樣:

	[ 0, 0, 0, 0, 1, 0, 0, 1, 0, 1, 0, 0, 1, 0, 0, 0, 0 ]

這時候如果再查詢 `"Hello WORLD"`, 過濾器判斷第 12 和第 9 個元素為 `1`, 就回傳 `true` 了.

這就造成了假陽性的錯誤. 所以才說布隆過濾器不能判斷 "一定存在", 只能判斷 "可能存在".

你可以藉由更大的陣列和更多的雜湊函式來解決這個問題. 當然囉, 越多的雜湊過程, 布隆過濾器效率就會越慢, 所以你必須去調整這個平衡.

在布隆過濾器裡面刪除是不可能的, 因為每個位元可能代表很多個物件. 所以當你插入了某個物件, 該位元就永遠在那.

布隆過濾器的時間複雜度是 **O(k)**, **k** 是雜湊函式的數量.

<!--
## The code

The code is quite straightforward. The internal bit array is set to a fixed length on initialization, which cannot be mutated once it is initialized.

```swift
public init(size: Int = 1024, hashFunctions: [(T) -> Int]) {
	self.array = [Bool](repeating: false, count: size)
  self.hashFunctions = hashFunctions
}
```

Several hash functions should be specified at initialization. Which hash functions you use will depend on the datatypes of the elements you'll be adding to the set. You can see some examples in the playground and the tests -- the `djb2` and `sdbm` hash functions for strings.

Insertion just flips the required bits to `true`:

```swift
public func insert(_ element: T) {
  for hashValue in computeHashes(element) {
    array[hashValue] = true
  }
}
```

This uses the `computeHashes()` function, which loops through the specified `hashFunctions` and returns an array of indices:

```swift
private func computeHashes(_ value: T) -> [Int] {
  return hashFunctions.map() { hashFunc in abs(hashFunc(value) % array.count) }
}
```

And querying checks to make sure the bits at the hashed values are `true`:

```swift
public func query(_ value: T) -> Bool {
  let hashValues = computeHashes(value)
  let results = hashValues.map() { hashValue in array[hashValue] }
	let exists = results.reduce(true, { $0 && $1 })
  return exists
}
```

If you're coming from another imperative language, you might notice the unusual syntax in the `exists` assignment. Swift makes use of functional paradigms when it makes code more consise and readable, and in this case `reduce` is a much more consise way to check if all the required bits are `true` than a `for` loop.

-->

## 程式碼

這程式碼滿直觀的. 內部的位元陣列在初始化時就固定了大小, 不能再變動了.

```swift
public init(size: Int = 1024, hashFunctions: [(T) -> Int]) {
	self.array = [Bool](repeating: false, count: size)
  self.hashFunctions = hashFunctions
}
```

雜湊函式也應該在初始化的時候決定好. 要使用哪種雜湊函式是依據你要儲存的物件型態來決定. 你可以在 playground 和 test 中找到一些範例 -- `djb2` 和 `sdbm` 字串雜湊函式.


插入就只是將指定的位元設定為 `true`:

```swift
public func insert(_ element: T) {
  for hashValue in computeHashes(element) {
    array[hashValue] = true
  }
}
```

<!--
This uses the `computeHashes()` function, which loops through the specified `hashFunctions` and returns an array of indices:
-->

這裡使用了 `computeHashes()` 函式, 它將物件經由 `hashFunctions` 轉換並回傳一堆索引:

```swift
private func computeHashes(_ value: T) -> [Int] {
  return hashFunctions.map() { hashFunc in abs(hashFunc(value) % array.count) }
}
```

<!--
And querying checks to make sure the bits at the hashed values are `true`:
-->

查詢:

```swift
public func query(_ value: T) -> Bool {
  let hashValues = computeHashes(value)
  let results = hashValues.map() { hashValue in array[hashValue] }
	let exists = results.reduce(true, { $0 && $1 })
  return exists
}
```

<!--
If you're coming from another imperative language, you might notice the unusual syntax in the `exists` assignment. Swift makes use of functional paradigms when it makes code more consise and readable, and in this case `reduce` is a much more consise way to check if all the required bits are `true` than a `for` loop.
-->

如果你撰寫指令式語言, 你會發現 `exists` 有很奇怪的宣告. Swift 增強了匿名函式的簡潔與可讀性, 使用 `reduce` 會比用 `for` 來得更簡潔.


*Written for Swift Algorithm Club by Jamil Dhanani. Edited by Matthijs Hollemans.*
