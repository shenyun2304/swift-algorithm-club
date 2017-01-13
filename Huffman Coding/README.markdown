# Huffman Coding (霍夫曼編碼)

<!--
The idea: Encode objects that occur often with a smaller number of bits than objects that occur less frequently.

Although you can encode any type of objects with this scheme, it's most common to compress a stream of bytes. Let's say you have the following text, where each character is one byte:
-->

概念: 將越常出現的物件給予越小的編碼.

雖然你可以將此編碼法用於任何型態的物件, 但它一般使用上還是用在壓縮位元組上. 讓我們舉以下字串做例子, 每個字元是一個 byte:

	so much words wow many compression

<!--
If you count how often each byte appears, you can clearly see that some bytes occur more than others:
-->

如果你計算每個字元的出現次數, 可以很千處的看到有些很常出現:

	space: 5                  u: 1
	    o: 5	              h: 1
	    s: 4	              d: 1
	    m: 3	              a: 1
	    w: 3	              y: 1
	    c: 2	              p: 1
	    r: 2	              e: 1
	    n: 2	              i: 1
	
<!--
We can assign bit strings to each of these bytes. The more common a byte is, the fewer bits we assign to it. We might get something like this:
-->
我們可以賦予每個字元一個位元字串. 越常出現, 字串就越少. 像這樣:


	space: 5    010           u: 1    11001
	    o: 5    000	          h: 1    10001
	    s: 4    101	          d: 1    11010
	    m: 3    111	          a: 1    11011
	    w: 3    0010	      y: 1    01111
	    c: 2    0011	      p: 1    11000
	    r: 2    1001	      e: 1    01110
	    n: 2    0110	      i: 1    10000

<!--
Now if we replace the original bytes with these bit strings, the compressed output becomes:
-->

現在我們將原始字串用位元字串來取代:


	101 000 010 111 11001 0011 10001 010 0010 000 1001 11010 101 
	s   o   _   m   u     c    h     _   w    o   r    d     s
	
	010 0010 000 0010 010 111 11011 0110 01111 010 0011 000 111
	_   w    o   w    _   m   a     n    y     _   c    o   m
	
	11000 1001 01110 101 101 10000 000 0110 0
	p     r    e     s   s   i     o   n

<!--
The extra 0-bit at the end is there to make a full number of bytes. We were able to compress the original 34 bytes into merely 16 bytes, a space savings of over 50%!

But hold on... to be able to decode these bits we need to have the original frequency table. That table needs to be transmitted or saved along with the compressed data, otherwise the decoder doesn't know how to interpret the bits. Because of the overhead of this frequency table (about 1 kilobyte), it doesn't really pay to use Huffman encoding on very small inputs.
-->

最後一個 0 是為了組成完整的 byte. 我們現在可以將一個 34 bytes 的字串壓縮到 16 bytes, 省了超過 50%!

但等等... 要能夠還原位元碼變字元, 我們需要字元與位元的對照表. 這個表需要和壓縮後的資料一起儲存, 不然了話解碼器不知道如何解開這些位元. 因為這個對照表所佔用的空間 (大概是 1k bytes), 對於非常小的資料做霍夫曼壓縮是不划算的.


<!--
## How it works

When compressing a stream of bytes, the algorithm first creates a frequency table that counts how often each byte occurs. Based on this table it creates a binary tree that describes the bit strings for each of the input bytes.

For our example, the tree looks like this:
-->

## 如何運作

當在壓縮位元組時, 演算法先創建一張計數的對照表. 然後再依據這張表建立一個二元樹, 最後會像這樣:

![The compression tree](Images/Tree.png)

<!--
Note that the tree has 16 leaf nodes (the grey ones), one for each byte value from the input. Each leaf node also shows the count of how often it occurs. The other nodes are "intermediate" nodes. The number shown in these nodes is the sum of the counts of their child nodes. The count of the root node is therefore the total number of bytes in the input.

The edges between the nodes either say "1" or "0". These correspond to the bit-encodings of the leaf nodes. Notice how each left branch is always 1 and each right branch is always 0.

Compression is then a matter of looping through the input bytes, and for each byte traverse the tree from the root node to that byte's leaf node. Every time we take a left branch, we emit a 1-bit. When we take a right branch, we emit a 0-bit.

For example, to go from the root node to `c`, we go right (`0`), right again (`0`), left (`1`), and left again (`1`). So the Huffman code for `c` is `0011`. 

Decompression works in exactly the opposite way. It reads the compressed bits one-by-one and traverses the tree until we get to a leaf node. The value of that leaf node is the uncompressed byte. For example, if the bits are `11010`, we start at the root and go left, left again, right, left, and a final right to end up at `d`.
-->

注意到這棵樹有 16 個葉節點 (灰色的那些), 每個節點都代表一個字元. 每個葉節點的數值表示該字元出現的次數. 其他的節點稱為 "中間節點". 中間節點上的數值是它兩個子節點數值的加總. 所以根節點的數值就是總共的字元數.

節點的邊不是 "1" 就是 "0". 這對應著葉節點的位元編碼. 注意到左邊永遠是 1 右邊永遠是 0.

壓縮過程就是從根節點走訪每個葉節點. 而路徑就是該葉節點的位元編碼.

舉例來說, 從根節點走道 `c`, 我們先走右邊 (`0`) -> 右邊 (`0`) -> 左邊 (`1`) -> 左邊 (`1`). 所以 `c` 的霍夫曼編碼就是 `0011`.

解壓縮就是反向操作. 一個一個位元讀取然後走訪樹直到遇到葉節點. 舉例來說, 假設讀取 `11010`, 我們從根節點開始走訪 左 -> 左 -> 右 -> 左 -> 右, 到達葉節點 `d`.


<!--
## The code

Before we get to the actual Huffman coding scheme, it's useful to have some helper code that can write individual bits to an `NSData` object. The smallest piece of data that `NSData` understands is the byte, but we're dealing in bits, so we need to translate between the two.
-->
## 程式碼:

在我們接觸真的霍夫曼編碼之前, 一個可以將位元轉換成 `NSData` 物件的程式碼是有幫助的. 在 `NSData` 中最小的資料大小是 byte (位元組), 可是我們在處理的是位元 (bit), 所以我們需要在這兩者之間作轉換:

```swift
public class BitWriter {
  public var data = NSMutableData()
  var outByte: UInt8 = 0
  var outCount = 0
  
  public func writeBit(bit: Bool) {
    if outCount == 8 {
      data.appendBytes(&outByte, length: 1)
      outCount = 0
    }
    outByte = (outByte << 1) | (bit ? 1 : 0)
    outCount += 1
  }

  public func flush() {
    if outCount > 0 {
      if outCount < 8 {
        let diff = UInt8(8 - outCount)
        outByte <<= diff
      }
      data.appendBytes(&outByte, length: 1)
    }
  }
}
```

<!--
To add a bit to the `NSData` you call `writeBit()`. This stuffs each new bit into the `outByte` variable. Once you've written 8 bits, `outByte` gets added to the `NSData` object for real.

The `flush()` method is used for outputting the very last byte. There is no guarantee that the number of compressed bits is a nice round multiple of 8, in which case there may be some spare bits at the end. If so, `flush()` adds a few 0-bits to make sure that we write a full byte.

We'll use a similar helper object for reading individual bits from `NSData`:
-->

要在 `NSData` 中新增一個位元, 你可以呼叫 `writeBit()`. 它會在每次呼叫就塞一個位元到 `outByte` 變數中. 一但寫入了 8 個位元, `outByte` 就把整個 byte 塞到 `NSData` 裡面.

`flush()` 方法是在處理最後一個位元組. 因為並沒有保證塞進去的位元都可以組成完整的位元組 (8 個倍數), 所以可能需要在尾端塞幾個補充的位元. `flush()` 計算還差多少個位才能組成完整的位元組, 然後塞 0 去填滿最後的位元組.

我們使用類似的物件來從 `NSData` 中讀取每個位元:

```swift
public class BitReader {
  var ptr: UnsafePointer<UInt8>
  var inByte: UInt8 = 0
  var inCount = 8
  
  public init(data: NSData) {
    ptr = UnsafePointer<UInt8>(data.bytes)
  }
  
  public func readBit() -> Bool {
    if inCount == 8 {
      inByte = ptr.memory    // load the next byte
      inCount = 0
      ptr = ptr.successor()
    }
    let bit = inByte & 0x80  // read the next bit
    inByte <<= 1
    inCount += 1
    return bit == 0 ? false : true
  }
}
```

<!--
This works in a similar fashion. We read one whole byte from the `NSData` object and put it in `inByte`. Then `readBit()` returns the individual bits from that byte. Once `readBit()` has been called 8 times, we read the next byte from the `NSData`.

> **Note:** It's no big deal if you're unfamiliar with this sort of bit manipulation. Just know that these two helper objects make it simple for us to write and read bits and not worry about all the messy stuff of making sure they end up in the right place.
-->


原理類似. 我們要從 `NSData` 中讀取一整個 byte 然後放到 `inByte` 變數中. 然後 `readBit()` 方法一個一個回傳該變數中的每個位元, 並以布林值回傳表示. 一旦 `readBit()` 被呼叫了 8 次, 我們就從 `NSData` 中讀取下一個 byte.

> **Note:** 如果你不熟悉位元操作也沒關線. 只要知道這兩個物件是來幫助寫入和讀取位元時不需要去管這些位元的位置是否正確.

<!--
## The frequency table

The first step in Huffman compression is to read the entire input stream and build a frequency table. This table contains a list of all 256 possible byte values and how often each of these bytes occurs in the input data.

We could store this frequency information in a dictionary or an array, but since we're going to need to build a tree anyway, we might as well store the frequency table as the leaves of the tree.

Here are the definitions we need:
-->

## 頻率對照表

霍夫曼壓縮第一步是讀取整個輸入串, 然後建立頻率對照表. 這張表包含了 byte 用位元表示的 256 個組合, 還有每個值相對應的出現次數.

我們可以將頻率資訊存到字典或陣列中, 但因為不管怎樣都得建立一棵樹, 所以乾脆就用樹來儲存頻率對照表.

以下是一些我們需要的定義:

```swift
class Huffman {
  typealias NodeIndex = Int
 
  struct Node {
    var count = 0
    var index: NodeIndex = -1
    var parent: NodeIndex = -1
    var left: NodeIndex = -1
    var right: NodeIndex = -1
  }
  
  var tree = [Node](count: 256, repeatedValue: Node())

  var root: NodeIndex = -1
}
```

<!--
The tree structure is stored in the `tree` array and will be made up of `Node` objects. Since this is a [binary tree](../Binary Tree/), each node needs two children, `left` and `right`, and a reference back to its `parent` node. Unlike a typical binary tree, however, these nodes don't to use pointers to refer to each other but simple integer indices in the `tree` array. (We also store the array `index` of the node itself; the reason for this will become clear later.)

Note that `tree` currently has room for 256 entries. These are for the leaf nodes because there are 256 possible byte values. Of course, not all of those may end up being used, depending on the input data. Later, we'll add more nodes as we build up the actual tree. For the moment there isn't a tree yet, just 256 separate leaf nodes with no connections between them. All the node counts are 0.

We use the following method to count how often each byte occurs in the input data:
-->

樹結構儲存在 `tree` 變數中, 是個內含 `Node` 型別元素的陣列. 因為這是一個 [二元樹](../Binary Tree/), 每個節點有兩個子節點, `left` 和 `right`, 還有該節點父節點的參考 `parent`. 和一般二元樹不同的地方是, 這些節點的 `left`, `right`, `parent` 變數不需要真的儲存到其他節點的參考, 只需要儲存該對應節點在 `tree` 陣列中的索引. (我們也用了 `index` 變數儲存了節點本身在 `tree` 陣列中的索引; 理由待會會解釋清楚.)

注意現在 `tree` 有 256 個空間. 因為對於葉節點來說, 總共會有 256 種不同的 byte 組合. 當然, 並不一定全部都會用到, 決定於資料內容. 稍後, 我們將會增加節點來建立真正的樹. 在這個時候樹還沒有完成, 只有 256 個沒有連線的葉節點. 每個節點的數值都是 0.

我們使用下面的方法來計算資料中每個 byte 發生的次數:

```swift
  private func countByteFrequency(data: NSData) {
    var ptr = UnsafePointer<UInt8>(data.bytes)
    for _ in 0..<data.length {
      let i = Int(ptr.memory)
      tree[i].count += 1
      tree[i].index = i
      ptr = ptr.successor()
    }
  }
```

<!--
This steps through the `NSData` object from beginning to end and for each byte increments the `count` of the corresponding leaf node. That's all there is to it. After `countByteFrequency()` completes, the first 256 `Node` objects in the `tree` array represent the frequency table.

Now, I mentioned that in order to decompress a Huffman-encoded block of data, you also need to have the original frequency table. If you were writing the compressed data to a file, then somewhere in the file you'd include the frequency table.

You could simply dump the first 256 elements from the `tree` array but that's not very efficient. It's likely that not all of these 256 elements will be used, plus you don't need to serialize the `parent`, `right`, and `left` pointers. All we need is the frequency information, not the entire tree.

Instead, we'll add a method to export the frequency table without all the pieces we don't need:
-->

此步驟我們從頭到尾一個一個 byte 走訪 `NSData` 物件, 每次走訪就把該 byte 當作索引在 `tree` 中把對應元素的 `count` 加一. 在 `countByteFrequency()` 完成後, 第一個包含 256 個 `Node` 物件的 `tree` 陣列就是頻率對照表了.

為了要解碼, 你需要有原始的頻率對照表. 如果你要將壓縮的資料儲存到檔案中, 那你需要將對照表也一併存進去.

你可以簡單的把 `tree` 中整個 256 個元素都存到檔案中, 不過那樣非常沒效率. 因為並不是真的 256 個元素都有用到, 更何況你不需要序列化 `parent`, `right`, `left` 參考指標. 我們只需要知道頻率對照資訊, 並非整棵樹.

所以, 我們需要一個方法來匯出頻率對照表需要的部分:

```swift
  struct Freq {
    var byte: UInt8 = 0
    var count = 0
  }
  
  func frequencyTable() -> [Freq] {
    var a = [Freq]()
    for i in 0..<256 where tree[i].count > 0 {
      a.append(Freq(byte: UInt8(i), count: tree[i].count))
    }
    return a
  }
```

<!--
The `frequencyTable()` method looks at those first 256 nodes from the tree but keeps only those that are actually used, without the `parent`, `left`, and `right` pointers. It returns an array of `Freq` objects. You have to serialize this array along with the compressed data so that it can be properly decompressed later.
-->

`frequencyTable()` 方法先走訪整個 256 個節點, 但是只留下真的有用到的 byte (`count > 0`), 也沒記錄 `parent`, `left`, 和 `right` 的參考指標. 這方法回傳一個內含 `Freq` 物件的陣列. 你需要把這個陣列和壓縮後的的資料一起做序列化, 這樣存到檔案後才能解碼.

<!--
## The tree

As a reminder, there is the compression tree for the example:
-->

## 樹

作為提醒, 這是個壓縮資料的樹範例:

![The compression tree](Images/Tree.png)

<!--
The leaf nodes represent the actual bytes that are present in the input data. The intermediary nodes connect the leaves in such a way that the path from the root to a frequently-used byte value is shorter than the path to a less common byte value. As you can see, `m`, `s`, space, and `o` are the most common letters in our input data and therefore they are highest up in the tree.

To build the tree, we do the following:

1. Find the two nodes with the smallest counts that do not have a parent node yet.
2. Create a new parent node that links these two nodes together.
3. This repeats over and over until only one node with no parent remains. This becomes the root node of the tree.

This is an ideal place to use a [priority queue](../Priority Queue/). A priority queue is a data structure that is optimized so that finding the minimum value is always very fast. Here, we repeatedly need to find the node with the smallest count.
-->

葉節點是在資料中真的有出現的 byte. 中間節點以出現次數越多離根節點越近, 出現次數越少離根節點越遠的原則, 將這些葉節點連結起來. 可以看到 `m`, `s`, 空白鍵 和 `o` 是最常出現的字元, 它們在樹中也在相對高層的地方.

要建立這個樹, 我們依照以下步驟:

1. 找到兩個數值(`count`)最少而且沒有父節點的節點.
2. 建立一個新的節點當此兩節點的父節點.
3. 以上步驟一直重複直到只剩下一個節點. 此節點就是樹的根節點.

這是個使用 [優先佇列](../Priority Queue/). 優先佇列是個優化搜尋最小值速度的資料結構. 這裡, 我們需要重複地找尋最小值的節點.

<!--
The function `buildTree()` then becomes:
-->

`buildTree()` 函式變成這樣:

```swift
  private func buildTree() {
    var queue = PriorityQueue<Node>(sort: { $0.count < $1.count })
    for node in tree where node.count > 0 {
      queue.enqueue(node)                            // 1
    }

    while queue.count > 1 {
      let node1 = queue.dequeue()!                   // 2
      let node2 = queue.dequeue()!

      var parentNode = Node()                        // 3
      parentNode.count = node1.count + node2.count
      parentNode.left = node1.index
      parentNode.right = node2.index
      parentNode.index = tree.count
      tree.append(parentNode)

      tree[node1.index].parent = parentNode.index    // 4
      tree[node2.index].parent = parentNode.index
      
      queue.enqueue(parentNode)                      // 5
    }

    let rootNode = queue.dequeue()!                  // 6
    root = rootNode.index
  }
```

<!--
Here is how it works step-by-step:

1. Create a priority queue and enqueue all the leaf nodes that have at least a count of 1. (If the count is 0, then this byte value did not appear in the input data.) The `PriorityQueue` object sorts the nodes by their count, so that the node with the lowest count is always the first one that gets dequeued.

2. While there are at least two nodes left in the queue, remove the two nodes that are at the front of the queue. Since this is a min-priority queue, this gives us the two nodes with the smallest counts that do not have a parent node yet.

3. Create a new intermediate node that connects `node1` and `node2`. The count of this new node is the sum of the counts of `node1` and `node2`. Because the nodes are connected using array indices instead of real pointers, we use `node1.index` and `node2.index` to find these nodes in the `tree` array. (This is why a `Node` needs to know its own index.)

4. Link the two nodes into their new parent node. Now this new intermediate node has become part of the tree.

5. Put the new intermediate node back into the queue. At this point we're done with `node1` and `node2`, but the `parentNode` stills need to be connected to other nodes in the tree.

6. Repeat steps 2-5 until there is only one node left in the queue. This becomes the root node of the tree, and we're done.

The animation shows what the process looks like:
-->

一步一步說明:

1. 建立一個優先佇列並將所有數值大於 0 的葉節點插入到佇列中歐（如果 `count` 是 0, 表示這個 byte 並沒有出現在資料中.) `PriorityQueue` 物件依照節點的 `count` 來排序, 所以有越小值的節點就會在佇列的越前端.

2. 因為佇列中至少有兩個節點, 取出最前端的兩個節點. 因為這是個最小優先佇列, 所以我們獲得的兩個節點一定是數值最小的節點, 而且尚未有父節點.
3. 建立一個連結 `node1` 和 `node2` 的中間節點. 這個節點的數值就是 `node1` 和 `node2` 數值的和. 這裡節點之間的連結是使用陣列的索引, 並非真的參考指標, 我們使用 `node1.index` 和 `node2.index` 去找到這些節點在 `tree` 陣列中的位置. (這就是為什麼節點要知道自己的陣列索引)
4. 將這兩個節點和此中間節點連結, 此中間節點就為這兩節點的父節點, 這時候這個中間節點就進入到樹中了.
5. 把此新的中間節點放到佇列中. 因為我們已經處理完 `node1` 和 `node2` 了, 但是這個新的 `parentNode` 還要去和其他節點連結.
6. 重複步驟 2-5 直到佇列中只剩下一個節點. 這個節點將變成樹的根節點, 而這時候我們就把樹完成了.

![Building the tree](Images/BuildTree.gif)

<!--
> **Note:** Instead of using a priority queue, you can repeatedly iterate through the `tree` array to find the next two smallest nodes, but that makes the compressor quite slow, **O(n^2)**. Using the priority queue, the running time is only **O(n log n)** where **n** is the number of nodes.

> **Fun fact:** Due to the nature of binary trees, if we have *x* leaf nodes we can at most add *x - 1* additional nodes to the tree. Given that at most there will be 256 leaf nodes, the tree will never contain more than 511 nodes total.
-->

> **注意:** 除了使用優先佇列以外, 也可以迭代整個 `tree` 陣列來找最小的兩個節點, 但是那樣佔用 **O(n^2)** 時間. 使用優先佇列, 時間複雜度是 **O(n log n)**, **n** 是節點傯個數.

> **有趣的事:** 基於二元樹的特性, 如果我們有 *x* 個葉節點, 那我們最多只會有 *x - 1* 個其他節點. 這可以知道就算有 256 個葉節點, 樹的總結點數也不會超過 511 個.


<!--
## Compression

Now that we know how to build the compression tree from the frequency table, we can use it to compress the contents of an `NSData` object. Here is the code:
-->

## 壓縮

現在我們知道要怎麼從頻率對照表來創建一個壓縮樹, 我們可以使用他來壓縮 `NSData` 的物件了:


```swift
  func compressData(data: NSData) -> NSData {
    countByteFrequency(data)
    buildTree()
    
    let writer = BitWriter()
    var ptr = UnsafePointer<UInt8>(data.bytes)

    for _ in 0..<data.length {
      let c = ptr.memory
      let i = Int(c)
      traverseTree(writer: writer, nodeIndex: i, childIndex: -1)
      ptr = ptr.successor()
    }

    writer.flush()
    return writer.data
  }
```

<!--
This first calls `countByteFrequency()` to build the frequency table, then `buildTree()` to put together the compression tree. It also creates a `BitWriter` object for writing individual bits. 

Then it loops through the entire input and for each byte calls `traverseTree()`. That method will step through the tree nodes and for each node write a 1 or 0 bit. Finally, we return the `BitWriter`'s data object.

> **Note:** Compression always requires two passes through the entire input data: first to build the frequency table, second to convert the bytes to their compressed bit sequences.

The interesting stuff happens in `traverseTree()`. This is a recursive method:
-->

先呼叫 `countByteFrequency()` 來建立頻率對照表, 然後呼叫 `buildTree()` 然建立壓縮樹. 然後建立 `BitWriter` 物件來準備寫入每個位元.

走訪整個輸入資料的每個 byte 並呼叫 `traverseTree()`. 這方法會走訪樹中的葉, 然後寫入 1 或 0 的位元值. 最後我們回傳 `BitWriter` 的 `data` 屬性物件.

在 `traverseTree()` 中有趣的是, 這是個遞迴方法:

```swift
  private func traverseTree(writer writer: BitWriter, nodeIndex h: Int, childIndex child: Int) {
    if tree[h].parent != -1 {
      traverseTree(writer: writer, nodeIndex: tree[h].parent, childIndex: h)
    }
    if child != -1 {
      if child == tree[h].left {
        writer.writeBit(true)
      } else if child == tree[h].right {
        writer.writeBit(false)
      }
    }
  }
```

<!--
When we call this method from `compressData()`, the `nodeIndex` parameter is the array index of the leaf node for the byte that we're about to encode. This method recursively walks the tree from a leaf node up to the root, and then back again. 

As we're going back from the root to the leaf node, we write a 1 bit or a 0 bit for every node we encounter. If a child is the left node, we emit a 1; if it's the right node, we emit a 0.

In a picture:
-->

當我們從 `compressData()` 中呼叫這個方法, `nodeIndex` 參數是葉節點的陣列索引, 也就是我們要編碼的那個 byte. 這個方法會把 `nodeIndex` 參數放父節點的索引並向上遞迴, 直到觸碰根節點為止, 然後再一路回傳回來.

在我們從根節點回傳回葉節點過程中, 我們對每個走訪的節點寫入 1 或 0 的位元值. 如果子節點是左節點, 我們寫入 1; 右節點, 我們寫入 0.

如圖:

![How compression works](Images/Compression.png)

<!--
Even though the illustration of the tree shows a 0 or 1 for each edge between the nodes, the bit values 0 and 1 aren't actually stored in the tree! The rule is that we write a 1 bit if we take the left branch and a 0 bit if we take the right branch, so just knowing the direction we're going in is enough to determine what bit value to write.

You use the `compressData()` method as follows:
-->

討論中我們看到樹中節點的兩個邊都有顯示 0 或 1, 但是 0 和 1 並沒有真的儲存在樹中! 規則就是我們遇到左子節點寫入 1, 遇到右子節點寫入 0, 所以我們只需要知道走訪的方向就可以知道要寫入什麼值.

你可以這樣使用 `compressData()`:


```swift
let s1 = "so much words wow many compression"
if let originalData = s1.dataUsingEncoding(NSUTF8StringEncoding) {
  let huffman1 = Huffman()
  let compressedData = huffman1.compressData(originalData)
  print(compressedData.length)
}
```

<!--
## Decompression

Decompression is pretty much compression in reverse. However, the compressed bits are useless to us without the frequency table. Earlier you saw the `frequencyTable()` method that returns an array of `Freq` objects. The idea is that if you were saving the compressed data into a file or sending it across the network, you'd also save that `[Freq]` array along with it.

We first need some way to turn the `[Freq]` array back into a compression tree. Fortunately, this is pretty easy:
-->

## 解壓縮

解壓縮就像是反向的壓縮. 無論如何, 壓縮後的資料如果沒有頻率對照表了話, 就一點用都沒有. 先前的 `frequencyTable()` 方法回傳一個 `Freq` 的陣列. 概念是如果你將壓縮後的資料存到檔案中或從網路傳送出去, 你需要把 `[Freq]` 也一起存起來或送出去.

```swift
  private func restoreTree(frequencyTable: [Freq]) {
    for freq in frequencyTable {
      let i = Int(freq.byte)
      tree[i].count = freq.count
      tree[i].index = i
    }
    buildTree()
  }
```

<!--
We convert the `Freq` objects into leaf nodes and then call `buildTree()` to do the rest.

Here is the code for `decompressData()`, which takes an `NSData` object with Huffman-encoded bits and a frequency table, and returns the original data:
-->

將 `Freq` 轉成葉節點然後呼叫 `buildTree()` 處理剩下的事情.

這裡是 `decompressData()` 的程式碼, 接受一個經過霍夫曼編碼後的 `NSData` 物件, 和對應的頻率對照表, 回傳原始資料:

```swift
  func decompressData(data: NSData, frequencyTable: [Freq]) -> NSData {
    restoreTree(frequencyTable)

    let reader = BitReader(data: data)
    let outData = NSMutableData()
    let byteCount = tree[root].count

    var i = 0
    while i < byteCount {
      var b = findLeafNode(reader: reader, nodeIndex: root)
      outData.appendBytes(&b, length: 1)
      i += 1
    }
    return outData
  }
```

<!--
This also uses a helper method to traverse the tree:
-->

一個好用的方法來走訪樹:

```swift
  private func findLeafNode(reader reader: BitReader, nodeIndex: Int) -> UInt8 {
    var h = nodeIndex
    while tree[h].right != -1 {
      if reader.readBit() {
        h = tree[h].left
      } else {
        h = tree[h].right
      }
    }
    return UInt8(h)
  }
```

<!--
`findLeafNode()` walks the tree from the root down to the leaf node given by `nodeIndex`. At each intermediate node we read a new bit and then step to the left (bit is 1) or the right (bit is 0). When we get to the leaf node, we simply return its index, which is equal to the original byte value.

In a picture:
-->

`findLeafNode()` 從根節點走訪到葉節點, 方向由 `nodeIndex` 決定. 在每個中間節點我們讀取一個位元並決定要走左 (該位元是 1) 或是右 (該位元是 0). 當我們到達葉節點, 我們直接回傳該葉節點的陣列索引, 也是資料的原始 byte 值.

![How decompression works](Images/Decompression.png)

<!--
Here's how you would use the decompression method:
-->

這是如何使用解壓縮方法:

```swift
  let frequencyTable = huffman1.frequencyTable()
  
  let huffman2 = Huffman()
  let decompressedData = huffman2.decompressData(compressedData, frequencyTable: frequencyTable)
  
  let s2 = String(data: decompressedData, encoding: NSUTF8StringEncoding)!
```

<!--
First you get the frequency table from somewhere (in this case the `Huffman` object we used to encode the data) and then call `decompressData()`. The string that results should be equal to the one you compressed in the first place.

You can see how this works in more detail in the Playground.
-->

首先獲得頻率對照表 (在此例中就是我們拿來壓縮資料的 `Huffman` 物件). 然後呼叫 `decompressData()`. 最後的字串應該和你一開始壓縮的字串是一樣的.

<!--
## See also
-->

## 相關閱讀

[Huffman coding at Wikipedia](https://en.wikipedia.org/wiki/Huffman_coding)

<!--
The code is loosely based on Al Stevens' C Programming column from Dr.Dobb's Magazine, February 1991 and October 1992.
-->

此程式碼是參考 1991 年 2 月 和 10 月的 Dr.Dobb 雜誌的 Al Stevens' C Programming 專欄. 

*Written for Swift Algorithm Club by Matthijs Hollemans*
