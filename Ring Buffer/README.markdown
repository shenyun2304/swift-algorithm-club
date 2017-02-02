# Ring Buffer (環狀緩衝)

<!--
Also known as a circular buffer.

The problem with a [queue based on an array](../Queue/) is that adding new items to the back of the queue is fast, **O(1)**, but removing items from the front of the queue is slow, **O(n)**. Removing is slow because it requires the remaining array elements to be shifted in memory.

A more efficient way to implement a queue is to use a ring buffer or circular buffer. This is an array that conceptually wraps around back to the beginning, so you never have to remove any items. All operations are **O(1)**.

Here is how it works in principle. We have an array of a fixed size, say 5 items:
-->

也被稱為 circular buffer.

在 [使用陣列實現佇列](../Queue/) 中有個問題是在佇列的尾端增加元素是很快的, **O(1)**, 但是從佇列前端移除元素是很慢的, **O(n)**. 移除元素會很慢因為需要將剩下的元素都向前移動一個記憶體位置.

有個更有效率實現佇列的方法是使用環狀緩衝. 這是一個在概念上將尾端和前端接在一起的陣列, 所以永遠不需要移除任何元素. 所有的操作都是 **O(1)**.

下面是展示動作流程的原理. 假設我們有個固定長度為 5 個陣列:



	[    ,    ,    ,    ,     ]
	 r
	 w

<!--
Initially, the array is empty and the read (`r`) and write (`w`) pointers are at the beginning.

Let's add some data to this array. We'll write, or "enqueue", the number `123`:
-->
初始時, 陣列是空的, 讀取指標 (`r`) 和寫入指標 (`w`) 都指向陣列起頭.

來加入一點資料. 我們將會寫入, 或者說是 "enqueue" 數字 `123`:

	[ 123,    ,    ,    ,     ]
	  r
	  ---> w

<!--
Each time we add data, the write pointer moves one step forward. Let's add a few more elements:
-->

每次增加資料, 寫入指標就像後移動一個位置. 讓我們再增加一些元素:

	[ 123, 456, 789, 666,     ]
	  r    
	       -------------> w

<!--
There is now one open spot left in the array, but rather than enqueuing another item the app decides to read some data. That's possible because the write pointer is ahead of the read pointer, meaning data is available for reading. The read pointer advances as we read the available data:
-->

現在陣列中只剩下一個空位置, 但與其繼續插入資料, 這時候 app 決定先讀取一些資料. 因為寫入指標比讀取指標還要超前, 表示資料是可以被讀取的. 讀取完後讀取指標向後移動一個位置:

	[ 123, 456, 789, 666,     ]
	  ---> r              w

<!--
Let's read two more items:
-->

再讀取兩個元素:


	[ 123, 456, 789, 666,     ]
	       --------> r    w

<!--
Now the app decides it's time to write again and enqueues two more data items, `333` and `555`:
-->

現在 app 決定要再次寫入兩筆資料, `333` 和 `555`:


	[ 123, 456, 789, 666, 333 ]
	                 r    ---> w

<!--
Whoops, the write pointer has reached the end of the array, so there is no more room for object `555`. What now? Well, this is why it's a circular buffer: we wrap the write pointer back to the beginning and write the remaining data:
-->

Whoops, 寫入指標到達了陣列的尾端, 所以表示已經沒有空間給 `555`. 現在怎麼辦? 這就是為什麼要用環狀緩衝: 我們把寫入指標移動到陣列的起頭索引然後覆寫在該位置的資料:


	[ 555, 456, 789, 666, 333 ]
	  ---> w         r        

<!--
We can now read the remaining three items, `666`, `333`, and `555`.
-->

我們現在可以繼續讀取剩下的三個元素, `666`, `333` 和 `555`.

	[ 555, 456, 789, 666, 333 ]
	       w         --------> r        

<!--
Of course, as the read pointer reaches the end of the buffer it also wraps around:
-->

當然, 當讀取指標到達陣列結尾時, 下一個指標會指向陣列起頭:

	[ 555, 456, 789, 666, 333 ]
	       w            
	  ---> r

<!--
And now the buffer is empty again because the read pointer has caught up with the write pointer.

Here is a very basic implementation in Swift:
-->

現在緩衝已經空了, 因為讀取指標已經趕上寫入指標了.

這裡是非常基礎的實作:

```swift
public struct RingBuffer<T> {
  fileprivate var array: [T?]
  fileprivate var readIndex = 0
  fileprivate var writeIndex = 0

  public init(count: Int) {
    array = [T?](repeating: nil, count: count)
  }

  public mutating func write(_ element: T) -> Bool {
    if !isFull {
      array[writeIndex % array.count] = element
      writeIndex += 1
      return true
    } else {
      return false
    }
  }

  public mutating func read() -> T? {
    if !isEmpty {
      let element = array[readIndex % array.count]
      readIndex += 1
      return element
    } else {
      return nil
    }
  }

  fileprivate var availableSpaceForReading: Int {
    return writeIndex - readIndex
  }

  public var isEmpty: Bool {
    return availableSpaceForReading == 0
  }

  fileprivate var availableSpaceForWriting: Int {
    return array.count - availableSpaceForReading
  }

  public var isFull: Bool {
    return availableSpaceForWriting == 0
  }
}
```

<!--
The `RingBuffer` object has an array for the actual storage of the data, and `readIndex` and `writeIndex` variables for the "pointers" into the array. The `write()` function puts the new element into the array at the `writeIndex`, and the `read()` function returns the element at the `readIndex`.

But hold up, you say, how does this wrapping around work? There are several ways to accomplish this and I chose a slightly controversial one. In this implementation, the `writeIndex` and `readIndex` always increment and never actually wrap around. Instead, we do the following to find the actual index into the array:
-->

`RingBuffer` 物件有個陣列來儲存資料, `readIndex` 和 `writeIndex` 變數用來表示陣列的 "指標". `write()` 函式將新的元素插入到 `writeIndex` 的位置, 而 `read()` 函式回傳 `readIndex` 所指的元素.

等等, 那細節是怎樣做的? 有很多方法可以達到, 在這邊我選擇一個有點爭議的方法. 在此次實作中, `writeIndex` 和  `readIndex` 會一直遞增且永遠不會重置. 相反的, 我們這樣做:

```swift
array[writeIndex % array.count]
```

and:

```swift
array[readIndex % array.count]
```

<!--
In other words, we take the modulo (or the remainder) of the read index and write index divided by the size of the underlying array.

The reason this is a bit controversial is that `writeIndex` and `readIndex` always increment, so in theory these values could become too large to fit into an integer and the app will crash. However, a quick back-of-the-napkin calculation should take away those fears.

Both `writeIndex` and `readIndex` are 64-bit integers. If we assume that they are incremented 1000 times per second, which is a lot, then doing this continuously for one year requires `ceil(log_2(365 * 24 * 60 * 60 * 1000)) = 35` bits. That leaves 28 bits to spare, so that should give you about 2^28 years before running into problems. That's a long time. :-)

To play with this ring buffer, copy the code to a playground and do the following to mimic the example above:
-->

換句話說, 我們使用讀取指標和寫入指標除以陣列長度後的餘數來當位置指標.

這邊有個小爭議是 `writeIndex` 和 `readIndex` 會一直遞增, 所以理論上會有整數溢位的問題. 無論如何, 我們總是可以使用很簡單的計算來解決這件事情.

`writeIndex` 和 `readIndex` 是 64 位元的整數. 假設他們每秒遞增 1000 次, 持續一年了話需要 `ceil(log_2(365 * 24 * 60 * 60 * 1000)) = 35` 位元. 還留下了 25 個位元沒用到, 所以這表示你還有 2^28 年才會遇到這個問題. 時間還挺長的. :-)

要玩玩這個環狀緩衝, 複製下面的程式碼並在 playground 中貼上來模仿上述的例子:


```swift
var buffer = RingBuffer<Int>(count: 5)

buffer.write(123)
buffer.write(456)
buffer.write(789)
buffer.write(666)

buffer.read()   // 123
buffer.read()   // 456
buffer.read()   // 789

buffer.write(333)
buffer.write(555)

buffer.read()   // 666
buffer.read()   // 333
buffer.read()   // 555
buffer.read()   // nil
```

<!--
You've seen that a ring buffer can make a more optimal queue but it also has a disadvantage: the wrapping makes it tricky to resize the queue. But if a fixed-size queue is suitable for your purposes, then you're golden.

A ring buffer is also very useful for when a producer of data writes into the array at a different rate than the consumer of the data reads it. This happens often with file or network I/O. Ring buffers are also the preferred way of communicating between high priority threads (such as an audio rendering callback) and other, slower, parts of the system.

The implementation given here is not thread-safe. It only serves as an example of how a ring buffer works. That said, it should be fairly straightforward to make it thread-safe for a single reader and single writer by using `OSAtomicIncrement64()` to change the read and write pointers.

A cool trick to make a really fast ring buffer is to use the operating system's virtual memory system to map the same buffer onto different memory pages. Crazy stuff but worth looking into if you need to use a ring buffer in a high performance environment.
-->

你已經看到環狀緩衝可以更優化陣列, 但它有個缺點: 這樣的包裝讓調整佇列的大小很棘手. 但是在固定大小佇列條件上, 它是黃金選項.

當資料進入和取出的速度不同時, 環狀緩衝是非常有用的. 這通常在檔案或網路的輸入/輸出上常使用. 環狀緩衝在高度優先的執行緒和其他執行緒之間的通訊中也非常有用.

這邊的實作並非 thread-safe. 這裡只是簡單的展示環狀緩衝如何運作. 雖然這樣說, 改用 `OSAtomicIncrement64()` 來做讀取和寫入指標, 對於單一的讀取者和單一的寫入者, 要做成 thread-safe 應該還滿直觀的.

有個很酷的技巧來讓環狀緩衝更快. 使用作業系統的虛擬記憶體系統來將同一個緩衝映射到不同的記憶體片段. 挺瘋狂的但是如果你需要將環狀緩衝調整到很高的效能, 倒是值得試試看

*Written for Swift Algorithm Club by Matthijs Hollemans*
