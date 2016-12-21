# Priority Queue (優先佇列)
<!--
A priority queue is a [queue](../Queue/) where the most important element is always at the front.

The queue can be a *max-priority* queue (largest element first) or a *min-priority* queue (smallest element first).
-->

優先佇列是一種讓最重要的元素永遠在最前端的[佇列](../Queue/).

這種佇列可以是 *最大優先* (最大的元素在前端) 或  *最小優先* (最小的元素在前端).


<!--
## Why use a priority queue?

Priority queues are useful for algorithms that need to process a (large) number of items and where you repeatedly need to identify which one is now the biggest or smallest -- or however you define "most important".

Examples of algorithms that can benefit from a priority queue:

- Event-driven simulations. Each event is given a timestamp and you want events to be performed in order of their timestamps. The priority queue makes it easy to find the next event that needs to be simulated.
- Dijkstra's algorithm for graph searching uses a priority queue to calculate the minimum cost.
- [Huffman coding](../Huffman Coding/) for data compression. This algorithm builds up a compression tree. It repeatedly needs to find the two nodes with the smallest frequencies that do not have a parent node yet.
- A* pathfinding for artificial intelligence.
- Lots of other places!

With a regular queue or plain old array you'd need to scan the entire sequence over and over to find the next largest item. A priority queue is optimized for this sort of thing.
-->

## 為什麼要使用優先佇列?

優先佇列在需要一直重複的識別那個元素是最大或最小 -- 或個人定義的"重要條件" -- 的演算法中非常有用.

舉幾個使用優先佇列的演算法例子:

- 事件驅動模擬. 每一個事件都會賦予一個時間戳記, 而你希望每個事件依照時間戳記的順序執行. 優先佇列在找尋下一個要被模擬的事件非常容易.
- Dijkstra's 演算法在圖片搜尋中使用優先佇列來計算最小成本.
- [Huffman 編碼](../Huffman Coding/) 壓縮資料. 這個演算法會建立一個壓縮樹. 它會不斷重複尋找頻率最小而又沒有父節點的那兩個節點. 
- 人工智慧的找尋路徑.
- 其他更多地方.

在一般佇列或陣列中, 你需要一次又一次的掃描整個序列來找尋下一個最大的元素. 而優先佇列優化了這個操作.

<!--
## What can you do with a priority queue?

Common operations on a priority queue:

- **Enqueue**: inserts a new element into the queue.
- **Dequeue**: removes and returns the queue's most important element.
- **Find Minimum** or **Find Maximum**: returns the most important element but does not remove it.
- **Change Priority**: for when your algorithm decides that an element has become more important while it's already in the queue.
-->

## 你可以拿優先佇列來做什麼?

一般操作:

- **插入**: 新增一個元素到佇列中.
- **取出**: 回傳並移除佇列中最重要的元素.
- **找最小** or **找最大**: 回傳最重要的元素但是不移除它.
- **改變優先順序**:  當你的演算法決定讓某個已存在佇列中的元素優先順序變得更高.

<!--
## How to implement a priority queue

There are different ways to implement priority queues:

- As a [sorted array](../Ordered Array/). The most important item is at the end of the array. Downside: inserting new items is slow because they must be inserted in sorted order.
- As a balanced [binary search tree](../Binary Search Tree/). This is great for making a double-ended priority queue because it implements both "find minimum" and "find maximum" efficiently.
- As a [heap](../Heap/). The heap is a natural data structure for a priority queue. In fact, the two terms are often used as synonyms. A heap is more efficient than a sorted array because a heap only has to be partially sorted. All heap operations are **O(log n)**.

Here's a Swift priority queue based on a heap:
-->

## 如何實作優先佇列

有幾個不一樣的方法:

- [有序陣列](../Ordered Array/). 最重要的元素總是在陣列的最尾端. 缺點是: 插入元素很慢, 因為保持有序的狀態.
- 平衡的 [二元搜尋樹](../Binary Search Tree/). 這對於建立雙端優先佇列有很大幫助, 因為它優化了 "找最小" 和 "找最大".
- [堆積](../Heap/). 堆積是優先佇列最自然想到的資料結構. 事實上, 這兩個名詞一般是同義的. 堆積比有序陣列更有效率是因為堆積只需要儲存某部分. 所有堆積的操作都是 **O(log n)**.

這裡是 Swift 使用堆積來實作優先佇列:


```swift
public struct PriorityQueue<T> {
  fileprivate var heap: Heap<T>

  public init(sort: (T, T) -> Bool) {
    heap = Heap(sort: sort)
  }

  public var isEmpty: Bool {
    return heap.isEmpty
  }

  public var count: Int {
    return heap.count
  }

  public func peek() -> T? {
    return heap.peek()
  }

  public mutating func enqueue(element: T) {
    heap.insert(element)
  }

  public mutating func dequeue() -> T? {
    return heap.remove()
  }

  public mutating func changePriority(index i: Int, value: T) {
    return heap.replace(index: i, value: value)
  }
}
```

<!--
As you can see, there's nothing much to it. Making a priority queue is easy if you have a [heap](../Heap/) because a heap *is* pretty much a priority queue.

## See also

[Priority Queue on Wikipedia](https://en.wikipedia.org/wiki/Priority_queue)
-->


看起來並沒有很多. 使用 [堆積](../Heap/) 來實作優先佇列是非常簡單的, 因為堆積本身就 *幾乎是* 一個優先佇列.

## 相關閱讀

[優先佇列](https://en.wikipedia.org/wiki/Priority_queue)

*Written for Swift Algorithm Club by Matthijs Hollemans*
