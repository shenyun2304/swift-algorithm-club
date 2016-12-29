# Stack (堆疊)

<!--
A stack is like an array but with limited functionality. You can only *push* to add a new element to the top of the stack, *pop* to remove the element from the top, and *peek* at the top element without popping it off.
-->
堆疊類似只有部分功能的陣列，可以使用 _push_ 新增元素到堆疊的最上層，使用 _pop_ 去取得並移除最上層的元素，使用 _peek_ 取得最上層元素但不移除。


<!--
Why would you want to do this? Well, in many algorithms you want to add objects to a temporary list at some point and then pull them off this list again at a later time. Often the order in which you add and remove these objects matters.
-->
為什麼想要這種結構? 在很多演算法中，你會希望在某些時間點新增一個物件到暫時的列表中，並在接下來的某時間移除他。有時候這種新增和移除的順序是你想要的。

<!--
A stack gives you a LIFO or last-in first-out order. The element you pushed last is the first one to come off with the next pop. (A very similar data structure, the [queue](../Queue/), is FIFO or first-in first-out.)
-->

堆疊實現了 LIFO 的順序，你最後新增的元素將會是你最優先移出的元素。(有個非常像似的資料結構 - [佇列](../Queue)，實現了 FIFO 的順序。)

<!--
For example, let's push a number on the stack:
-->

舉例來說，將一個數字新增到堆疊之中:

```swift
stack.push(10)
```
<!--
The stack is now `[ 10 ]`. Push the next number:
-->

現在這個堆疊是 `[ 10 ]`. 再新增一個數字:

```swift
stack.push(3)
```

<!--
The stack is now `[ 10, 3 ]`. Push one more number:
-->


現在這個堆疊是 ` [ 10, 3 ] `. 再新增一個數字:

```swift
stack.push(57)
```

<!--
The stack is now `[ 10, 3, 57 ]`. Let's pop the top number off the stack:
-->


現在這個堆疊是 ` [ 10, 3, 57 ] `. 讓我們來取得並移除最上層的元素:

```swift
stack.pop()
```

<!--
This returns `57`, because that was the most recent number we pushed. The stack is `[ 10, 3 ]` again.
-->


這個動作會回傳 `57`，因為他是最後才加入的元素。而現在此堆疊回到 ` [ 10, 3 ] `。

```swift
stack.pop()
```

<!--
This returns `3`, and so on. If the stack is empty, popping returns `nil` or in some implementations it gives an error message ("stack underflow").
-->


這次回傳 `3`，以此類推。如果從一個沒有元素的堆疊中取得元素，將會回傳 `nil` 或者錯誤訊息 ("stack underflow")。


<!--
A stack is easy to create in Swift. It's just a wrapper around an array that just lets you push, pop, and peek:
-->

在 swift 中要創建一個堆疊很簡單，只要包覆一個陣列並實現新增、取得移除、取得三種方法:

```swift
public struct Stack<T> {
  fileprivate var array = [T]()

  public var isEmpty: Bool {
    return array.isEmpty
  }

  public var count: Int {
    return array.count
  }

  public mutating func push(_ element: T) {
    array.append(element)
  }

  public mutating func pop() -> T? {
    return array.popLast()
  }

  public func peek() -> T? {
    return array.last
  }
}
```


<!--
Notice that a push puts the new element at the end of the array, not the beginning. Inserting at the beginning of an array is expensive, an **O(n)** operation, because it requires all existing array elements to be shifted in memory. Adding at the end is **O(1)**; it always takes the same amount of time, regardless of the size of the array.
-->


注意到新增元素時是新增到陣列的尾端，不是前端。新增到陣列的前端是昂貴的 **O(n)** 操作，因為這樣的動作需要所有陣列中的元素做搬移的動作。然而新增到尾端是 **O(1)**; 這樣的操作無論陣列大小時間都一樣。


<!--
Fun fact about stacks: Each time you call a function or a method, the CPU places the return address on a stack. When the function ends, the CPU uses that return address to jump back to the caller. That's why if you call too many functions -- for example in a recursive function that never ends -- you get a so-called "stack overflow" as the CPU stack has run out of space.
-->

一些關於堆疊有趣的事: 每次調用函式或方法時，CPU會將當時程序的指標置放到一個堆疊中，當函式或方法結束，CPU會利用剛剛的堆疊來獲得最後的程序指標位置並繼續往下執行。所以當你執行太多函式 -- 例如一個無線的遞迴函式 -- 你會得到一個叫做 "stack overflow" 的訊息，表示 CPU 的堆疊已經滿了。

*Written for Swift Algorithm Club by Matthijs Hollemans*
