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


這次回傳 `3`, 下一次 dequeue 會回傳 `57`, 依此類推. 如果對一個空的佇列做 dequeue 的動作, 會回傳 `nil` 或有一些做法是會跳出錯誤訊息.

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
  
  public func peek() -> T? {
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

你可能會對插入複雜度 **O(1)** 感到困惑. 那是因為在 Swift 中的陣列總是會在陣列後端保留一些空白的空間.

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




	[ "Ada", "Steve", "Tim", xxx, xxx, xxx ]

where `xxx` is memory that is reserved but not filled in yet. Adding a new element to the array overwrites the next unused spot:

	[ "Ada", "Steve", "Tim", "Grace", xxx, xxx ]

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

## A more efficient queue

To make dequeuing more efficient, we can use the same trick of reserving some extra free space, but this time do it at the front of the array. We're going to have to write this code ourselves as the built-in Swift array doesn't support this out of the box.

The idea is as follows: whenever we dequeue an item, we don't shift the contents of the array to the front (slow) but mark the item's position in the array as empty (fast). After dequeuing `"Ada"`, the array is:

	[ xxx, "Steve", "Tim", "Grace", xxx, xxx ]

After dequeuing `"Steve"`, the array is:

	[ xxx, xxx, "Tim", "Grace", xxx, xxx ]

These empty spots at the front never get reused for anything, so they're wasting space. Every so often you can trim the array by moving the remaining elements to the front again:

	[ "Tim", "Grace", xxx, xxx, xxx, xxx ]

This trimming procedure involves shifting memory so it's an **O(n)** operation. But because it only happens once in a while, dequeuing is now **O(1)** on average.

Here is how you could implement this version of `Queue`:

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
  
  public func peek() -> T? {
    if isEmpty {
      return nil
    } else {
      return array[head]
    }
  }
}
```

The array now stores objects of type `T?` instead of just `T` because we need some way to mark array elements as being empty. The `head` variable is the index in the array of the front-most object.

Most of the new functionality sits in `dequeue()`. When we dequeue an item, we first set `array[head]` to `nil` to remove the object from the array. Then we increment `head` because now the next item has become the front one.

We go from this:

	[ "Ada", "Steve", "Tim", "Grace", xxx, xxx ]
	  head

to this:

	[ xxx, "Steve", "Tim", "Grace", xxx, xxx ]
	        head

It's like some weird supermarket where the people in the checkout lane don't shuffle forward towards the cash register, but the cash register moves up the queue.

Of course, if we never remove those empty spots at the front then the array will keep growing as we enqueue and dequeue elements. To periodically trim down the array, we do the following:

```swift
    let percentage = Double(head)/Double(array.count)
    if array.count > 50 && percentage > 0.25 {
      array.removeFirst(head)
      head = 0
    }
```

This calculates the percentage of empty spots at the beginning as a ratio of the total array size. If more than 25% of the array is unused, we chop off that wasted space. However, if the array is small we don't want to resize it all the time, so there must be at least 50 elements in the array before we try to trim it. 

> **Note:** I just pulled these numbers out of thin air -- you may need to tweak them based on the behavior of your app in a production environment.

To test this in a playground, do:

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

To test the trimming behavior, replace the line,

```swift
    if array.count > 50 && percentage > 0.25 {
```

with:

```swift
    if head > 2 {
```

Now if you dequeue another object, the array will look as follows:

```swift
q.dequeue()         // "Tim"
q.array             // [{Some "Grace"}]
q.count             // 1
```

The `nil` objects at the front have been removed and the array is no longer wasting space. This new version of `Queue` isn't much more complicated than the first one but dequeuing is now also an **O(1)** operation, just because we were a bit smarter about how we used the array.

## See also

There are many other ways to create a queue. Alternative implementations use a [linked list](../Linked List/), a [circular buffer](../Ring Buffer/), or a [heap](../Heap/). 

Variations on this theme are [deque](../Deque/), a double-ended queue where you can enqueue and dequeue at both ends, and [priority queue](../Priority Queue/), a sorted queue where the "most important" item is always at the front.

*Written for Swift Algorithm Club by Matthijs Hollemans*
