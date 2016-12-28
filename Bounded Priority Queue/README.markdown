# Bounded Priority queue (有界優先佇列)

<!--
A bounded priority queue is similar to a regular [priority queue](../Priority Queue/), except that there is a fixed upper bound on the number of elements that can be stored. When a new element is added to the queue while the queue is at capacity, the element with the highest priority value is ejected from the queue.
-->

除了有個固定的元素總數上限以外, 有界優先佇列其實和 [優先佇列](../Priority Queue/) 類似. 當佇列已經滿了又有新的元素要進入佇列, 佇列中擁有最低優先值的元素就會被擠出佇列.

<!--
## Example

Suppose we have a bounded-priority-queue with maximum size 5 that has the following values and priorities:
-->

## 範例

假設我們有個有界優先佇列最大容量是 5 個元素:

```
Value:    [ A,   B,   C,    D,    E   ]
Priority: [ 4.6, 3.2, 1.33, 0.25, 0.1 ]
```

<!--
Here, we consider the object with the highest priority value to be the most important (so this is a *max-priority* queue). The larger the priority value, the more we care about the object. So `A` is more important than `B`, `B` is more important than `C`, and so on.

Now we want to insert the element `F` with priority `0.4` into this bounded priority queue. Because the queue has maximum size 5, this will insert the element `F` but then evict the lowest-priority element (`E`), yielding the updated queue:
-->

這裡我們把優先值最大的當作最重要的元素 (所以這是 *最大優先佇列*). 優先值越大, 表示我們越在意該元素. 所以 `A` 比 `B` 更重要, `B` 比 `C` 更重要, 依此類推.

現在我們要插入 `F` 優先值是 `0.4`. 因為佇列最大容量是 5 個元素, 這表示插入 `F` 會連帶將優先值最低的元素 (`E`) 移出佇列:

```
Value:    [ A,   B,   C,    F,   D    ]
Priority: [ 4.6, 3.2, 1.33, 0.4, 0.25 ]
```
<!--
`F` is inserted between `C` and `D` because of its priority value. It's less important than `C` but more important than `D`.

Suppose that we wish to insert the element `G` with priority 0.1 into this BPQ. Because `G`'s priority value is less than the minimum-priority element in the queue, upon inserting `G` it will immediately be evicted. In other words, inserting an element into a BPQ with priority less than the minimum-priority element of the BPQ has no effect.
-->

`F` 插入在 `C` 和 `D` 之間是根據它的優先值. 它比 `C` 不重要但是比 `D` 重要.

假設我們要插入 `G` 優先值是 `0.1`, 因為 `G` 的優先值比原本在佇列中最小優先值的元素 `D(0.25)` 還要小, 所以 `G` 會再插入佇列後又被最優先的移除 (因為插入後 `G` 變成最小優先值). 這表示如果插入的元素優先值比原本在佇列中最小優先值的元素還小, 就根本無法插入. 

<!--
## Implementation

While a [heap](../Heap/) may be a really simple implementation for a priority queue, a sorted [linked list](../Linked List/) allows for **O(k)** insertion and **O(1)** deletion, where **k** is the bounding number of elements.

Here's how you could implement it in Swift:
-->

## 實作

[堆積](../Heap/) 也許是個非常簡單的優先佇列實作, 或者有序的 [鏈結列表](../Linked List/) 也提供了 **O(k)** 的插入時間 和 **O(1)** 的移除時間, **k** 是佇列最大容量.

Swift 實作:


```swift
public class BoundedPriorityQueue<T: Comparable> {
  private typealias Node = LinkedListNode<T>

  private(set) public var count = 0
  fileprivate var head: Node?
  private var tail: Node?
  private var maxElements: Int

  public init(maxElements: Int) {
    self.maxElements = maxElements
  }

  public var isEmpty: Bool {
    return count == 0
  }

  public func peek() -> T? {
    return head?.value
  }
```

<!--
The `BoundedPriorityQueue` class contains a doubly linked list of `LinkedListNode` objects. Nothing special here yet. The fun stuff happens in the `enqueue()` method:
-->

`BoundedPriorityQueue` class 有兩個 `LinkedListNode` 型別的物件. 除此之外沒什麼特別. 有趣的是 `enqueue()` 方法:

```swift
public func enqueue(_ value: T) {
  if let node = insert(value, after: findInsertionPoint(value)) {
    // If the newly inserted node is the last one in the list, then update
    // the tail pointer.
    if node.next == nil {
      tail = node
    }

    // If the queue is full, then remove an element from the back.
    count += 1
    if count > maxElements {
      removeLeastImportantElement()
    }
  }
}

private func insert(_ value: T, after: Node?) -> Node? {
  if let previous = after {

    // If the queue is full and we have to insert at the end of the list,
    // then there's no reason to insert the new value.
    if count == maxElements && previous.next == nil {
      print("Queue is full and priority of new object is too small")
      return nil
    }

    // Put the new node in between previous and previous.next (if exists).
    let node = Node(value: value)
    node.next = previous.next
    previous.next?.previous = node
    previous.next = node
    node.previous = previous
    return node

  } else if let first = head {
    // Have to insert at the head, so shift the existing head up once place.
    head = Node(value: value)
    head!.next = first
    first.previous = head
    return head

  } else {
    // This is the very first item in the queue.
    head = Node(value: value)
    return head
  }
}

/* Find the node after which to insert the new value. If this returns nil,
   the new value should be inserted at the head of the list. */
private func findInsertionPoint(_ value: T) -> Node? {
  var node = head
  var prev: Node? = nil

  while let current = node where value < current.value {
    prev = node
    node = current.next
  }
  return prev
}

private func removeLeastImportantElement() {
  if let last = tail {
    tail = last.previous
    tail?.next = nil
    count -= 1
  }

  // Note: Instead of using a tail pointer, we could just scan from the new
  // node until the end. Then nodes also don't need a previous pointer. But
  // this is much slower on large lists.
}
```

<!--
We first check if the queue already has the maximum number of elements. If so, and the new priority value is less than the `tail` element's priority value, then there is no room for this new element and we return without inserting it.

If the new value is acceptable, then we search through the list to find the proper insertion location and update the `next` and `previous` pointers.

Lastly, if the queue has now reached the maximum number of elements, then we `dequeue()` the one with the largest priority value.

By keeping the most important element at the front of the list, it makes dequeueing very easy:
-->

我們先檢查佇列是否已經滿了. 如果滿了, 而這個新的元素優先值比當前最小優先元素(`tail`)的優先值還小, 那就直接返回並且不插入該元素.

如果還有空間, 那走訪整個佇列找尋合適的位置並更新 `next` 和 `previous` 參考指標.


最後, 如果佇列已經滿了, 那我們可以使用 `dequeue()` 來取出最大優先值的元素.

因為我們有留最優先元素的參考指標, 所以取出動作很簡單:

```swift
public func dequeue() -> T? {
  if let first = head {
    count -= 1
    if count == 0 {
      head = nil
      tail = nil
    } else {
      head = first.next
      head!.previous = nil
    }
    return first.value
  } else {
    return nil
  }
}
```

<!--
This simply removes the `head` element from the list and returns it.
-->

簡單的吧 `head` 元素移除並回傳.

*Written for Swift Algorithm Club by John Gill and Matthijs Hollemans*
