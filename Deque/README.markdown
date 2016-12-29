# Deque (雙端佇列)

<!--
A double-ended queue. For some reason this is pronounced as "deck".

A regular [queue](../Queue/) adds elements to the back and removes from the front. The deque also allows enqueuing at the front and dequeuing from the back, and peeking at both ends.

Here is a very basic implementation of a deque in Swift:
-->

一個兩端都可操作的佇列. 因為某些原因這個單字發音為 "deck".

一般的 [佇列](../Queue/) 是在尾端插入元素, 在前端取出元素. 而雙端佇列在兩端都可以做插入跟取出的動作, 還可以檢查兩邊的元素.

這裡用 Swift 實做一個非常基本的雙端佇列:


```swift
public struct Deque<T> {
  private var array = [T]()
  
  public var isEmpty: Bool {
    return array.isEmpty
  }
  
  public var count: Int {
    return array.count
  }
  
  public mutating func enqueue(_ element: T) {
    array.append(element)
  }
  
  public mutating func enqueueFront(_ element: T) {
    array.insert(element, atIndex: 0)
  }
  
  public mutating func dequeue() -> T? {
    if isEmpty {
      return nil
    } else {
      return array.removeFirst()
    }
  }
  
  public mutating func dequeueBack() -> T? {
    if isEmpty {
      return nil
    } else {
      return array.removeLast()
    }
  }
  
  public func peekFront() -> T? {
    return array.first
  }
  
  public func peekBack() -> T? {
    return array.last
  }
}
```

<!--
This uses an array internally. Enqueuing and dequeuing are simply a matter of adding and removing items from the front or back of the array.

An example of how to use it in a playground:
-->

一樣使用陣列. 插入和取出也是很簡單的直接對陣列的前端或尾端做操作.

在 playground 你可以這樣使用:


```swift
var deque = Deque<Int>()
deque.enqueue(1)
deque.enqueue(2)
deque.enqueue(3)
deque.enqueue(4)

deque.dequeue()       // 1
deque.dequeueBack()   // 4

deque.enqueueFront(5)
deque.dequeue()       // 5
```
<!--
This particular implementation of `Deque` is simple but not very efficient. Several operations are **O(n)**, notably `enqueueFront()` and `dequeue()`. I've included it only to show the principle of what a deque does.
-->

這個 `Deque` 的實作非常簡單, 但是並沒有很好的效能. 很多操作都是 **O(n)**, 特別是 `enqueueFront()` 和 `dequeue()`.
這只是展示雙端佇列的操作原則而已.

<!--
## A more efficient version
  
The reason that `dequeue()` and `enqueueFront()` are **O(n)** is that they work on the front of the array. If you remove an element at the front of an array, what happens is that all the remaining elements need to be shifted in memory.

Let's say the deque's array contains the following items:
-->
## 更有效率的版本

`dequeue()` 和 `enqueueFront()` 的時間複雜度是 **O(n)** 的操作是因為它們是對陣列的前端做操作. 如果你移除了某個前端的元素, 剩下的元素皆要做相對的移動.

讓我們對下面這個陣列做取出的動作:

	[ 1, 2, 3, 4 ]

<!--
Then `dequeue()` will remove `1` from the array and the elements `2`, `3`, and `4`, are shifted one position to the front:
-->

`dequeue()` 會移除 `1` 並且 `2`, `3` 和 `4` 會想前端移動一個位置:

	[ 2, 3, 4 ]

<!--
This is an **O(n)** operation because all array elements need to be moved by one position in the computer's memory.

Likewise, inserting an element at the front of the array is expensive because it requires that all other elements must be shifted one position to the back. So `enqueueFront(5)` will change the array to be:
-->

這是一個 **O(n)** 的操作, 因為所有元素在記憶體中都要移動一個位置.

同樣的, 在前端插入一個元素是代價也是很大的, 因為這個操作需要所有的元素向後移動一個位置. 所以 `enqueueFront(5)` 將會改變陣列為:

	[ 5, 2, 3, 4 ]

<!--
First, the elements `2`, `3`, and `4` are moved up by one position in the computer's memory, and then the new element `5` is inserted at the position where `2` used to be.

Why is this not an issue at for `enqueue()` and `dequeueBack()`? Well, these operations are performed at the end of the array. The way resizable arrays are implemented in Swift is by reserving a certain amount of free space at the back. 

Our initial array `[ 1, 2, 3, 4]` actually looks like this in memory:
-->
首先, `2`, `3` 和 `4` 都往後移動了一個位置. 然後 `5` 覆寫了原本 `2` 的位置.

為什麼 `enqueue()` 和 `dequeueBack()` 沒有這問題? 因為這兩個操作都是在陣列尾端. 可調整大小的陣列在 Swift 中的做法是在陣列的尾端保留一些空值的位置.

	[ 1, 2, 3, 4, x, x, x ]

<!--
where the `x`s denote additional positions in the array that are not being used yet. Calling `enqueue(6)` simply copies the new item into the next unused spot:
-->

`x` 表示該位置沒有被使用. 所以呼叫 `enqueue(6)` 只需要簡單地把新元素覆寫到尚未使用的空間就好.

	[ 1, 2, 3, 4, 6, x, x ]

`dequeueBack()` 使用 `array.removeLast()` 去刪除元素. 這動作並沒有縮減陣列的記憶體大小, 只是讓 `array.count` 少一而已. 這樣做就不需要涉及到更多記憶體的操作. 所以對於陣列尾端的操作是非常快速的 **O(1)**.

<!--
It is possible the array runs out of free spots at the back. In that case, Swift will allocate a new, larger array and copy over all the data. This is an **O(n)** operation but because it only happens once in a while, adding new elements at the end of an array is still **O(1)** on average.

Of course, we can use this same trick at the *beginning* of the array. That will make our deque efficient too for operations at the front of the queue. Our array will look like this:
-->

陣列尾端的保留空間是有可能用完的. 用完時, Swift 將會重新指派一個新的, 更大的記憶體區塊, 然後將原本的資料複製過去. 這是一個 **O(n)** 的操作, 但是這並不是常常在做, 在執行單一插入結尾操作時間複雜度平均還是 **O(1)**.

當然, 我們也可以把這個技巧使用在 *前端* 上. 這樣也會讓我們的雙端佇列在前端操作上更有效率. 陣列會像這樣:

	[ x, x, x, 1, 2, 3, 4, x, x, x ]

<!--
There is now also a chunk of free space at the start of the array, which allows adding or removing elements at the front of the queue to be **O(1)** as well.

Here is the new version of `Deque`:
-->

現在前端也有保留的空值位置. 這樣就能夠使得前端操作的時間複雜度變為 **O(1)**.

```swift
public struct Deque<T> {
  private var array: [T?]
  private var head: Int
  private var capacity: Int
  
  public init(_ capacity: Int = 10) {
    self.capacity = max(capacity, 1)
    array = [T?](repeating: nil, count: capacity)
    head = capacity
  }
  
  public var isEmpty: Bool {
    return count == 0
  }
  
  public var count: Int {
    return array.count - head
  }
  
  public mutating func enqueue(_ element: T) {
    array.append(element)
  }
  
  public mutating func enqueueFront(_ element: T) {
    // this is explained below
  }

  public mutating func dequeue() -> T? {
    // this is explained below
  }

  public mutating func dequeueBack() -> T? {
    if isEmpty {
      return nil
    } else {
      return array.removeLast()
    }
  }
  
  public func peekFront() -> T? {
    if isEmpty {
      return nil
    } else {
      return array[head]
    }
  }
  
  public func peekBack() -> T? {
    if isEmpty {
      return nil
    } else {
      return array.last!
    }
  }  
}
```

<!--
It still largely looks the same -- `enqueue()` and `dequeueBack()` haven't changed -- but there are also a few important differences. The array now stores objects of type `T?` instead of just `T` because we need some way to mark array elements as being empty.

The `init` method allocates a new array that contains a certain number of `nil` values. This is the free room we have to work with at the beginning of the array. By default this creates 10 empty spots. 

The `head` variable is the index in the array of the front-most object. Since the queue is currently empty, `head` points at an index beyond the end of the array.
-->

看起來跟 `enqueue()` 和 `dequeueBack()` 很像 -- 但是還是有一些明顯的差異. 陣列現在內涵元素是 `T?` 型態, 不再是 `T`, 因為我們需要某些位置為空值.

`init` 方法創建了一個新的陣列並且內容值都是 `nil`. 這些都是可以自由操作的空值空間. 預設最少賦予 10 個空值空間.

`head` 變數表示陣列中最左邊元樹的索引值. 當創建時, 陣列沒有元素, `head` 指向陣列的尾端之外.

	[ x, x, x, x, x, x, x, x, x, x ]
	                                 |
	                                 head
<!--
To enqueue an object at the front, we move `head` one position to the left and then copy the new object into the array at index `head`. For example, `enqueueFront(5)` gives:
-->

要在前端插入元素, 我們將 `head` 往左一個位置, 然後將新的元素複製到該索引內. 舉例來說, `enqueueFront(5)` 給我們:

	[ x, x, x, x, x, x, x, x, x, 5 ]
	                             |
	                             head
	                             
<!--
Followed by `enqueueFront(7)`:
-->

在執行一次 `enqueueFront(7)`:

	[ x, x, x, x, x, x, x, x, 7, 5 ]
	                          |
	                          head

<!--
And so on... the `head` keeps moving to the left and always points at the first item in the queue. `enqueueFront()` is now **O(1)** because it only involves copying a value into the array, a constant-time operation.

Here is the code:
-->

以此類推... `head` 持續向左移動並永遠指向陣列最前端的元素. 
`enqueueFront()` 現在是 **O(1)** 的操作, 因為現在這個動作只是複製一個直到陣列的某個索引而已.

程式碼:

```swift
  public mutating func enqueueFront(element: T) {
    head -= 1
    array[head] = element
  }
```
<!--
Appending to the back of the queue has not changed (it's the exact same code as before). For example, `enqueue(1)` gives:
-->

在尾端插入元素並沒有改變 (跟之前一模一樣). `enqueue(1)` 就像這樣:

	[ x, x, x, x, x, x, x, x, 7, 5, 1, x, x, x, x, x, x, x, x, x ]
	                          |
	                          head

<!--
Notice how the array has resized itself. There was no room to add the `1`, so Swift decided to make the array larger and add a number of empty spots to the end. If you enqueue another object, it gets added to the next empty spot in the back. For example, `enqueue(2)`:
-->
注意這邊陣列自動重新調整大小. 因為後面沒有空間插入 `1`, 所以 Swift 決定要創造一個更大的陣列並且在後方一樣放置空值空間. 如果又插入某個元素, 它就直接覆寫後端的下一個空值空間. 舉例來說, `enqueue(2)`:


	[ x, x, x, x, x, x, x, x, 7, 5, 1, 2, x, x, x, x, x, x, x, x ]
	                          |
	                          head

<!--
> **Note:** You won't see those empty spots at the back of the array when you `print(deque.array)`. This is because Swift hides them from you. Only the ones at the front of the array show up. 

The `dequeue()` method does the opposite of `enqueueFront()`, it reads the value at `head`, sets the array element back to `nil`, and then moves `head` one position to the right:
-->

> **注意:** 當你用 `print(deque.array)` 的時候, 是看不到後端的空值的. 你只能看到前端的空值, 這是 Swift 預設的.

`dequeue()` 方法就是  `enqueueFront()` 的相反, 它讀取 `head` 的內容值, 設定該值為 `nil`, 然後將 `head` 向右一個位置.

```swift
  public mutating func dequeue() -> T? {
    guard head < array.count, let element = array[head] else { return nil }

    array[head] = nil
    head += 1

    return element
  }
```

<!--
There is one tiny problem... If you enqueue a lot of objects at the front, you're going to run out of empty spots at the front at some point. When this happens at the back of the array, Swift automatically resizes it. But at the front of the array we have to handle this situation ourselves, with some extra logic in `enqueueFront()`:
-->

有一個小問題... 如果你插入很多元素到前端, 可能會將前端保留的空值空間用完. 當這件事情發生在陣列尾端的時候, Swift 會自動的調整陣列大小. 但是如果是在陣列前端, 那我們就得自己處理這個問題, 在 `enqueueFront()` 上要加點工:


```swift
  public mutating func enqueueFront(element: T) {
    if head == 0 {
      capacity *= 2
      let emptySpace = [T?](repeating: nil, count: capacity)
      array.insert(contentsOf: emptySpace, at: 0)
      head = capacity
    }

    head -= 1
    array[head] = element
  }
```
<!--
If `head` equals 0, there is no room left at the front. When that happens, we add a whole bunch of new `nil` elements to the array. This is an **O(n)** operation but since this cost gets divided over all the `enqueueFront()`s, each individual call to `enqueueFront()` is still **O(1)** on average. 

> **Note:** We also multiply the capacity by 2 each time this happens, so if your queue grows bigger and bigger, the resizing happens less often. This is also what Swift arrays automatically do at the back.

We have to do something similar for `dequeue()`. If you mostly enqueue a lot of elements at the back and mostly dequeue from the front, then you may end up with an array that looks as follows:
-->

如果 `head` 是 0, 表示前端已經沒有任何保留空間了. 當發生這個情況時, 我們加入一堆 `nil` 直到陣列前端. 這是一項 **O(n)** 的操作, 但是這個操作讓 `enqueueFront()` 保持在 **O(1)** 的時間複雜度.

> **注意:** 每次發生這個情況的時候都會加入兩倍保留空間的量到陣列前端, 所以當你的陣列越來越大時, 這個操作的次數會越來越少. 這也是 Swift 在陣列後端自動調整大小時的做法.

我們需要做一些像 `dequeue()` 的事情. 如果你常常在陣列後端差入元素, 並且在前端取出他們, 那你個陣列就會變成這樣:


	[ x, x, x, x, x, x, x, x, x, x, x, x, x, x, x, x, x, x, x, x, 1, 2, 3 ]
	                                                              |
	                                                              head

那些前端的空值空間只有在你呼叫 `enqueueFront()` 的時候才會使用到. 但如果當前端插入元素的操作很少時, 這將造成很大的空間浪費. 所以在 `dequeue()` 上下點工:

```swift
  public mutating func dequeue() -> T? {
    guard head < array.count, let element = array[head] else { return nil }

    array[head] = nil
    head += 1

    if capacity > 10 && head >= capacity*2 {
      let amountToRemove = capacity + capacity/2
      array.removeFirst(amountToRemove)
      head -= amountToRemove
      capacity /= 2
    }
    return element
  }
```

<!--
Recall that `capacity` is the original number of empty places at the front of the queue. If the `head` has advanced more to the right than twice the capacity, then it's time to trim off a bunch of these empty spots. We reduce it to about 25%.

For example, this:
-->

`capacity` 是一開始留給前端保留空間的數量. 如果 `head` 已經超過 `capacity` 的兩倍, 那就是該把前端沒用的空間清一清的時候了.
我們將清到剩下 25% 左右.

像這樣:


	[ x, x, x, x, x, x, x, x, x, x, x, x, x, x, x, x, x, x, x, x, 1, 2, 3 ]
	                                |                             |
	                                capacity                      head

<!--
becomes after trimming:
-->

清理後:

	[ x, x, x, x, x, 1, 2, 3 ]
	                 |
	                 head
	                 capacity

<!--
This way we can strike a balance between fast enqueuing and dequeuing at the front and keeping the memory requirements reasonable.

> **Note:** We don't perform trimming on very small arrays. It's not worth it for saving just a few bytes of memory.
-->


這樣我們就可以在前端的插入和取出操作中取得記憶體用量的平衡.

> **注意:** 我們不對小陣列做清裡的動作. 對那一點點記憶體空間是不值得的.

<!--
## See also

Other ways to implement deque are by using a [doubly linked list](../Linked List/), a [circular buffer](../Ring Buffer/), or two [stacks](../Stack/) facing opposite directions.
[A fully-featured deque implementation in Swift](https://github.com/lorentey/Deque)
-->

## 相關閱讀

其他的實作有 [雙端鏈結列表](../Linked List/), [環狀緩衝](../Ring Buffer/) 或是 [堆疊](../Stack/).


[Swift 完整實作雙端佇列](https://github.com/lorentey/Deque)

*Written for Swift Algorithm Club by Matthijs Hollemans*
