# Linked List (鏈結列表)

<!--
A linked list is a sequence of data items, just like an array. But where an array allocates a big block of memory to store the objects, the elements in a linked list are totally separate objects in memory and are connected through links:
-->

鏈結列表事一個資料的序列, 跟陣列很像. 但是陣列是一個大型連續的記憶體空間, 連結陣列中的物件幾乎都是分散在記憶體之中的, 互相使用鏈結來連接:

	+--------+    +--------+    +--------+    +--------+
	|        |    |        |    |        |    |        |
	| node 0 |--->| node 1 |--->| node 2 |--->| node 3 |
	|        |    |        |    |        |    |        |
	+--------+    +--------+    +--------+    +--------+

<!--
The elements of a linked list are referred to as *nodes*. The above picture shows a *singly linked list*, where each node only has a reference -- or a "pointer" -- to the next node. In a *doubly linked list*, shown below, nodes also have pointers to the previous node:
-->

在鏈結列表中的元素就像 *節點* 一樣. 上面展示一個 *單向鏈結列表*, 每個節點含有到下一個節點的參考 -- 或稱 "指標". 在 *雙向鏈結列表* 中, 節點還會有前一個節點的指標.

	+--------+    +--------+    +--------+    +--------+
	|        |--->|        |--->|        |--->|        |
	| node 0 |    | node 1 |    | node 2 |    | node 3 |
	|        |<---|        |<---|        |<---|        |
	+--------+    +--------+    +--------+    +--------+

<!--
You need to keep track of where the list begins. That's usually done with a pointer called the *head*:
-->


你需要追蹤列表的起點. 通常用一個指標 (*head*) 就解決了:

	         +--------+    +--------+    +--------+    +--------+
	head --->|        |--->|        |--->|        |--->|        |---> nil
	         | node 0 |    | node 1 |    | node 2 |    | node 3 |
	 nil <---|        |<---|        |<---|        |<---|        |<--- tail
	         +--------+    +--------+    +--------+    +--------+

<!--
It's also useful to have a reference to the end of the list, known as the *tail*. Note that the "next" pointer of the last node is `nil`, just like the "previous" pointer of the very first node.
-->

在結尾有個指標也挺有用的, 如圖中的 *tail*. 注意到最後一個元素的 "下一個" 指標是 `nil`, 就像第一個元素的 "前一個" 指標一樣.

<!--
## Performance of linked lists

Most operations on a linked list have **O(n)** time, so linked lists are generally slower than arrays. However, they are also much more flexible -- rather than having to copy large chunks of memory around as with an array, many operations on a linked list just require you to change a few pointers.

The reason for the **O(n)** time is that you can't simply write `list[2]` to access node 2 from the list. If you don't have a reference to that node already, you have to start at the `head` and work your way down to that node by following the `next` pointers (or start at the `tail` and work your way back using the `previous` pointers).

But once you have a reference to a node, operations like insertion and deletion are really quick. It's just that finding the node is slow.

This means that when you're dealing with a linked list, you should insert new items at the front whenever possible. That is an **O(1)** operation. Likewise for inserting at the back if you're keeping track of the `tail` pointer.
-->

## 表現

大部分的鏈結列表都是 **O(n)** 時間, 所以鏈結列表通常是比陣列還要慢的.
不過呢, 鏈結列表更以彈性 -- 比起陣列那種要使用整區的記憶體空間, 很多鏈結料表的操作只需要改變幾個指標.

會是 **O(n)** 的原因是因為你無法用 `list[2]` 來取得列表中第 2 個節點. 如果你不是已經獲得該節點的指標, 你就必須要從 `head` 開始一個一個找到下一個節點的指標. (或從 `tail` 開始一一的往前一個節點走訪).

但是一但你有某個節點的指標, 在做插入和刪除是非常快速的. 只有在找節點的步驟上是比較慢的.

這表示當你在處理鏈結列表時, 你應該盡可能地將元素插入到列表的前方. 這是個 **O(1)** 的操作. 當然如果你都從 `tail` 開始尋找, 那你就應該把元素插入到列表的後方.

<!--
## Singly vs doubly linked lists

A singly linked list uses a little less memory than a doubly linked list because it doesn't need to store all those `previous` pointers.

But if you have a node and you need to find its previous node, you're screwed. You have to start at the head of the list and iterate through the entire list until you get to the right node.

For many tasks, a doubly linked list makes things easier.
-->

## 單向 vs 雙向 鏈結列表

單向鏈結列表因為不需要儲存 `前一個` 節點的指標, 所以使用較少的記憶體資源.

但如果你有某一個節點你需要知道他前一個節點, 你就爆炸了. 你需要從頭開始走訪整個列表直到你找到正確的節點.

大部分的任務中, 雙向的鏈結列表會讓事情變得比較簡單.

<!--
## Why use a linked list?

A typical example of where to use a linked list is when you need a [queue](../Queue/). With an array, removing elements from the front of the queue is slow because it needs to shift down all the other elements in memory. But with a linked list it's just a matter of changing `head` to point to the second element. Much faster.

But to be honest, you hardly ever need to write your own linked list these days. Still, it's useful to understand how they work; the principle of linking objects together is also used with [trees](../Tree/) and [graphs](../Graph/).
-->

## 為什麼要使用鏈結列表?

有個典型要使用鏈結列表的例子是當你需要一個 [佇列](../Queue/). 對於陣列來說, 要移除陣列前端的元素是相對慢的, 因為這的操作要移動其他元素在記憶體中的位置. 但使用鏈結列表, 只需要讓 `head` 指向第二個節點就好了. 快多了!

但老實說, 現在你也很少會自己去撰寫鏈結列表. 不過, 了解它是怎麼運作, 還是非常有用的; 把物件連結起來的原理還有 [樹](../Tree/) 和 [圖](../Graph/)

<!--
## The code

We'll start by defining a type to describe the nodes:
-->
## 程式碼

我們從定義節點開始:

```swift
public class LinkedListNode<T> {
  var value: T
  var next: LinkedListNode?
  weak var previous: LinkedListNode?

  public init(value: T) {
    self.value = value
  }
}
```
<!--
This is a generic type, so `T` can be any kind of data that you'd like to store in the node. We'll be using strings in the examples that follow.

Ours is a doubly-linked list and each node has a `next` and `previous` pointer. These can be `nil` if there are no next or previous nodes, so these variables must be optionals. (In what follows, I'll point out which functions would need to change if this was just a singly- instead of a doubly-linked list.)

> **Note:** To avoid ownership cycles, we declare the `previous` pointer to be weak. If you have a node `A` that is followed by node `B` in the list, then `A` points to `B` but also `B` points to `A`. In certain circumstances, this ownership cycle can cause nodes to be kept alive even after you deleted them. We don't want that, so we make one of the pointers `weak` to break the cycle.

Let's start building `LinkedList`. Here's the first bit:
-->

這是一個泛型的型別, `T` 可以是任何一種資料型別, 就看你想要的節點是哪種型別的. 接下來我們將使用字串來當例子.

我們使用雙向鏈結列表, 每個節點有 `next` 和 `previous` 的指標. 它們有可能是 `nil`, 所以變數必須宣告為 optional. (下面我會指出如果使用單向鏈結列表了話, 哪些函式需要改變.)

> **注意:** 為了避免所有權相互參考, 我們將 `previous` 指標設為 weak. 如果你有個 `A` 節點後面接 `B`, 那 `A` 會有個指標變數指向 `B`, `B` 也會有指標變數指向 `A`. 這種情況, 會導致節點被刪除後還是不會被釋放. 我們不想那樣, 所以我們將其中一個指標定為 `weak` 來避免這種情況.

讓我們建立一個 `LinkedList`:


```swift
public class LinkedList<T> {
  public typealias Node = LinkedListNode<T>

  private var head: Node?
  
  public var isEmpty: Bool {
    return head == nil
  }
  
  public var first: Node? {
    return head
  }
}
```

<!--
Ideally, I'd like to put the `LinkedListNode` class inside `LinkedList` but Swift currently doesn't allow generic types to have nested types. Instead we're using a typealias so inside `LinkedList` we can write the shorter `Node` instead of `LinkedListNode<T>`.

This linked list only has a `head` pointer, not a tail. Adding a tail pointer is left as an exercise for the reader. (I'll point out which functions would be different if we also had a tail pointer.)

The list is empty if `head` is nil. Because `head` is a private variable, I've added the property `first` to return a reference to the first node in the list.

You can try it out in a playground like this:
-->

理想情況上, 我希望能把 `LinkedListNode` 類別放到 `LinkedList` 裡面, 但是 Swift 目前不允許這種寫法. 不過我們可以用 typealias 將 `Node` 設為 `LinkedListNode<T>`.

此鏈結列表只有一個 `head` 的指標, 不是 `tail`. 加入 `tail` 指標就留給讀者練習囉. (我會指出哪些函式會因為加入了 `tail` 指標而需要調整)

如果是空的列表, 那 `head` 指標就是空指標. 因為 `head` 是私有變數, 所以我加入了一個 `first` 的屬性來回傳列表中的第一個節點.

你可以在 playground 中試試看:

```swift
let list = LinkedList<String>()
list.isEmpty   // true
list.first     // nil
```

<!--
Let's also add a property that gives you the last node in the list. This is where it starts to become interesting:
-->

讓我們再加入一個屬性會回傳最後一個節點. 這時候就開始有趣了:

```swift
  public var last: Node? {
    if var node = head {
      while case let next? = node.next {
        node = next
      }
      return node
    } else {
      return nil
    }
  }
```

<!--
If you're new to Swift, you've probably seen `if let` but maybe not `if var`. It does the same thing -- it unwraps the `head` optional and puts the result in a new local variable named `node`. The difference is that `node` is not a constant but an actual variable, so we can change it inside the loop.

The loop also does some Swift magic. The `while case let next? = node.next` bit keeps looping until `node.next` is nil. You could have written this as follows:
-->

如果你是 Swift 的新手, 你應該已經看過 `if let` 但可能沒看過 `if var`. 它們是一樣的事情 -- 它將解開 `head` 的 optional 餅把結果放到新的區域變數 `node` 中. 不同的是 `node` 不是一個常數, 而是一個變數, 所以可以在迴圈中改變他的值.

這個迴圈也是一些 Swift 的特殊語法. `while case let next? = node.next` 持續走訪直到 `node.next` 是 `nil`. 你也可以寫成像下面這樣:

```swift
      var node: Node? = head
      while node != nil && node!.next != nil {
        node = node!.next
      }
```

<!--
But that doesn't feel very Swifty to me. We might as well make use of Swift's built-in support for unwrapping optionals. You'll see a bunch of these kinds of loops in the code that follows.

> **Note:** If we kept a tail pointer, `last` would simply do `return tail`. But we don't, so it has to step through the entire list from beginning to the end. It's an expensive operation, especially if the list is long.

Of course, `last` only returns nil because we don't have any nodes in the list. Let's add a method that adds a new node to the end of the list:
-->

但這樣讓我感覺非常不 Swifty. 我們可以使用 Swift 內建的解 optional 支援. 你會在接下來的程式中看到大量的這種迴圈.

> **注意:** 如果我們保留尾端指標, `last` 就是簡單的執行 `return tail`. 但我們沒有, 所以這個動作需要從頭到尾走訪整個列表. 這是個昂貴的操作, 特別在列表元素很多的時候.

當然, `last` 只會回傳 `nil` 因為我們現在的列表沒有任何元素. 讓我們寫一個在列表尾端增加節點的方法:

```swift
  public func append(value: T) {
    let newNode = Node(value: value)
    if let lastNode = last {
      newNode.previous = lastNode
      lastNode.next = newNode
    } else {
      head = newNode
    }
  }
```

<!--
The `append()` method first creates a new `Node` object and then asks for the last node using the `last` property we've just added. If there is no such node, the list is still empty and we make `head` point to this new `Node`. But if we did find a valid node object, we connect the `next` and `previous` pointers to link this new node into the chain. A lot of linked list code involves this kind of `next` and `previous` pointer manipulation.

Let's test it in the playground:
-->

`append()` 方法先建立了一個新的 `Node` 物件, 然後用 `last` 屬性去找最後一個節點. 如果列表是空的, 那我們就將 `head` 指向這個新的 `Node`. 但如果我們有找到最尾端節點, 我們將新節點的 `previous` 設定為原本的的最尾端節點, 然後將原本最尾端節點的 `next` 設為此新節點. 很多鏈結列表的操作都牽涉到 `next` 和 `prevous` 指標的操作.

讓我們在 playground 中測試看看:


```swift
list.append("Hello")
list.isEmpty         // false
list.first!.value    // "Hello"
list.last!.value     // "Hello"
```

<!--
The list looks like this:
-->

這個列表看起來像這樣:

	         +---------+
	head --->|         |---> nil
	         | "Hello" |
	 nil <---|         |
	         +---------+

<!--
Now add a second node:
-->

現在增加第二個節點:

```swift
list.append("World")
list.first!.value    // "Hello"
list.last!.value     // "World"
```

<!--
And the list looks like:
-->

現在列表像這樣:

	         +---------+    +---------+
	head --->|         |--->|         |---> nil
	         | "Hello" |    | "World" |
	 nil <---|         |<---|         |
	         +---------+    +---------+

<!--
You can verify this for yourself by looking at the `next` and `previous` pointers:
-->

你可以用 `next` 和 `previous` 指標來驗證看看:

```swift
list.first!.previous          // nil
list.first!.next!.value       // "World"
list.last!.previous!.value    // "Hello"
list.last!.next               // nil
```

<!--
Let's add a method to count how many nodes are in the list. This will look very similar to what we did already:
-->

讓我們撰寫一個方法來計數列表中有多少個節點. 和我們之前做得非常類似:

```swift
  public var count: Int {
    if var node = head {
      var c = 1
      while case let next? = node.next {
        node = next
        c += 1
      }
      return c
    } else {
      return 0
    }
  }
```

<!--
It loops through the list in the same manner but this time increments a counter as well.

> **Note:** One way to speed up `count` from **O(n)** to **O(1)** is to keep track of a variable that counts how many nodes are in the list. Whenever you add or remove a node, you also update this variable.

What if we wanted to find the node at a specific index in the list? With an array we can just write `array[index]` and it's an **O(1)** operation. It's a bit more involved with linked lists, but again the code follows a similar pattern:
-->

它走訪整個列表然後遞增計數器.

> **注意:** 要將計算 `count` 從 **O(n)** 降到 **O(1)** 的辦法是追蹤新增和刪除節點, 並在每次操作後都更新 `count` 變數.

如果我們想要依照索引找到列表中某個特定的節點? 在陣列中我們可以直接寫 `array[index]` 而且是個 **O(1)** 的操作. 不過在鏈結列表中, 會有比較多的步驟, 不過呢, 和之前的流程也差不多:

```swift
  public func nodeAt(_ index: Int) -> Node? {
    if index >= 0 {
      var node = head
      var i = index
      while node != nil {
        if i == 0 { return node }
        i -= 1
        node = node!.next
      }
    }
    return nil
  }
```

<!--
The loop looks a little different but it does the same thing: it starts at `head` and then keeps following the `node.next` pointers to step through the list. We're done when we've seen `index` nodes, i.e. when the counter has reached 0.

Try it out:
-->

這迴圈看起來有一點不同但是還是做一樣的事情: 從 `head` 開始然後持續地走訪 `node.next`, 直到我們到達 `index` 節點, 即當 `i` 達到 0.

試試看:

```swift
list.nodeAt(0)!.value    // "Hello"
list.nodeAt(1)!.value    // "World"
list.nodeAt(2)           // nil
```

<!--
For fun we can implement a `subscript` method too:
-->

我們也可以實現 `subscript` 方法:

```swift
  public subscript(index: Int) -> T {
    let node = nodeAt(index)
    assert(node != nil)
    return node!.value
  }
```

<!--
Now you can write the following:
-->

現在你可以這樣寫了:

```swift
list[0]   // "Hello"
list[1]   // "World"
list[2]   // crash!
```

<!--
It crashes on `list[2]` because there is no node at that index.

So far we've written code to add new nodes to the end of the list, but that's slow because you need to find the end of the list first. (It would be fast if we used a tail pointer.) For this reason, if the order of the items in the list doesn't matter, you should insert at the front of the list instead. That's always an **O(1)** operation.

Let's write a method that lets you insert a new node at any index in the list. First, we'll define a helper function:
-->

在 `list[2]` 的時候壞掉了, 因為該索引沒有節點.

到目前為止我們撰寫了在尾端新增節點的程式碼, 但那樣很慢, 因為你需要先找到列表的尾端. (如果有個尾端的指標那會快很多.) 因此, 如果列表中元素順序並不重要, 你應該將元素從前端插入. 這樣就會是個 **O(1)** 的操作.

來寫個可以插入元素到任意索引的方法吧. 首先, 我們定義一些有幫助的函式:


```swift
  private func nodesBeforeAndAfter(index: Int) -> (Node?, Node?) {
    assert(index >= 0)
    
    var i = index
    var next = head
    var prev: Node?

    while next != nil && i > 0 {
      i -= 1
      prev = next
      next = next!.next
    }
    assert(i == 0)

    return (prev, next)
  }
```

<!--
This returns a tuple containing the node currently at the specified index and the node that immediately precedes it, if any. The loop is very similar to `nodeAtIndex()`, except that here we also keep track of what the previous node is as we iterate through the list. 

Let's look at an example. Suppose we have the following list:
-->

這會回傳一個包含指定索引的節點和該節點下一個節點的元組. 這跟 `nodeAtIndex()` 非常像, 除了我們同時追蹤走訪節點的前一個節點.

來看看一個範例. 假設我們有以下的列表:

	head --> A --> B --> C --> D --> E --> nil

<!--
We want to find the nodes before and after index 3. As we start the loop, `i = 3`, `next` points at `"A"`, and `prev` is nil. 
-->

我們想找到索引 3 和前一個節點. 當我開始時, `i = 3`, `next` 指向 `"A"`, 而 `prev` 是空指標.

	head --> A --> B --> C --> D --> E --> nil
	        next

<!--
We decrement `i`, make `prev` point to `"A"`, and move `next` to the next node, `"B"`:
-->

遞減 `i`, `prev` 這時候指向 `"A"`, 而 `next` 指向 `"B"`:

	head --> A --> B --> C --> D --> E --> F --> nil
	        prev  next

<!--
Again, we decrement `i` and update the pointers. Now `prev` points to `"B"`, and `next` points to `"C"`:
-->

在遞減 `i`. 現在 `prev` 指向 `"B"`, 而 `next` 指向 `"C"`:

	head --> A --> B --> C --> D --> E --> F --> nil
	              prev  next

<!--
As you can see, `prev` always follows one behind `next`. We do this one more time and then `i` equals 0 and we exit the loop:
-->

可以看, `prev` 總是在 `next` 之前. 我們在做一次遞減 `i` 就是 0 了, 然後會離開迴圈:

	head --> A --> B --> C --> D --> E --> F --> nil
	                    prev  next

<!--
The `assert()` after the loop checks whether there really were enough nodes in the list. If `i > 0` at this point, then the specified index was too large.

> **Note:** If any of the loops in this article don't make much sense to you, then draw a linked list on a piece of paper and step through the loop by hand, just like what we did here.

For this example, the function returns `("C", "D")` because `"D"` is the node at index 3 and `"C"` is the one right before that.

Now that we have this helper function, we can write the method for inserting nodes:
-->

`assert()` 會檢查是不是已經走訪足夠的節點. 如果 `i > 0`, 那表示指定的索引比列表長度還要大.

> **注意:** 如果這篇文章中的迴圈你不太熟悉, 那在紙上畫一個鏈結列表, 然後手動的將迴圈跑一遍, 就像我們之前做的那樣.

此範例中, 回傳 `("C", "D")`, 因為 `"D"` 是在索引 3 的節點, 而 `"C"` 是它前一個節點.

現在有這個幫助函式, 我們可以撰寫插入節點的方法:


```swift
  public func insert(value: T, atIndex index: Int) {
    let (prev, next) = nodesBeforeAndAfter(index)     // 1
    
    let newNode = Node(value: value)    // 2
    newNode.previous = prev
    newNode.next = next
    prev?.next = newNode
    next?.previous = newNode

    if prev == nil {                    // 3
      head = newNode
    }
  }
```

<!--
Some remarks about this method:

1. First, we need to find where to insert this node. After calling the helper method, `prev` points to the previous node and `next` is the node currently at the given index. We'll insert the new node in between these two. Note that `prev` can be nil (index is 0), `next` can be nil (index equals size of the list), or both can be nil if the list is empty.

2. Create the new node and connect the `previous` and `next` pointers. Because the local `prev` and `next` variables are optionals and may be nil, so we use optional chaining here.

3. If the new node is being inserted at the front of the list, we need to update the `head` pointer. (Note: If the list had a tail pointer, you'd also need to update that pointer here if `next == nil`, because that means the last element has changed.)

Try it out:
-->

方法備註:

1. 首先, 我們要知道在哪裡插入這個節點. 在呼叫玩這個幫助方法後, `prev` 指向前一個節點而 `next` 是現在正在該索引的節點. 我們要將新的節點插入到這兩個節點中間. 注意到 `prev` 有可能是空指標 (索引是 0), `next` 也可能是空指標 (索引和列表長度相同), 或是兩者都是空指標 (陣列無元素).
2. 建立一個新的節點並連接 `previous` 和 `next` 指標. 因為這邊的 `prev` 和 `next` 區域變數有可能是空指標, 所以需要用到 optional 鏈來指派.
3. 如果新的節點要從列表前端插入, 那我們需要更新 `head` 指標 (注意: 如果列表有尾端指標, 如果 `next == nil`, 那你同時也需要更新尾端指標)

試試看:

```swift
list.insert("Swift", atIndex: 1)
list[0]     // "Hello"
list[1]     // "Swift"
list[2]     // "World"
```

<!--
Also try adding new nodes to the front and back of the list, to verify that this works properly.

> **Note:** The `nodesBeforeAndAfter()` and `insert(atIndex)` functions can also be used with a singly linked list because we don't depend on the node's `previous` pointer to find the previous element.

What else do we need? Removing nodes, of course! First we'll do `removeAll()`, which is really simple:
-->

也試試看在列表前端和後端插入節點, 驗證是否可以正常運作.

> **注意:** `nodesBeforeAndAfter()` 和 `insert(atIndex)` 函式也可以在單向鏈結列表中使用, 因為我們並沒有用到 `previous` 指標.

我們還需要什麼? 刪除節點? 當然! 我們先來做 `removeAll()`, 非常簡單:

```swift
  public func removeAll() {
    head = nil
  }
```

<!--
If you had a tail pointer, you'd set it to `nil` here too.

Next we'll add some functions that let you remove individual nodes. If you already have a reference to the node, then using `removeNode()` is the most optimal because you don't need to iterate through the list to find the node first. 
-->

如果你有尾端指標, 你也可以將它設為 `nil`.

接下來我們增加一些函式讓你可以刪除個別節點. 如果你已經有該節點的指標, 那使用 `removeNode()` 是最好的方法, 因為你不需要去走訪整個列表來找到該節點.


```swift
  public func remove(node: Node) -> T {
    let prev = node.previous
    let next = node.next
    
    if let prev = prev {
      prev.next = next
    } else {
      head = next
    }
    next?.previous = prev
    
    node.previous = nil
    node.next = nil
    return node.value
  }
```

<!--
When we take this node out of the list, we break the links to the previous node and the next node. To make the list whole again we must connect the previous node to the next node.

Don't forget the `head` pointer! If this was the first node in the list then `head` needs to be updated to point to the next node. (Likewise for when you have a tail pointer and this was the last node). Of course, if there are no more nodes left, `head` should become nil.

Try it out:
-->

當我們要把這個節點從列表中移走的時候, 我們是將它和它前後的節點參考指標破壞掉. 但為了讓整個列表完整, 我們也需要把前一個節點和後一個節點連起來.

別忘記 `head` 指標! 如果要刪除的是列表中的第一個元素, 那 `head` 就要更新到下一個節點. (如果你有尾端指標, 而要移除最後的節點, 也是要處理). 當然, 如果刪完以後都沒有節點了, 那 `head` 就應該是空指標.

試試看:

```swift
list.remove(list.first!)   // "Hello"
list.count                     // 2
list[0]                        // "Swift"
list[1]                        // "World"
```

<!--
If you don't have a reference to the node, you can use `removeLast()` or `removeAt()`:
-->

如果你沒有該節點的指標, 你可以用 `removeLast()` 或 `removeAt()`:

```swift
  public func removeLast() -> T {
    assert(!isEmpty)
    return remove(node: last!)
  }

  public func removeAt(_ index: Int) -> T {
    let node = nodeAt(index)
    assert(node != nil)
    return remove(node: node!)
  }
```

<!--
All these removal functions also return the value from the removed element. 
-->

所有這些移除的函式都會回傳要被移除那個節點的值.


```swift
list.removeLast()              // "World"
list.count                     // 1
list[0]                        // "Swift"

list.removeAt(0)          // "Swift"
list.count                     // 0
```

<!--
> **Note:** For a singly linked list, removing the last node is slightly more complicated. You can't just use `last` to find the end of the list because you also need a reference to the second-to-last node. Instead, use the `nodesBeforeAndAfter()` helper method. If the list has a tail pointer, then `removeLast()` is really quick, but you do need to remember to make `tail` point to the previous node.

There's a few other fun things we can do with our `LinkedList` class. It's handy to have some sort of readable debug output:
-->

> **注意:** 對於單向鏈結列表, 要移除尾端節點會多一點工要做. 你不能只是用 `last` 去找到尾端節點, 因為你還需要找到尾端的前一個節點. 相反的, 使用 `nodesBeforeAndAfter()` 方法. 如果列表有尾端指標, 那使用 `removeLast()` 是最快速的, 但你需要將 `tail` 指派到倒數第二個節點.

我們還可以再加一點有趣的東西給我們的 `LinkedList`. 例如讓他的 debug 輸出更具可讀性:

```swift
extension LinkedList: CustomStringConvertible {
  public var description: String {
    var s = "["
    var node = head
    while node != nil {
      s += "\(node!.value)"
      node = node!.next
      if node != nil { s += ", " }
    }
    return s + "]"
  }
}
```

<!--
This will print the list like so:
-->

結果會變成:

	[Hello, Swift, World]

<!--
How about reversing a list, so that the head becomes the tail and vice versa? There is a very fast algorithm for that:
-->

反轉列表如何, 讓列表的頭尾互換? 有一個非常快的演算法來處理這件事:


```swift
  public func reverse() {
    var node = head
    while let currentNode = node {
      node = currentNode.next
      swap(&currentNode.next, &currentNode.previous)
      head = currentNode
    }
  }
```

<!--
This loops through the entire list and simply swaps the `next` and `previous` pointers of each node. It also moves the `head` pointer to the very last element. (If you had a tail pointer you'd also need to update it.) You end up with something like this:
-->

走訪整個列表然後簡單地將 `next` 和 `previous` 指標互換. 這也將 `head` 移動到最後一個元素. (如果你有尾端指標, 你也需要更新它.) 你會得到:

	         +--------+    +--------+    +--------+    +--------+
	tail --->|        |<---|        |<---|        |<---|        |---> nil
	         | node 0 |    | node 1 |    | node 2 |    | node 3 |
	 nil <---|        |--->|        |--->|        |--->|        |<--- head
	         +--------+    +--------+    +--------+    +--------+

<!--
Arrays have `map()` and `filter()` functions, and there's no reason why linked lists shouldn't either.
-->

陣列有 `map()` 和 `filter()` 函式, 沒有理由鏈結列表沒有.

```swift
  public func map<U>(transform: T -> U) -> LinkedList<U> {
    let result = LinkedList<U>()
    var node = head
    while node != nil {
      result.append(transform(node!.value))
      node = node!.next
    }
    return result
  }
```

<!--
You can use it like this:
-->

你可以這樣用:

```swift
let list = LinkedList<String>()
list.append("Hello")
list.append("Swifty")
list.append("Universe")

let m = list.map { s in s.characters.count }
m  // [5, 6, 8]
```

<!--
And here's filter:
-->

這裡是 `filter`:

```swift
  public func filter(predicate: T -> Bool) -> LinkedList<T> {
    let result = LinkedList<T>()
    var node = head
    while node != nil {
      if predicate(node!.value) {
        result.append(node!.value)
      }
      node = node!.next
    }
    return result
  }
```

<!--
And a silly example:
-->

簡單的範例:

```swift
let f = list.filter { s in s.characters.count > 5 }
f    // [Universe, Swifty]
```

<!--
Exercise for the reader: These implementations of `map()` and `filter()` aren't very fast because they `append()` the new node to the end of the new list. Recall that append is **O(n)** because it needs to scan through the entire list to find the last node. Can you make this faster? (Hint: keep track of the last node that you added.)
-->

給讀者了練習: 這兩個 `map()` 和 `filter()` 實作的並沒有很快速, 因為他們使用 `append()` 在新列表的尾端新增節點. 回想一下 `append` 是 **O(n)** 時間, 因為他需要掃描整個列表然後找到尾端節點. 你可以讓這個更快嗎? (提示: 追蹤尾端節點)

<!--
## An alternative approach

The version of `LinkedList` you've seen so far uses nodes that are classes and therefore have reference semantics. Nothing wrong with that, but that does make them a bit more heavyweight than Swift's other collections such as `Array` and `Dictionary`.

It is possible to implement a linked list with value semantics using an enum. That would look somewhat like this:
-->

## 另一個趨近

這個 `LinkedList` 的版本使用傳址的節點物件. 這沒什麼問題, 但是的確讓這個結構比 Swift 原生的 Array` 和 `Dictionary` 還要耗資源一點.

使用列舉傳值來實作是可以的. 看起來會像這樣:


```swift
enum ListNode<T> {
  indirect case Node(T, next: ListNode<T>)
  case End
}
```

<!--
The big difference with the class-based version is that any modification you make to this list will result in a *new copy* being created. Whether that's what you want or not depends on the application.

[I might fill out this section in more detail if there's a demand for it.]
-->

和物件參考傳址版本最大的差異在於每次你對列表做了任何操作都會建立一個該列表的 *新複製*. 這根據你的應用來決定要用哪種了.

[如果有需要我會再詳細的解釋這個部分]

<!--
## Some things to keep in mind

Linked lists are flexible but many operations are **O(n)**.

When performing operations on a linked list, you always need to be careful to update the relevant `next` and `previous` pointers, and possibly also the `head` and `tail` pointers. If you mess this up, your list will no longer be correct and your program will likely crash at some point. Be careful!

When processing lists, you can often use recursion: process the first element and then recursively call the function again on the rest of the list. You’re done when there is no next element. This is why linked lists are the foundation of functional programming languages such as LISP.
-->

## 一些要注意的地方

鏈結列表示很具有彈性但是大部分的操作都是 **O(n)**.

當在鏈結列表上執行操作時, 你需要很小心地更新 `next` 和 `previous` 的指標, 還有注意 `head` 和 `tail`. 如果不小心搞砸了, 你整個列表將不再正確, 而你的程式有可能在某些時候會崩潰. 要小心!

當使用列表時, 你可以常使用遞迴: 處理第一個元素並遞迴的對剩下的元素呼叫該方法. 當沒有下一個元素時你就做完了. 這就是為什麼鏈結列表是某些程序導向語言的基礎, 像是 LISP.


*Written for Swift Algorithm Club by Matthijs Hollemans*
