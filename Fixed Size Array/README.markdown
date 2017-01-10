# Fixed-Size Arrays (固定長度陣列)

<!--
Early programming languages didn't have very fancy arrays. You'd create the array with a specific size and from that moment on it would never grow or shrink. Even the standard arrays in C and Objective-C are still of this type.

When you define an array like so,
-->

早期的程式語言沒有功能很強的陣列物件. 創建一個陣列在初始化時必須指定大小而且無法再改變大小. 就算到現在, 在 C 或 Objective-C 中的標準陣列也還是這種形態.

	int myArray[10];

<!--
the compiler allocates one contiguous block of memory that can hold 40 bytes (assuming an `int` is 4 bytes):
-->

編譯器會配置一個連續的 40 bytes 記憶體空間給這個陣列 (假設 `int` 是 4 bytes):

![An array with room for 10 elements](Images/array.png)

<!--
That's your array. It will always be this size. If you need to fit more than 10 elements, you're out of luck... there is no room for it.

To get an array that grows when it gets full you need to use a [dynamic array](https://en.wikipedia.org/wiki/Dynamic_array) object such as `NSMutableArray` in Objective-C or `std::vector` in C++, or a language like Swift whose arrays increase their capacity as needed.

A major downside of the old-style arrays is that they need to be big enough or you run out of space. But if they are too big you're wasting memory. And you need to be careful about security flaws and crashes due to buffer overflows. In summary, fixed-size arrays are not flexible and they leave no room for error.

That said, **I like fixed-size arrays** because they are simple, fast, and predictable.

The following operations are typical for an array:

- append a new element to the end
- insert a new element at the beginning or somewhere in the middle
- delete an element
- look up an element by index
- count the size of the array

For a fixed-size array, appending is easy as long as the array isn't full yet:
-->

這就是你的陣列, 而它永遠就是這個大小. 如果你需要它能夠多塞 10 個元素, 那是無解的.

一個可以調整大小的陣列可以使用 [動態陣列](https://en.wikipedia.org/wiki/Dynamic_array) 物件像是 Objective-C 中的 `NSMutableArray` 或 C++ 中的 `std::vector` 更或者類似 Swift 的程式語言, 陣列本身是可調整大小的.

舊式的陣列缺點是你必須要被它空間上的大小所限制. 但是如果一開始就創建很大個陣列, 會造成空間上的浪費, 而太小又不夠用. 而你在安全性和緩衝記憶體上溢位上要非常注意. 固定長度的陣列是沒有彈性也沒有任何錯誤的空間.

雖然這樣說, **我喜歡固定長度陣列**, 因為他們簡單、快速、可預知.

以下是典型的陣列操作:

- 在尾端新增元素
- 在前端或中間任何位置插入元素
- 刪除元素
- 以索引查找元素
- 計算陣列長度

對於固定長度陣列, 如果陣列還沒滿的情況下在尾端新增元素是很簡單的:

![Appending a new element](Images/append.png)

<!--
Looking up by index is also quick and easy:
-->

以索引來查找也是快速又簡單的:

![Indexing the array](Images/indexing.png)

<!--
These two operations have complexity **O(1)**, meaning the time it takes to perform them is independent of the size of the array.

For an array that can grow, appending is more involved: if the array is full, new memory must be allocated and the old contents copied over to the new memory buffer. On average, appending is still an **O(1)** operation, but what goes on under the hood is less predictable.

The expensive operations are inserting and deleting. When you insert an element somewhere that's not at the end, it requires moving up the remainder of the array by one position. That involves a relatively costly memory copy operation. For example, inserting the value `7` in the middle of the array:
-->

這兩個操作是複雜度 **O(1)** 的操作, 這表示實際所需使用的時間受陣列的長度的影響.

對於一個可以擴大的陣列, 新增元素時, 如果陣列已經滿了, 就會配置新的記憶體資源, 然後把舊的陣列複製過去新的記憶體空間. 平均來說, 新增還是一個 **O(1)** 的操作, 但實際上到底會是多少也無法預測.

最昂貴的操作是插入和刪除. 當你想在陣列中間插入一個元素, 這動作需要把該索引之後的元素都向後移動一個位置. 這也包含的記憶體複製的操作. 舉例還說, 要將 `7` 插入到陣列中間:

![Insert requires a memory copy](Images/insert.png)

<!--
If your code was using any indexes into the array beyond the insertion point, these indexes are now referring to the wrong objects.

Deleting requires a copy the other way around:
-->

這時候如果你使用插入點後面的索引, 這些索引現在的值已經不同了.

刪除元素一樣需要複製操作, 只是另一種形式:

![Delete also requires a memory copy](Images/delete.png)

<!--
This, by the way, is also true for `NSMutableArray` or Swift arrays. Inserting and deleting are **O(n)** operations -- the larger the array the more time it takes.

Fixed-size arrays are a good solution when:

1. You know beforehand the maximum number of elements you'll need. In a game this could be the number of sprites that can be active at a time. It's not unreasonable to put a limit on this. (For games it's a good idea to allocate all the objects you need in advance anyway.)
2. It is not necessary to have a sorted version of the array, i.e. the order of the elements does not matter.

If the array does not need to be sorted, then an `insertAt(index)` operation is not needed. You can simply append any new elements to the end, until the array is full.

The code for adding an element becomes:
-->

順帶一提, `NSMutableArray` 和 Swift 中的 array 也是這樣. 插入和刪除是 **O(n)** 的操作 -- 陣列越大則耗時越久.

固定長度陣列對某些情況是很好的解法:

1. 事先知道最多元素的數量. 在遊戲中, 操作可能牽扯到一堆元素需要同時連動. 將這種情況的數量限制是合理的. (對於遊戲來說, 事先把所有元素都產生好是個不錯的方式)

2. 排序不是必須, 即陣列中的元素順序並不重要.

如果一個陣列不一定是要排序的, 那 `insertAt(index)` 的操作是不必要的. 你可以簡單的將元素插入到尾端, 直到陣列空間滿.

新增元素程式碼:

```swift
func append(_ newElement: T) {
  if count < maxSize {
    array[count] = newElement
    count += 1
  }
}
```

<!--
The `count` variable keeps track of the size of the array and can be considered the index just beyond the last element. That's the index where you'll insert the new element.

Determining the number of elements in the array is just a matter of reading the `count` variable, a **O(1)** operation.

The code for removing an element is equally simple:
-->

`count` 變數持續的追蹤陣列目前擁有元素的數量, 此變數的值可視為最尾端元素的下一個索引. 就是你可以插入新元素的索引.

要知道陣列目前有多少個元素只需要讀取 `count` 的值就好, 一個 **O(1)** 的操作.

移除元素程式碼:

```swift
func removeAt(index: Int) {
  count -= 1
  array[index] = array[count]
}
```

<!--
This copies the last element on top of the element you want to remove, and then decrements the size of the array.
-->

這的操作把陣列最後一個元素覆寫到要刪除的索引中.

![Deleting just means copying one element](Images/delete-no-copy.png)


<!--
This is why the array is not sorted. To avoid an expensive copy of a potentially large portion of the array we copy just one element, but that does change the order of the elements.

There are now two copies of element `6` in the array, but what was previously the last element is no longer part of the active array. It's just junk data -- the next time you append an new element, this old version of `6` will be overwritten.

Under these two constraints -- a limit on the number of elements and an unsorted array -- fixed-size arrays are still perfectly suitable for use in modern software.

Here is an implementation in Swift:
-->

這就是為什麼陣列沒有排序. 為了避免昂貴的複製操作, 我們只簡單的覆寫一個元素, 但是這的確改變了元素的順序.

現在陣列中有兩個 `6` 的元素, 但是最後一個已經不是陣列的一部分了. 那只是個垃圾資料 -- 下一次你新增元素時, 這個值將會被覆寫.

在這兩個限制下 -- 最大長度和無序元素 -- 固定長度陣列在現代軟體中還是非常好用的.


```swift
struct FixedSizeArray<T> {
  private var maxSize: Int
  private var defaultValue: T
  private var array: [T]
  private (set) var count = 0
  
  init(maxSize: Int, defaultValue: T) {
    self.maxSize = maxSize
    self.defaultValue = defaultValue
    self.array = [T](repeating: defaultValue, count: maxSize)
  }
  
  subscript(index: Int) -> T {
    assert(index >= 0)
    assert(index < count)
    return array[index]
  }
  
  mutating func append(_ newElement: T) {
    assert(count < maxSize)
    array[count] = newElement
    count += 1
  }
  
  mutating func removeAt(index: Int) -> T {
    assert(index >= 0)
    assert(index < count)
    count -= 1
    let result = array[index]
    array[index] = array[count]
    array[count] = defaultValue
    return result
  }
  
  mutating func removeAll() {
    for i in 0..<count {
      array[i] = defaultValue
    }
    count = 0
  }
}
```

<!--
When creating the array, you specify the maximum size and a default value:
-->

當你創建陣列的時候, 你需要指定最大長度和元素預設值:

```swift
var a = FixedSizeArray(maxSize: 10, defaultValue: 0)
```

<!--
Note that `removeAt(index: Int)` overwrites the last element with this `defaultValue` to clean up the "junk" object that gets left behind. Normally it wouldn't matter to leave that duplicate object in the array, but if it's a class or a struct it may have strong references to other objects and it's good boyscout practice to zero those out.
-->

這邊注意到 `removeAt(index: Int)` 會將最後一個元素覆寫為 `defaultValue` 來清除 `垃圾` 資料. 正常來說, 不覆寫最後一個元素也不會怎樣, 但如果這是個 class 或 struct, 有可能對其他物件的強連結, 還有將不用的元素清空也是個好習慣.


*Written for Swift Algorithm Club by Matthijs Hollemans*
