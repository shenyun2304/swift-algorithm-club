# Queue (佇列)

佇列是一個只能在後端插入元素和在前端移除元素的列表. 這樣保證了最新放進列表的元素也會是最先離開列表的元素. 先來, 先處理!

為什麼需要這東西? 在很多演算法中, 某些時候會需要新增物件到暫存的列表中, 然後在其他時間把他們取出來. 常常這個取的順序是有差別的.

佇列提供 FIFO 或稱 先進先出 的順序. 最先插入的元素也會是最先拿出來的元素. (有一個非常相似的結構, [堆疊](../Stack), 提供了 LIFO 或稱 後進先出 的順序.)

<!--
For example, let's enqueue a number:
-->

舉例來說, 讓我們插入一個數字:

```swift
queue.enqueue(10)
```
<!--
The queue is now `[ 10 ]`. Add the next number to the queue:
-->

佇列現在是 `[ 10 ]`. 再加一個數字到佇列中:

```swift
queue.enqueue(3)
```

<!--
The queue is now `[ 10, 3 ]`. Add one more number:
-->

佇列現在是 `[ 10, 3 ]`. 在插入一個數字:

```swift
queue.enqueue(57)
```

<!--
The queue is now `[ 10, 3, 57 ]`. Let's dequeue to pull the first element off the front of the queue:
-->

佇列現在是 `[ 10, 3, 57 ]`. 讓我們從佇列前端取出最先插入的元素:

```swift
queue.dequeue()
```

<!--
This returns `10` because that was the first number we inserted. The queue is now `[ 3, 57 ]`. Everyone moved up by one place.
-->

這會回傳 `10`, 因為它是第一個插入佇列的元素. 而佇列現在是 `[ 3, 57 ]`. 所有元素都往前移動了一個位置.

```swift
queue.dequeue()
```

<!--
This returns `3`, the next dequeue returns `57`, and so on. If the queue is empty, dequeuing returns `nil` or in some implementations it gives an error message.

> **Note:** A queue is not always the best choice. If the order in which the items are added and removed from the list isn't important, you might as well use a [stack](../Stack/) instead of a queue. Stacks are simpler and faster.
-->


這次回傳 `3`, 下一次取出會回傳 `57`, 依此類推. 如果對一個空的佇列做取出的動作, 會回傳 `nil` 或有一些做法是會跳出錯誤訊息.

> **注意:** 佇列並不總是最好的選擇. 如果插入和移除的元素順序並不重要. 那用 [堆疊](../Stack/) 可能會更好. 堆疊更簡單且快速.

<!--
## The code

Here is a very simplistic implementation of a queue in Swift. It's just a wrapper around an array that lets you enqueue, dequeue, and peek at the front-most item:
-->

## 程式碼

這裡是一個 Swift 佇列簡單的實作. 只是包裝了一個陣列然後提供了插入, 取出 和 查看最前端元素 三個函式:


```swift
public struct Queue<T> {
  fileprivate var array = [T]()

  public var isEmpty: Bool {
    return array.isEmpty
  }
  
  public var count: Int {
    return array.count
  }

  public mutating func enqueue(_ element: T) {
    array.append(element)
  }
  
  public mutating func dequeue() -> T? {
    if isEmpty {
      return nil
    } else {
      return array.removeFirst()
    }
  }
  
  public var front: T? {
    return array.first
  }
}
```

<!--
This queue works just fine but it is not optimal.

Enqueuing is an **O(1)** operation because adding to the end of an array always takes the same amount of time, regardless of the size of the array. So that's great.

You might be wondering why appending items to an array is **O(1)**, or a constant-time operation. That is so because an array in Swift always has some empty space at the end. If we do the following:
-->

這個佇列運作正常, 但是並非 Optional.

插入是一個 **O(1)** 的操作, 因為在陣列尾端插入元素總是佔用一樣的時間, 無論陣列多大.

你可能會對插入的時間複雜度 **O(1)** 感到困惑. 那是因為在 Swift 中的陣列總是會在陣列後端保留一些空白的空間.

如果我們這樣做:


```swift
var queue = Queue<String>()
queue.enqueue("Ada")
queue.enqueue("Steve")
queue.enqueue("Tim")
```

<!--
then the array might actually look like this:
-->

其實陣列實際上是這樣:

	[ "Ada", "Steve", "Tim", xxx, xxx, xxx ]

<!--
where `xxx` is memory that is reserved but not filled in yet. Adding a new element to the array overwrites the next unused spot:
-->

`xxx` 是保留且尚未使用的記憶體區塊. 插入一個新的元素就是覆寫掉下一個未使用的位置.

	[ "Ada", "Steve", "Tim", "Grace", xxx, xxx ]

<!--
This is simply matter of copying memory from one place to another, a constant-time operation.

Of course, there are only a limited number of such unused spots at the end of the array. When the last `xxx` gets used and you want to add another item, the array needs to resize to make more room.

Resizing includes allocating new memory and copying all the existing data over to the new array. This is an **O(n)** process, so it's relatively slow. But since it happens only every so often, the time for appending a new element to the end of the array is still **O(1)** on average, or **O(1)** "amortized".

The story for dequeueing is slightly different. To dequeue we remove the element from the *beginning* of the array, not the end. This is always an **O(n)** operation because it requires all remaining array elements to be shifted in memory.

In our example, dequeuing the first element `"Ada"` copies `"Steve"` in the place of `"Ada"`, `"Tim"` in the place of `"Steve"`, and `"Grace"` in the place of `"Tim"`:

	before   [ "Ada", "Steve", "Tim", "Grace", xxx, xxx ]
	                   /       /      /
	                  /       /      /
	                 /       /      /
	                /       /      /
	 after   [ "Steve", "Tim", "Grace", xxx, xxx, xxx ]
 
Moving all these elements in memory is always an **O(n)** operation. So with our simple implementation of a queue, enqueuing is efficient but dequeueing leaves something to be desired...
-->

這只是很簡單的記憶體從一個地方複製到另一個地方而已, 是一個使用常數時間的操作.

當然, 這些保留的區塊數也是有限的. 當最後一個 `xxx` 也使用了, 在插入一個元素時就陣列就需要重新調整大小.

重新調整大小需要配置新的記憶體空間, 並且將以存的元素放到新的陣列空間中. 這是一項 **O(n)** 的操作, 所以相對來說比較慢. 雖然大概固定一段時間就會發生重新調整大小的狀況, 但從陣列尾端插入元素的時間平均依然是 **O(1)**.

從佇列中取值就有點不同. 我們從陣列的 *前端* 將值取出, 不是尾端. 這樣的操作需要所有的元素都向前移動一個位置, 時間複雜度是 **O(n)**

在我們的例子中, 取出第一個值 `Ada` 後會將 `"Steve"` 覆寫到 `"Ada"` 的位置, `"Tim"` 覆寫到 `"Steve"` 的位置, 最後 `"Grace"` 覆寫到 `"Tim"` 的位置:

	before   [ "Ada", "Steve", "Tim", "Grace", xxx, xxx ]
	                   /       /      /
	                  /       /      /
	                 /       /      /
	                /       /      /
	 after   [ "Steve", "Tim", "Grace", xxx, xxx, xxx ]


移動所有元素是一個 **O(n)** 的操作. 所以在我們簡單的實作中, 插入元素是很有效率的, 不過取出元素卻有待加強.

<!--
## A more efficient queue

To make dequeuing more efficient, we can use the same trick of reserving some extra free space, but this time do it at the front of the array. We're going to have to write this code ourselves as the built-in Swift array doesn't support this out of the box.

The idea is as follows: whenever we dequeue an item, we don't shift the contents of the array to the front (slow) but mark the item's position in the array as empty (fast). After dequeuing `"Ada"`, the array is:
-->

## 更有效率的佇列

為了讓取出更有效率, 我們可以用和插入一樣保留未使用空間的技巧, 但這次是將空間放在陣列的前端. 

	[ xxx, "Steve", "Tim", "Grace", xxx, xxx ]

<!--
After dequeuing `"Steve"`, the array is:
-->

再取出 `"Steve"`之後, 陣列是像這樣:

	[ xxx, xxx, "Tim", "Grace", xxx, xxx ]

<!--
These empty spots at the front never get reused for anything, so they're wasting space. Every so often you can trim the array by moving the remaining elements to the front again:
-->

這些前端的保留空間不會被拿來做任何事情, 所以的確是浪費空間. 所以每隔一段時間要調整大小:

	[ "Tim", "Grace", xxx, xxx, xxx, xxx ]

<!--
This trimming procedure involves shifting memory so it's an **O(n)** operation. But because it only happens once in a while, dequeuing is now **O(1)** on average.

Here is how you could implement this version of `Queue`:
-->

這個調整大小的操作要移動陣列中所有的元素, 所以時間複雜度是 **O(n)**. 不過因為這一陣子才會執行一次, 所以單次的取出時間平均是 **n(1)**.

以下是實做這個版本的 `Queue` 範例:


```swift
public struct Queue<T> {
  fileprivate var array = [T?]()
  fileprivate var head = 0
  
  public var isEmpty: Bool {
    return count == 0
  }

  public var count: Int {
    return array.count - head
  }
  
  public mutating func enqueue(_ element: T) {
    array.append(element)
  }
  
  public mutating func dequeue() -> T? {
    guard head < array.count, let element = array[head] else { return nil }

    array[head] = nil
    head += 1

    let percentage = Double(head)/Double(array.count)
    if array.count > 50 && percentage > 0.25 {
      array.removeFirst(head)
      head = 0
    }
    
    return element
  }
  
  public var front: T? {
    return array.first
  }
}
```

<!--
The array now stores objects of type `T?` instead of just `T` because we need some way to mark array elements as being empty. The `head` variable is the index in the array of the front-most object.

Most of the new functionality sits in `dequeue()`. When we dequeue an item, we first set `array[head]` to `nil` to remove the object from the array. Then we increment `head` because now the next item has become the front one.

We go from this:
-->

現在這個陣列內的元素是 `T?` 不是 `T` 而已, 因為我們需要某些位置是空值. `head` 變數表示當前陣列第一個元素的陣列索引.
大部分新的操作在於 `dequeue()`. 當我們取出一個元素, 先將 `array[head]` 設為 `nil`. 然後在迭代 `head` 讓它指向下一個元素的索引.

就像這樣:

	[ "Ada", "Steve", "Tim", "Grace", xxx, xxx ]
	  head

變這樣:

	[ xxx, "Steve", "Tim", "Grace", xxx, xxx ]
	        head

<!--
It's like some weird supermarket where the people in the checkout lane don't shuffle forward towards the cash register, but the cash register moves up the queue.

Of course, if we never remove those empty spots at the front then the array will keep growing as we enqueue and dequeue elements. To periodically trim down the array, we do the following:
-->

這有點像在超市結帳的時候不是由顧客一個一個往前移動結帳, 而是收銀員一個一個去找顧客收帳.

當然如果我們都不移除那些陣列前端的空值, 那無論插入或取出的操作都會使得陣列一直不斷的擴大. 如何每隔一段時間就做縮減調整, 我們這樣做:

```swift
    let percentage = Double(head)/Double(array.count)
    if array.count > 50 && percentage > 0.25 {
      array.removeFirst(head)
      head = 0
    }
```

<!--
This calculates the percentage of empty spots at the beginning as a ratio of the total array size. If more than 25% of the array is unused, we chop off that wasted space. However, if the array is small we don't want to resize it all the time, so there must be at least 50 elements in the array before we try to trim it. 

> **Note:** I just pulled these numbers out of thin air -- you may need to tweak them based on the behavior of your app in a production environment.

To test this in a playground, do:
-->

這裡計算了空值數佔陣列長度的百分比. 如果超過 25% 的位置是空值, 那我們就清理那些空間. 不過呢, 當陣列長度不大時, 我們並不希望一直在調整大小, 所以又加上了陣列大小至少要 50 個元素以上才會做清空間的動作.

> **注意:** 這邊只是憑空的設定這些數值 -- 需要針對你自己的專案來做調整

在 playground 中測試:

```swift
var q = Queue<String>()
q.array                   // [] empty array

q.enqueue("Ada")
q.enqueue("Steve")
q.enqueue("Tim")
q.array             // [{Some "Ada"}, {Some "Steve"}, {Some "Tim"}]
q.count             // 3

q.dequeue()         // "Ada"
q.array             // [nil, {Some "Steve"}, {Some "Tim"}]
q.count             // 2

q.dequeue()         // "Steve"
q.array             // [nil, nil, {Some "Tim"}]
q.count             // 1

q.enqueue("Grace")
q.array             // [nil, nil, {Some "Tim"}, {Some "Grace"}]
q.count             // 2
```

<!--
To test the trimming behavior, replace the line,
-->

要測試清理空間的動作, 把以下這段

```swift
    if array.count > 50 && percentage > 0.25 {
```

<!--
with:
-->
改寫成:

```swift
    if head > 2 {
```

<!--
Now if you dequeue another object, the array will look as follows:
-->

現在如果你取出任何一個元素, 陣列將會做清裡的動作:

```swift
q.dequeue()         // "Tim"
q.array             // [{Some "Grace"}]
q.count             // 1
```

<!--
The `nil` objects at the front have been removed and the array is no longer wasting space. This new version of `Queue` isn't much more complicated than the first one but dequeuing is now also an **O(1)** operation, just because we were a bit smarter about how we used the array.
-->

在前端的 `nil` 物件已經被移除了, 所以陣列中也沒有浪費空間. 這個新版本的 `Queue` 跟原來的比起來並沒有複雜多少, 但是這時候取出的動作也是 **O(1)** 時間了, 

<!--
## See also

There are many other ways to create a queue. Alternative implementations use a [linked list](../Linked List/), a [circular buffer](../Ring Buffer/), or a [heap](../Heap/). 

Variations on this theme are [deque](../Deque/), a double-ended queue where you can enqueue and dequeue at both ends, and [priority queue](../Priority Queue/), a sorted queue where the "most important" item is always at the front.
-->

## 相關閱讀

還有很多其他的方法來創建佇列. 像是 [鏈結列表](../Linked List/), [環狀緩衝](../Ring Buffer/) 或 [堆積](../Heap/).

本例的變化有 [雙端佇列](../Deque/), 這是一個可以在任一端插入或著取出的結構, 還有 [優先佇列](../ Priority Queue/), 一個排序後的佇列讓 "最重要" 的元素永遠在最前端.


*Written for Swift Algorithm Club by Matthijs Hollemans*
