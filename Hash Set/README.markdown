# Hash Set (雜湊集)

<!--
A set is a collection of elements that is kind of like an array but with two important differences: the order of the elements in the set is unimportant and each element can appear only once.

If the following were arrays, they'd all be different. However, they all represent the same set:
-->

集合有點類似陣列, 但是有兩個關鍵的不同: 元素是無序的, 每種元素只會出現一次.

下列皆為不同陣列, 但是如果以集合觀點來看, 都是同一個集合:

```swift
[1 ,2, 3]
[2, 1, 3]
[3, 2, 1]
[1, 2, 2, 3, 1]
```

<!--
Because each element can appear only once, it doesn't matter how often you write the element down -- only one of them counts.

> **Note:** I often prefer to use sets over arrays when I have a collection of objects but don't care what order they are in. Using a set communicates to the programmer that the order of the elements is unimportant. If you're using an array, then you can't assume the same thing.
-->

因為每個元素只能算出現一次, 所以無論寫了幾次都只能算一次.

> **注意:** 如果不在意元素的順序, 比起陣列我會比較喜歡用集合. 使用集合可以告知程式員元素的順序不重要, 如果是用陣列, 就不能表達出來.

<!--
Typical operations on a set are:

- insert an element
- remove an element
- check whether the set contains an element
- take the union with another set
- take the intersection with another set
- calculate the difference with another set

Union, intersection, and difference are ways to combine two sets into a single one:
-->

典型的集合操作:

- 插入元素
- 移除元素
- 檢查元素是否存在於集合中
- 與其他集合做聯集
- 與其他集合做交集
- 與其他集合計算差集

聯集, 交集, 差集是將兩個集合組合成一個集合的方式:

![Union, intersection, difference](Images/CombineSets.png)

<!--
As of Swift 1.2, the standard library includes a built-in `Set` type but here I'll show how you can make your own. You wouldn't use this in production code, but it's instructive to see how sets are implemented.

It's possible to implement a set using a simple array but that's not the most efficient way. Instead, we'll use a dictionary. Since `Swift`'s dictionary is built using a hash table, our own set will be a hash set.
-->

在 Swift 1.2 的標準函式庫中已經有 `Set` 型態, 但這裡我們自己做一個. 你不會在專案上使用這些程式碼, 但這可以讓你了解集合的程式結構.

是可以用一個簡單的陣列來實作集合啦. 但那並不是最有效率的方法. 相對的, 我們使用字典. 因為 `Swift` 的字典是以雜湊表來創建的, 所以我們的集合將會是個雜湊集合.

<!--
## The code

Here are the beginnings of `HashSet` in Swift:
-->

## 程式碼

`HashSet` 的起點:

```swift
public struct HashSet<T: Hashable> {
    fileprivate var dictionary = Dictionary<T, Bool>()

    public init() {

    }

    public mutating func insert(_ element: T) {
        dictionary[element] = true
    }

    public mutating func remove(_ element: T) {
        dictionary[element] = nil
    }

    public func contains(_ element: T) -> Bool {
        return dictionary[element] != nil
    }

    public func allElements() -> [T] {
        return Array(dictionary.keys)
    }

    public var count: Int {
        return dictionary.count
    }

    public var isEmpty: Bool {
        return dictionary.isEmpty
    }
}
```


<!--
The code is really very simple because we rely on Swift's built-in `Dictionary` to do all the hard work. The reason we use a dictionary is that dictionary keys must be unique, just like the elements from a set. In addition, a dictionary has **O(1)** time complexity for most of its operations, making this set implementation very fast.

Because we're using a dictionary, the generic type `T` must conform to `Hashable`. You can put any type of object into our set, as long as it can be hashed. (This is true for Swift's own `Set` too.)

Normally, you use a dictionary to associate keys with values, but for a set we only care about the keys. That's why we use `Bool` as the dictionary's value type, even though we only ever set it to `true`, never to `false`. (We could have picked anything here but booleans take up the least space.)

Copy the code to a playground and add some tests:
-->

我們使用 Swift 內建的 `Dictionary` 來做, 讓程式碼便的非常簡單. 我們使用字典因為字典中的鍵值必須是唯一的, 就像是集合中的元素. 再者, 字典有 **O(1)** 的時間複雜度, 讓此集合的實作非常快速.

基於使用字典, 所以泛型 `T` 必須是符合 `Hashable` 的型別. 你可以放任何可以被雜湊的物件當作字典的鍵值. (和 Swift 的 `Set` 一樣)

正常來說, 你用字典是要建立鍵值和內容值的關係, 但是在集合中我們只在意鍵值. 這就是為什麼我們使用 `Bool` 當作內容值的型別, 就算我們只會把它設為 `true` 而永遠不會設為 `false`. (其實我們可以隨意選取任何內容值的型別, 只是布林值所佔的空間最小.)

在 playground 中試試看吧:


```swift
var set = HashSet<String>()

set.insert("one")
set.insert("two")
set.insert("three")
set.allElements()      // ["one, "three", "two"]

set.insert("two")
set.allElements()      // still ["one, "three", "two"]

set.contains("one")    // true
set.remove("one")
set.contains("one")    // false
```

<!--
The `allElements()` function converts the contents of the set into an array. Note that the order of the elements in that array can be different than the order in which you added the items. As I said, a set doesn't care about the order of the elements (and neither does a dictionary).
-->

`allElements()` 函式將集合中的元素放到陣列中. 注意到陣列中元素的順序可能和你插入元素的順序不同. 像之前提的, 集合不管元素的順序. (字典也是)

<!--
## Combining sets

A lot of the usefulness of sets is in how you can combine them. (If you've ever used a vector drawing program like Sketch or Illustrator, you'll have seen the Union, Subtract, Intersect options to combine shapes. Same thing.)

Here is the code for the union operation:
-->

## 集合組合

集合另一個好用的地方是你可以組合他們. (如果你曾經用過繪製向量圖的程式像是 Sketch 或 Illustrator, 你會在圖形的操作上看到像是 聯集, 差集, 交集的選項. 一樣的東西.)

這是聯集操作的程式碼:

```swift
extension HashSet {
    public func union(_ otherSet: HashSet<T>) -> HashSet<T> {
        var combined = HashSet<T>()
        for obj in self.dictionary.keys {
            combined.insert(obj)
        }
        for obj in otherSet.dictionary.keys {
            combined.insert(obj)
        }
        return combined
    }
}
```

<!--
The *union* of two sets creates a new set that consists of all the elements in set A plus all the elements in set B. Of course, if there are duplicate elements they count only once.

Example:
-->

兩個集合的 *聯集* 是另一個集合, 內含的元素是在 A 或 B 中的元素. 當然, 如果他們有重複, 也只能算一個.

範例:

```swift
var setA = HashSet<Int>()
setA.insert(1)
setA.insert(2)
setA.insert(3)
setA.insert(4)

var setB = HashSet<Int>()
setB.insert(3)
setB.insert(4)
setB.insert(5)
setB.insert(6)

let union = setA.union(setB)
union.allElements()           // [5, 6, 2, 3, 1, 4]
```

<!--
As you can see, the union of the two sets contains all of the elements now. The values `3` and `4` still appear only once, even though they were in both sets.

The *intersection* of two sets contains only the elements that they have in common. Here is the code:
-->

可以看到, 兩個集合的聯集現在含有所有的元素. `3` 和 `4` 當然只能出現一次, 就算他們在兩個集合中都存在.

兩個集合的 *交集* 是一個內含元素是同時存在於 A 與 B 中的元素. 程式碼如下:

```swift
extension HashSet {
    public func intersect(_ otherSet: HashSet<T>) -> HashSet<T> {
        var common = HashSet<T>()
        for obj in dictionary.keys {
            if otherSet.contains(obj) {
                common.insert(obj)
            }
        }
        return common
    }
}
```

<!--
To test it:
-->

測試一下:

```swift
let intersection = setA.intersect(setB)
intersection.allElements()
```

<!--
This prints `[3, 4]` because those are the only objects from set A that are also in set B.

Finally, the *difference* between two sets removes the elements they have in common. The code is as follows:
-->

這會印出 `[3, 4]`, 因為他們是唯二的元素同時存在於 A 與 B 之中.

最後, 兩個集合的 *差集* 是移除兩個集合共同有的元素. 程式碼如下:

```swift
extension HashSet {
    public func difference(_ otherSet: HashSet<T>) -> HashSet<T> {
        var diff = HashSet<T>()
        for obj in dictionary.keys {
            if !otherSet.contains(obj) {
                diff.insert(obj)
            }
        }
        return diff
    }
}
```

<!--
It's really the opposite of `intersect()`. Try it out:
-->

其實就是 `intersect()` 的相對操作. 試試看:

```swift
let difference1 = setA.difference(setB)
difference1.allElements()                // [2, 1]

let difference2 = setB.difference(setA)
difference2.allElements()                // [5, 6]
```

<!--
## Where to go from here?

If you look at the [documentation](http://swiftdoc.org/v2.1/type/Set/) for Swift's own `Set`, you'll notice it has tons more functionality. An obvious extension would be to make `HashSet` conform to `SequenceType` so that you can iterate it with a `for`...`in` loop.

Another thing you could do is replace the `Dictionary` with an actual [hash table](../Hash Table), but one that just stores the keys and doesn't associate them with anything. So you wouldn't need the `Bool` values anymore.

If you often need to look up whether an element belongs to a set and perform unions, then the [union-find](../Union-Find/) data structure may be more suitable. It uses a tree structure instead of a dictionary to make the find and union operations very efficient.

> **Note:** I'd like to make `HashSet` conform to `ArrayLiteralConvertible` so you can write `let setA: HashSet<Int> = [1, 2, 3, 4]` but currently this crashes the compiler.
-->

## 接下來呢?

如果你查看 Swift 原生 `Set` 的[文件](http://swiftdoc.org/v2.1/type/Set/), 你會發現它有更多的功能. 一個明顯的擴展是將 `HashSet` 實現 `SequenceType`, 這樣你就可以使用 `for`...`in` 來迭代.

你也可以用 [hash table](../Hash Table) 來取代 `Dictionary`, 這個只儲存鍵值而沒有內容值. 所以你不再需要 `Bool` 內容值.

如果你需要時常的檢查集合內或聯集後的是否存在某些元素, 那 [聯集查找](../Union-Find/) 結構可能更適合. 它使用了樹狀結構而不使用字典, 讓查找和聯集操作更效率.

> **注意:** 我喜歡將 `HashSet` 實現 `ArrayLiteralConvertible`, 這樣你就可以像這樣寫 `let setA: HashSet<Int> = [1, 2, 3, 4]`, 但目前這樣做會把編譯器搞壞.

*Written for Swift Algorithm Club by Matthijs Hollemans*
