# Ordered Set (有序集合)

<!--
An Ordered Set is a collection of unique items in sorted order. Items are usually sorted from least to greatest.

The Ordered Set data type is a hybrid of:

- a [Set](https://en.wikipedia.org/wiki/Set_%28mathematics%29), a collection of unique items where the order does not matter, and
- a [Sequence](https://en.wikipedia.org/wiki/Sequence), an ordered list of items where each item may appear more than once.

It's important to keep in mind that two items can have the same *value* but still may not be equal. For example, we could define "a" and "z" to have the same value (their lengths), but clearly "a" != "z".
-->

一些唯一元素依照順序儲存的集合. 通常是由小到大排序.

有序集合的資料型別是以下的混合型別:

- [集合](https://en.wikipedia.org/wiki/Set_%28mathematics%29), 元素唯一的集合, 順序不重要.
- [序列](https://en.wikipedia.org/wiki/Sequence), 一個有序的列表, 元素有可能重複.

要記得兩個元素是可以擁有一樣的 *值*, 但不一定相等. 舉個例子, 我們可以定義 "a" 與 "z" 有一樣的值 (字串長度), 但很明顯 "a" != "z".

<!--
## Why use an ordered set?

Ordered Sets should be considered when you need to keep your collection sorted at all times, and you do lookups on the collection much more frequently than inserting or deleting items. Many of the lookup operations for an Ordered Set are **O(1)**.

A good example would be keeping track of the rankings of players in a scoreboard (see example 2 below).
-->

## 為何使用有序集合?

當你需要保持集合的有續性, 而從集合中查找物件頻率比新增或刪除還來的高. 大部分查找物件在有序集合中的時間是 **O(1)**.

<!--
#### These are ordered sets

A set of integers:
-->

#### 這些是有序集合

整數集合:

	[1, 2, 3, 6, 8, 10, 1000]

<!--
A set of strings:
-->

字串集合:

	["a", "is", "set", "this"]

<!--
The "value" of these strings could be their text content, but also for example their length.
-->

這些字串元素的 "值" 可以是他們的文字內容, 也可是他們的長度.

<!--
#### These are not ordered sets

This set violates the property of uniqueness:
-->

#### 這些不是有序集合

這個集合違反了唯一性:

	[1, 1, 2, 3, 5, 8]

<!--
This set violates the sorted property:
-->

這個集合違反了有序性:

	[1, 11, 2, 3]

<!--
## The code

We'll start by creating our internal representation for the Ordered Set. Since the idea of a set is similar to that of an array, we will use an array to represent our set. Furthermore, since we'll need to keep the set sorted, we need to be able to compare the individual elements. Thus, any type must conform to the [Comparable Protocol](https://developer.apple.com/library/watchos/documentation/Swift/Reference/Swift_Comparable_Protocol/index.html).
-->

## 程式碼

我們從創建內部集合開始. 因為集合的概念和陣列相似, 我們使用陣列來代表我們的集合. 再來, 因為我們要保持集合的有序性, 我們需要兩倆比對的元素. 因此, 資料型別必須要實現 [Comparable Protocol](https://developer.apple.com/library/watchos/documentation/Swift/Reference/Swift_Comparable_Protocol/index.html).


```swift
public struct OrderedSet<T: Comparable> {
  private var internalSet = [T]()

  // Returns the number of elements in the OrderedSet.
  public var count: Int {
    return internalSet.count
  }
  ...
```

<!--
Lets take a look at the `insert()` function first. This first checks if the item already exists in the collection. If so, it returns and does not insert the item.  Otherwise, it will insert the item through straightforward iteration.
-->

先來看看 `insert()` 函式. 先檢查元素是否已經在集合中. 如果已經存在, 不做任何事情直接回傳. 如果不存在, 直接用一個迭代迴圈把元素插入.


```swift
  public mutating func insert(_ item: T){
    if exists(item) {
      return  // don't add an item if it already exists
    }

    // Insert new the item just before the one that is larger.
    for i in 0..<count {
      if internalSet[i] > item {
        internalSet.insert(item, at: i)
        return
      }
    }

    // Append to the back if the new item is greater than any other in the set.
    internalSet.append(item)
  }
```

<!--
As we'll see later on, checking if the item is already in the set has an efficiency of **O(log(n) + k)** where **k** is the number of items with the same value as the item we are inserting.

To insert the new item, the `for` loop starts from the beginning of the array, and checks to see if each item is larger than the item we want to insert. Once we find such an item, we insert the new one into its place. This shifts the rest of the array over to the right by 1 position. This loop is at worst **O(n)**.

The total performance of the `insert()` function is therefore **O(n)**.

Next up is the `remove()` function:
-->

檢查一個元素是否在集合中是 **O(log(n) + k)**, **k** 是那些和我們要搜尋的元素值相同的元素個數.

要插入一個新的元素, `for` 迴圈從陣列的開頭開始, 逐個檢查元素是否大於我們要插入的元素. 一但我們找到該元素, 我們就把新的元素插入到該位置. 之後的元素都要向右移動一個位置. 這個迴圈最糟的情況就是 **O(n)**.

`insert()` 的總體時間是 **O(n)**.

接下來是 `remove()` 函式:


```swift
  public mutating func remove(_ item: T) {
    if let index = index(of: item) {
      internalSet.remove(at: index)
    }
  }
```

<!--
First this checks if the item exists and then removes it from the array. Because of the `removeAtIndex()` function, the efficiency for remove is **O(n)**.

The next function is `indexOf()`, which takes in an object of type `T` and returns the index of the corresponding item if it is in the set, or `nil` if it is not. Since our set is sorted, we can use a binary search to quickly search for the item.
-->

先檢查這個元素是否存在集合中, 如果存在就從陣列中移除. 因為使用 `removeAtIndex()` 函式, 移除的時間複雜度是 **O(n)**.

下一個函式是 `indexOf()`, 接收 `T` 型別的參數, 如果該元素存在集合中則回傳該元素的索引, 如果不存在就回傳 `nil`. 因為集合是有序的, 我們可以使用二元搜尋來快速找到該元素.


```swift
  public func index(of item: T) -> Int? {
    var leftBound = 0
    var rightBound = count - 1

    while leftBound <= rightBound {
      let mid = leftBound + ((rightBound - leftBound) / 2)

      if internalSet[mid] > item {
        rightBound = mid - 1
      } else if internalSet[mid] < item {
        leftBound = mid + 1
      } else if internalSet[mid] == item {
        return mid
      } else {
      	// see below
      }
    }
    return nil
  }
```

<!--
> **Note:** If you are not familiar with the concept of binary search, we have an [article that explains all about it](../Binary Search).

However, there is an important issue to deal with here. Recall that two objects can be unequal yet still have the same "value" for the purposes of comparing them. Since a set can contain multiple items with the same value, it is important to check that the binary search has landed on the correct item.

For example, consider this ordered set of `Player` objects. Each `Player` has a name and a number of points:
-->

> **注意:** 如果你對二元搜尋的觀念不熟, 我們有 [一篇專門解釋它的文章](../Binary Search/).

無論如何, 這裡有個很重要的議題. 記得兩個物件可以是不同的但是擁有一樣的 "值" 用來做比對. 因為集合可以包含很多有相同值的元素, 所以要檢查二元搜尋是否有指向正確的元素是很重要的.

舉例來說, 考慮以下這個 `Player` 型別的有序集合. 每個 `Player` 有名字和分數:

	[ ("Bill", 50), ("Ada", 50), ("Jony", 50), ("Steve", 200), ("Jean-Louis", 500), ("Woz", 1000) ]

<!--
We want the set to be ordered by points, from low to high. Multiple players can have the same number of points. The name of the player is not important for this ordering. However, the name *is* important for retrieving the correct item.

Let's say we do `indexOf(bill)` where `bill` is player object `("Bill", 50)`. If we did a traditional binary search we'd land on index 2, which is the object `("Jony", 50)`. The value 50 matches, but it's not the object we're looking for!

Therefore, we also need to check the items with the same value to the right and left of the midpoint. The code to check the left and right side looks like this:
-->

我們想要依照分數來排序, 由低到高. 玩家有可能有相同分數. 玩家名稱對於排序不重要. 不過, 名稱對 *檢索* 正確的元素很重要.

假設我們做 `indexOf(bill)`, `bill` 的 `Player` 物件是 `("Bill", 50)`. 如果用傳統的二元搜尋, 我們會在索引 2 停止搜尋, 該物件是 `("Jony", 50)`. 因為一樣是 50 分, 但是這並不是我們要搜尋的物件!

因此, 我們也需要檢查左右那些相同值的元素. 程式碼像這樣:


```swift
        // Check to the right.
        for j in mid.stride(to: count - 1, by: 1) {
          if internalSet[j + 1] == item {
            return j + 1
          } else if internalSet[j] < internalSet[j + 1] {
            break
          }
        }

        // Check to the left.
        for j in mid.stride(to: 0, by: -1) {
          if internalSet[j - 1] == item {
            return j - 1
          } else if internalSet[j] > internalSet[j - 1] {
            break
          }
        }

        return nil
```

<!--
These loops start at the current `mid` value and then look at the neighboring values until we've found the correct object.

The combined runtime for `indexOf()` is **O(log(n) + k)** where **n** is the length of the set, and **k** is the number of items with the same *value* as the one that is being searched for.

Since the set is sorted, the following operations are all **O(1)**:
-->

這些迴圈從目前 `mid` 的值開始, 查詢左右鄰居直到我們找到正確的物件.

這些組合起來, `indexOf()` 的時間複雜度是 **O(log(n) + k)**, **n** 是集合的大小, **k** 是擁有和搜尋元素相同值的元素數量.

因為集合是有序的, 以下的操作都是 **O(1)**:

```swift
  // Returns the 'maximum' or 'largest' value in the set.
  public func max() -> T? {
    return count == 0 ? nil : internalSet[count - 1]
  }

  // Returns the 'minimum' or 'smallest' value in the set.
  public func min() -> T? {
    return count == 0 ? nil : internalSet[0]
  }

  // Returns the k-th largest element in the set, if k is in the range
  // [1, count]. Returns nil otherwise.
  public func kLargest(_ k: Int) -> T? {
    return k > count || k <= 0 ? nil : internalSet[count - k]
  }

  // Returns the k-th smallest element in the set, if k is in the range
  // [1, count]. Returns nil otherwise.
  public func kSmallest(_ k: Int) -> T? {
    return k > count || k <= 0 ? nil : internalSet[k - 1]
  }
```

<!--
## Examples

Below are a few examples that can be found in the playground file.
-->

## 範例

以下幾個範例可以在 playground 中找到.


<!--
### Example 1

Here we create a set with random Integers. Printing the largest/smallest 5 numbers in the set is fairly easy.
-->

### 範例 1

我們創建了一個隨機整數的集合. 列出陣列 最大/最小 的 5 個數字是很簡單的.

```swift
// Example 1 with type Int
var mySet = OrderedSet<Int>()

// Insert random numbers into the set
for _ in 0..<50 {
  mySet.insert(randomNum(50, max: 500))
}

print(mySet)

print(mySet.max())
print(mySet.min())

// Print the 5 largest values
for k in 1...5 {
  print(mySet.kLargest(k))
}

// Print the 5 lowest values
for k in 1...5 {
  print(mySet.kSmallest(k))
}
```

<!--
### Example 2

In this example we take a look at something a bit more interesting. We define a `Player` struct as follows:
-->

### 範例 2

在這範例中我們定義了 `Player` struct:

```swift
public struct Player: Comparable {
  public var name: String
  public var points: Int
}
```

<!--
The `Player` also gets its own `==` and `<` operators. The `<` operator is used to determine the sort order of the set, while `==` determines whether two objects are really equal.

Note that `==` compares both the name and the points:  
-->

`Player` 也有自己的 `==` 和 `<` 運算子. `<` 運算子是用來決定排序的, 而 `==` 是決定兩個物件是否相等.

注意到 `==` 比對名稱跟分數:

```swifr
func ==(x: Player, y: Player) -> Bool {
  return x.name == y.name && x.points == y.points
}
```

<!--
But `<` only compares the points:
-->

但是 `<` 只比對分數:

```swift
func <(x: Player, y: Player) -> Bool {
  return x.points < y.points
}
```

<!--
Therefore, two `Player`s can each have the same value (the number of points), but are not guaranteed to be equal (they can have different names).

We create a new set and insert 20 random players. The `Player()` constructor gives each player a random name and score:
-->

因此, 兩個 `Player` 可以有一樣的值 (分數), 但是並不保證是相等的 (可能不同名稱).

我們創建一個新的集合並插入 20 個隨機的玩家. `Player()` 建構子隨機的給名字和分數:


```swift
var playerSet = OrderedSet<Player>()

// Populate the set with random players.
for _ in 0..<20 {
  playerSet.insert(Player())
}
```

<!--
Insert another player:
-->

插入另一個玩家:

```swift
var anotherPlayer = Player()
playerSet.insert(anotherPlayer)
```

<!--
Now we use the `indexOf()` function to find out what rank `anotherPlayer` is.
-->

現在我們使用 `indexOf()` 函式來找 `anotherPlayer` 的階級.

```swift
let level = playerSet.count - playerSet.indexOf(anotherPlayer)!
print("\(anotherPlayer.name) is ranked at level \(level) with \(anotherPlayer.points) points")
```

<!--
### Example 3

The final example demonstrates the need to look for the right item even after the binary search has completed.

We insert 9 players into the set:
-->

### 範例 3

最終的範例展示在每次二元搜尋後都要檢查正確元素的需求.

我們先插入 9 個玩家:

```swift
var repeatedSet = OrderedSet<Player>()

repeatedSet.insert(Player(name: "Player 1", points: 100))
repeatedSet.insert(Player(name: "Player 2", points: 100))
repeatedSet.insert(Player(name: "Player 3", points: 100))
repeatedSet.insert(Player(name: "Player 4", points: 100))
repeatedSet.insert(Player(name: "Player 5", points: 100))
repeatedSet.insert(Player(name: "Player 6", points: 50))
repeatedSet.insert(Player(name: "Player 7", points: 200))
repeatedSet.insert(Player(name: "Player 8", points: 250))
repeatedSet.insert(Player(name: "Player 9", points: 25))
```

<!--
Notice how several of these players have the same value of 100 points.

The set looks something like this:
-->

注意有好幾個玩家有一樣的分數, 100 分.

集合現在像這樣:

	[Player 9, Player 6, Player 1, Player 2, Player 3, Player 4, Player 5, Player 7, Player 8]

<!--
The next line looks for `Player 2`:
-->

來查找 `Player 2`:


```swift
print(repeatedSet.index(of: Player(name: "Player 2", points: 100)))
```

<!--
After the binary search finishes, the value of `mid` is at index 5:
-->

在二元搜尋完後, `mid` 在索引 5 的地方:

	[Player 9, Player 6, Player 1, Player 2, Player 3, Player 4, Player 5, Player 7, Player 8]
	                                                      mid

<!--
However, this is not `Player 2`. Both `Player 4` and `Player 2` have the same points, but a different name. The binary search only looked at the points, not the name.

But we do know that `Player 2` must be either to the immediate left or the right of `Player 4`, so we check both sides of `mid`. We only need to look at the objects with the same value as `Player 4`. The others are replaced by `X`:
-->

不過, 它並不是 `Player 2`. `Player 4` 和  `Player 2` 有相同的分數, 但是不同的名稱. 而二元搜尋只比對分數, 沒有名稱.

但我們知道 `Player 2` 一定在 `Player 4` 的附近, 所以我們檢查 `mid` 的兩邊. 我們只需要查看那些和 `Player 4` 有相同值的元素. 其他的我們用 `X` 取代:


	[X, X, Player 1, Player 2, Player 3, Player 4, Player 5, X, X]
	                                       mid
<!--
The code then first checks on the right of `mid` (where the `*` is):
-->

程式碼先檢查 `mid` 的右邊 (`*` 的地方):


	[X, X, Player 1, Player 2, Player 3, Player 4, Player 5, X, X]
	                                       mid        *

<!--
The right side did not contain the item, so we look at the left side:
-->

不過右邊並沒有我們要的元素, 所以我們轉向檢查左邊:


	[X, X, Player 1, Player 2, Player 3, Player 4, Player 5, X, X]
	                              *        mid        

	[X, X, Player 1, Player 2, Player 3, Player 4, Player 5, X, X]
	                    *                  mid        

<!--
Finally, we've found `Player 2`, and return index 3.
-->

最後, 我們找到 `Player 2`, 然後回傳索引 3.

*Written By Zain Humayun*
