# Hash Table (雜湊表)

<!--
A hash table allows you to store and retrieve objects by a "key".

Also called dictionary, map, associative array. There are other ways to implement these, such as with a tree or even a plain array, but hash table is the most common.

This should explain why Swift's built-in `Dictionary` type requires that keys conform to the `Hashable` protocol: internally it uses a hash table, just like the one you'll learn about here.
-->

雜湊表可以儲存和透過鍵值來獲得物件.

字典, 地圖, 關聯事陣列. 有很多方法來實作, 可以用樹或甚至一般陣列, 但最常使用的還是雜湊表.

這也解釋了 Swift 內建的 `Dictionary` 型別, 鍵值需要實現 `Hashable` 協定: 在內部使用了一個雜湊表, 你會在這裡學到.

<!--
## How it works

At its most basic, a hash table is nothing more than an array. Initially, this array is empty. When you put a value into the hash table under a certain key, it uses that key to calculate an index in the array, like so:
-->

## 如何運作

在基礎上, 雜湊表就是個陣列. 初始化時, 陣列是空的. 當你插入了一個有鍵值的元素, 它使用該鍵值計算一個陣列中的索引, 像這樣:

```swift
hashTable["firstName"] = "Steve"

	The hashTable array:
	+--------------+
	| 0:           |
	+--------------+
	| 1:           |
	+--------------+
	| 2:           |
	+--------------+
	| 3: firstName |---> Steve
	+--------------+
	| 4:           |
	+--------------+
```

<!--
In this example, the key `"firstName"` maps to array index 3.

Adding a value under a different key puts it at another array index:
-->

在此例中, `"firstName"` 映射到索引 3.

插入另一個含有鍵值的元素會將它放到另一個索引:

```swift
hashTable["hobbies"] = "Programming Swift"

	The hashTable array:
	+--------------+
	| 0:           |
	+--------------+
	| 1: hobbies   |---> Programming Swift
	+--------------+
	| 2:           |
	+--------------+
	| 3: firstName |---> Steve
	+--------------+
	| 4:           |
	+--------------+
```

<!--
The trick is in how the hash table calculates those array indices. That's where the hashing comes in. When you write,
-->

現在問題就在於如何將鍵值轉換成陣列索引. 這就是雜湊的地方. 當你撰寫, 

```swift
hashTable["firstName"] = "Steve"
```

<!--
the hash table takes the key `"firstName"` and asks it for its `hashValue` property. That's why keys must be `Hashable`. 

When you do `"firstName".hashValue`, it returns a big integer: -4799450059917011053. Likewise, `"hobbies".hashValue` has the hash value 4799450060928805186. (The values you see may vary.)

Of course, these numbers are way too big to be used as indices into our array. One of them is even negative! A common way to make these big numbers more suitable is to first make the hash positive and then take the modulo with the array size.

Our array has size 5, so the index for the `"firstName"` key becomes `abs(-4799450059917011053) % 5 = 3`. You can calculate for yourself that the array index for `"hobbies"` is 1.

Using hashes in this manner is what makes the dictionary so efficient: to find an element in the hash table you only have to hash the key to get an array index and then look up the element in the underlying array. All these operations take a constant amount of time, so inserting, retrieving, and removing are all **O(1)**.

> **Note:** As you can see, it's hard to predict where in the array your objects end up. That's why dictionaries do not guarantee any particular order of the elements in the hash table.
-->

雜湊表訪問 `"firstName"` 的 `hashValue` 屬性值. 這就是為什麼鍵值必須是要 `Hashable`.

當你這樣做 `"firstName".hashValue`, 將會回傳一個很大的整數像是 -4799450059917011053 之類的. `"hobbies".hashValue` 可能是 4799450060928805186. (你看到的可能不同)

當然, 這些數字對陣列索引來說實在太大了. 其中一個甚至還是負數! 最簡單的索引取法就是將雜湊值改為正數, 然後對陣列大小取餘數.

我們陣列的長度是 5, 所以 `"firstName"` 的索引就是 `abs(-4799450059917011053) % 5 = 3`. 你可以自己計算一下 `"hobbies"` 的索引是 1.

這樣雜湊法對於字典來說是很有效率的: 要在雜湊表中找到你要的物件, 只需要利用鍵值獲得陣列索引, 再用索引獲得物件. 這些操作佔用的是常數時間, 所以插入, 檢索, 和刪除都是 **O(1)**.

<!--
## Avoiding collisions

There is one problem: because we take the modulo of the hash value with the size of the array, it can happen that two or more keys get assigned the same array index. This is called a collision.

One way to avoid collisions is to have a very large array. That reduces the likelihood of two keys mapping to the same index. Another trick is to use a prime number for the array size. However, collisions are bound to occur so you need some way to handle them.

Because our table is so small it's easy to show a collision. For example, the array index for the key `"lastName"` is also 3. That's a problem, as we don't want to overwrite the value that's already at this array index.

There are a few ways to handle collisions. A common one is to use chaining. The array now looks as follows:
-->
## 避免碰撞:

有一個問題: 我們使用取餘來獲得陣列索引, 但是有可能發生兩個鍵值會計算出相同索引的情況. 另一個技巧是將陣列長度設為質數. 無論如何, 碰撞還是可能發生, 所以需要去處理這問題.

因為我們的表很小, 所以很容易展現碰撞. 舉個例子, `"lastName"` 的索引也一樣是 3. 這就是個問題了, 我們並不想要覆寫原本在索引 3 的內容. 

有幾個處理碰撞的方法. 常見的是使用鏈結. 陣列現在像是這樣:


```swift
	buckets:
	+-----+
	|  0  |
	+-----+     +----------------------------+
	|  1  |---> | hobbies: Programming Swift |
	+-----+     +----------------------------+
	|  2  |
	+-----+     +------------------+     +----------------+
	|  3  |---> | firstName: Steve |---> | lastName: Jobs |
	+-----+     +------------------+     +----------------+
	|  4  |
	+-----+
```

<!--
With chaining, keys and their values are not stored directly in the array. Instead, each array element is really a list of zero or more key/value pairs. The array elements are usually called the *buckets* and the lists are called the *chains*. So here we have 5 buckets and two of these buckets have chains. The other three buckets are empty.

If we now write the following to retrieve an item from the hash table,
-->

在鏈結方法下, 內容值並不會直接儲存在鍵值計算出的索引下. 相反的, 每個陣列元素其實是個鍵 / 值的列表. 這樣的陣列元素通常稱作 *籃子* 而列表稱為 *鏈*. 所以我們有 5 個籃子, 而其中有兩個籃子有鏈. 其他三個籃子是空的.

如果我們現在像這樣來獲得元素:

```swift
let x = hashTable["lastName"]
```

<!--
then this first hashes the key `"lastName"` to calculate the array index, which is 3. Bucket 3 has a chain, so we step through that list to find the value with the key `"lastName"`. That is done by comparing the keys, so here that involves a string comparison. The hash table sees that this key belongs to the last item in the chain and returns the corresponding value, `"Jobs"`.

Common ways to implement this chaining mechanism are to use a linked list or another array. Technically speaking the order of the items in the chain doesn't matter, so you also can think of it as a set instead of a list. (Now you can also imagine where the term "bucket" comes from; we just dump all the objects together into the bucket.)

It's important that chains do not become too long or looking up items in the hash table becomes really slow. Ideally, we would have no chains at all but in practice it is impossible to avoid collisions. You can improve the odds by giving the hash table enough buckets and by using high-quality hash functions.

> **Note:** An alternative to chaining is "open addressing". The idea is this: if an array index is already taken, we put the element in the next unused bucket. Of course, this approach has its own upsides and downsides.
-->

首先取得 `"lastName"` 的雜湊值並計算出索引是 3, 索引 3 的籃子有鏈, 所以走訪列表並找出鍵值 `"lastName"`. 這動作是以字串比對來達成. 雜湊表查找到這個鍵值是該鏈中的最後一個元素, 並回傳對應的內容值, `"Jobs"`.

一般實作這個鏈機制是以鏈結列表或另一個陣列. 技術上來說, 在鏈中的元素順序並不重要, 所以你可以將該鏈想像成一個集合而不是列表. (現在你可以了解 "籃子" 的概念了吧, 我們只是單純把計算出來相同索引的元素通通放到同一個籃子裡)

鏈太大或讓在雜湊表中查詢元素變得很慢這件事是很重要的. 理想的狀況下是我們希望完全沒有鏈, 但是我們也無法完全避免碰撞. 你可以增強雜湊函數和陣列中的籃子數量來降低碰撞機率.

> **注意:** 另一種鏈的做法是 "公開位址". 這個概念是這樣的: 如果某個陣列索引已經被使用了, 我們就把元素放在下一個尚未使用的索引位置. 當然, 這方法有優點也有缺點.

<!--
## The code

Let's look at a basic implementation of a hash table in Swift. We'll build it up step-by-step.
-->

## 程式碼:

讓我們來看看在 Swift 中的雜湊表基礎的實作. 我們將一步一步的來建立.

```swift
public struct HashTable<Key: Hashable, Value> {
  private typealias Element = (key: Key, value: Value)
  private typealias Bucket = [Element]
  private var buckets: [Bucket]

  private(set) public var count = 0
  
  public var isEmpty: Bool { return count == 0 }

  public init(capacity: Int) {
    assert(capacity > 0)
    buckets = Array<Bucket>(repeatElement([], count: capacity))
  }
}
```

<!--
The `HashTable` is a generic container and the two generic types are named `Key` (which must be `Hashable`) and `Value`. We also define two other types: `Element` is a key/value pair for use in a chain and `Bucket` is an array of such `Elements`.

The main array is named `buckets`. It has a fixed size, the so-called capacity, provided by the `init(capacity)` method. We're also keeping track of how many items have been added to the hash table using the `count` variable.

An example of how to create a new hash table object:
-->

`HashTable` 是一個泛型元素的容器, 接受 `Key` (必須是 `Hashable`) 和 `Value`. 我們也同時定義了兩種型別: `Element` 是個鍵 / 值的元組, 用於鏈中; 而 `Bucket` 是 `Element` 的陣列.

真正儲存的陣列叫做 `buckets`. 固定長度, 稱為容量, 由 `init(capacity)` 方法給定. 我們也使用 `count` 變數來追蹤有多少雜湊表中有多少個元素.

創建一個雜湊表物件:

```swift
var hashTable = HashTable<String, String>(capacity: 5)
```

<!--
Currently the hash table doesn't do anything yet, so let's add the remaining functionality. First, add a helper method that calculates the array index for a given key:
-->

目前這個雜湊表還沒做任何事情, 所以讓我們把剩下的功能加進去. 第一, 加入一個計算索引的方法:


```swift
  private func index(forKey key: Key) -> Int {
    return abs(key.hashValue) % buckets.count
  }
```

<!--
This performs the calculation you saw earlier: it takes the absolute value of the key's `hashValue` modulo the size of the buckets array. We've put this in a function of its own because it gets used in a few different places.

There are four common things you'll do with a hash table or dictionary: 

- insert a new element
- look up an element
- update an existing element
- remove an element

The syntax for these is:
-->

這裡展示了方才提到的算法: 將雜湊值的絕對值後和陣列長度取餘數. 我們把這動作拉出來成為一個獨立的方法因為在其他地方也會用到相同的操作.

以下是四個一般雜湊表或字典會有的操作:

- 新增一個元素
- 查找一個元素
- 更新一個既存的元素
- 移除一個元素

這些操作的語法會像這樣:

```swift
hashTable["firstName"] = "Steve"   // insert
let x = hashTable["firstName"]     // lookup
hashTable["firstName"] = "Tim"     // update
hashTable["firstName"] = nil       // delete
```

<!--
We can do all these things with a `subscript` function:
-->

我們可以使用 `subscript` 來實現:

```swift
  public subscript(key: Key) -> Value? {
    get {
      return value(forKey: key)
    }
    set {
      if let value = newValue {
        updateValue(value, forKey: key)
      } else {
        removeValue(forKey: key)
      }
    }
  }
```

<!--
This calls three helper functions to do the actual work. Let's take a look at `value(forKey:)` first, which retrieves an object from the hash table.
-->

來看看 `value(forKey:)` 的實作, 可以獲得該鍵值的元素.

```swift
  public func value(forKey key: Key) -> Value? {
    let index = self.index(forKey: key)
    for element in buckets[index] {
      if element.key == key {
        return element.value
      }
    }
    return nil  // key not in hash table
  }
```

<!--
First it calls `index(forKey:)` to convert the key into an array index. That gives us the bucket number, but if there were collisions this bucket may be used by more than one key. So `value(forKey:)` loops through the chain from that bucket and compares the keys one-by-one. If found, it returns the corresponding value, otherwise it returns `nil`.

The code to insert a new element or update an existing element lives in `updateValue(_:forKey:)`. It's a little bit more complicated:
-->

第一行呼叫了 `index(forKey:)` 將鍵值轉換成陣列索引. 這告訴我們籃子的索引, 但是如果有碰撞產生, 那籃子內可能含有一個以上的鍵值. 所以 `value(forKey:)` 走訪了該籃子的鏈然後一個一個比對鍵值字串. 如果有找到, 回傳該元素, 若無則回傳 `nil`.

下面的程式碼顯示 `updateValue(_:forKey:)` 如何新增或更新一個既存的元素. 有一點複雜:


```swift
  public mutating func updateValue(_ value: Value, forKey key: Key) -> Value? {
    let index = self.index(forKey: key)
    
    // Do we already have this key in the bucket?
    for (i, element) in buckets[index].enumerate() {
      if element.key == key {
        let oldValue = element.value
        buckets[index][i].value = value
        return oldValue
      }
    }
    
    // This key isn't in the bucket yet; add it to the chain.
    buckets[index].append((key: key, value: value))
    count += 1
    return nil
  }
```

<!--
Again, the first thing we do is convert the key into an array index to find the bucket. Then we loop through the chain for that bucket. If we find the key in the chain, it means we must update it with the new value. If the key is not in the chain, we insert the new key/value pair to the end of the chain.

As you can see, it's important that chains are kept short (by making the hash table large enough). Otherwise, you spend a lot of time in these `for`...`in` loops and the performance of the hash table will no longer be **O(1)** but more like **O(n)**.

Removing is similar in that again it loops through the chain:
-->

相同的, 第一件事情是把鍵值轉換成陣列索引. 然後我們搜訪該索引籃子的鏈. 如果找到鍵值, 表示我們要更新該元素的值. 如果該鏈中不存在該鍵值, 則在該籃子的鏈最後加上此鍵 / 值元組.

可以看到, 保持小的鏈長度很重要 (以增大雜湊表來辦到). 另一方面, 使用大量的 `for`...`in` 迴圈表示雜湊表將不再是 **O(1)** 的操作, 而比較像是 **O(n)**.

移除也類似, 一樣要走訪鏈:

```swift
  public mutating func removeValue(forKey key: Key) -> Value? {
    let index = self.index(forKey: key)

    // Find the element in the bucket's chain and remove it.
    for (i, element) in buckets[index].enumerated() {
      if element.key == key {
        buckets[index].remove(at: i)
        count -= 1
        return element.value
      }
    }
    return nil  // key not in hash table
  }
```

<!--
These are the basic functions of the hash table. They all work the same way: convert the key into an array index using its hash value, find the bucket, then loop through that bucket's chain and perform the desired operation.

Try this stuff out in a playground. It should work just like a standard Swift `Dictionary`.
-->

這些是雜湊表的基礎功能. 他們原理都一樣: 將鍵值轉換成陣列索引, 找到該索引的籃子, 走訪該籃子的鏈然後執行指定的操作.

在 playground 中試試看. 跟 Swift 原生的 `Dictionary` 應該差不多.

<!--
## Resizing the hash table

This version of `HashTable` always uses an array of a fixed size or capacity. That's fine if you've got a good idea of many items you'll be storing in the hash table. For the capacity, choose a prime number that is greater than the maximum number of items you expect to store and you're good to go.

The *load factor* of a hash table is the percentage of the capacity that is currently used. If there are 3 items in a hash table with 5 buckets, then the load factor is `3/5 = 60%`.

If the hash table is too small and the chains are long, the load factor can become greater than 1. That's not a good idea.

If the load factor becomes too high, say > 75%, you can resize the hash table. Adding the code for this is left as an exercise for the reader. Keep in mind that making the buckets array larger will change the array indices that the keys map to! To account for this, you'll have to insert all the elements again after resizing the array.
-->

## 調整雜湊表的大小

這個版本的 `HashTable` 總是使用固定長度的陣列. 這種做法對於你已經掌握元素的數量是個好主意. 對於容量, 選一個超過最大元素數量的質數就好了.

*負載因子* 是一個目前使用的容量比例. 如果目前表中有 3 個元素, 而表共有 5 個籃子, 那這個雜湊表的負載因子是 `3/5 = 60%`.

如果雜湊表太小而鏈很長, 負載因子就可能會超過 1. 那可不是個好主意.

如果負載因子已經太高了, 假設是大於 75%, 那你可以重新調整雜湊表的大小. 這段程式碼就留給讀者當練習了. 記住讓籃子陣列增大將會改變鍵值轉換成索引的值! 為了處理這點, 你必須對調整後的陣列重新插入所有原本既存元素.

<!--
## Where to go from here?

`HashTable` is quite basic. It might be fun to integrate it better with the Swift standard library by making it a `SequenceType`, for example.
-->

## 接下來?

`HashTable` 是滿基礎的結構. 試試看將它整合 Swift 標準函式庫中的 `SequenceType`, 應該挺有趣.

*Written for Swift Algorithm Club by Matthijs Hollemans*
