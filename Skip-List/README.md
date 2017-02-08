# Skip List (跳躍列表)

<!--
Skip List is a probablistic data-structure  with same logarithmic time bound and
efficiency  as AVL/  or  Red-Black  tree and  provides  a  clever compromise  to
efficiently support  search and update  operations and is relatively  simpler to
implement compared to other map data structures.

A skip  list *S*  consists of  series of  sorted linked  lists *{L0,  ..., Ln}*,
layered hierarchicaly and each layer *L* stores  a subset of items in layer *L0*
in incremental order.  The items in layers  *{L1, ... Ln}* are  chosen at random
based on a coin flipping function  with probability 1/2 .  For traversing, every
item in  a layer  hold references  to the node  below and  the next  node.  This
layers serve as  express lanes to the layer underneath  them, effectively making
fast O(log n) searching possible by  skipping lanes and reducing travel distance
and in worse case  searching degrades to O (n), as  expected with regular linked
list.

For a skip list *S*:

1. List *L0* contains every inserted item.
2.  For lists *{L1, ..., Ln}*, *Li*  contains a randomly generated subset of the
   items in *Li-1*
3. Height is determined by coin-flipping.

-->

這是一個機率的資料結構, 擁有和 [AVL樹](../AVL Tree/) 或 [紅黑樹](../Red-Black Tree/) 一樣的對數時間複雜度, 搜尋和更新更有效率, 並且比其他資料結構相對簡單簡單.

一個跳躍列表 *S* 是由一些有序的鏈結列表 *{L0, ..., Ln}* 組成的, 階層結構而且在每層 *L* 中都會儲存第 0 層 *L0* 鏈結列表中元素的子集合. 在 *{L1, ... Ln}* 中的元素是以一個硬幣投擲的函式 (有 1/2 的機率) 從階層 0 中選取的. 就走訪而言, 階層中的元素都擁有到下一個和到下一層節點的參考指標. 每個階層都可以當作到下一層的快速通道, 有效的以跳躍不同階層來減少走訪的距離來優化 O(log n) 搜尋, 在最糟的情況下搜尋降到 O(n), 就像一般的鏈結列表一樣.

對於跳躍列表 *S*:

1. *L0* 含有所有的元素
2. 對於 *{L1, ..., Ln}*, *Li* 含有 *Li-1* 元素的隨機子集合
3. 高度是以硬幣投擲函式 (1/2 機率) 決定的

![Schematic view](Images/Intro.png)
Figure 1


<!--
#Searching

Searching for  element *N* starts by  traversing from top most  layer *Ln* until
*L0*.

Our objective  is to find an  element *K* such  that its value at  the rightmost
position of current layer, is less-than  target item and its subsequent node has
a greater-equal  value or nil (  *K.key < N.key  <= (K.next.key or nil)*  ). if
value of *K.next* is equal to *N*,  search is terminated and we return *K.next*,
otherwise drop underneath using *K.down* to the node below ( at layer Ln-1 ) and
repeat the process until *L0* where *K.down* is `nil` which indicates that level
is *L0* and item doesn't exists.
-->

## 搜尋

要搜尋一個元素 *N*, 會從最高的階層 *Ln* 到 *L0* 來搜尋.

我們的目的是找到某個元素 *K*, 其值是在當前階層小於目標元素中最右邊的值, 而它下一個節點會大於等於目標元素或者是 nil (*K.key < N.key <= (K.next.key or nil)*). 如果 *K.next* 的值和 *N* 的值一樣, 搜尋結束並回傳 *K.next*; 不是了話就跳到下個階層 *K.down* (在 Ln-1 層) 然後重複上述步驟直到達到 *L0* 或 *K.down* 是 `nil`, 到這步還沒找到了話表示已經到達 *L0* 並且該目標元素不存在.

<!--
###Example:
-->

### 範例:

![Inserting first element](Images/Search1.png)

<!--
#Inserting

Inserting  element  *N*  has  a  similar process  as  searching.  It  starts  by
traversing from  top most layer *Ln*  until *L0*. We  need to keep track  of our
traversal path  using a  stack. It  helps us  to traverse  the path  upward when
coin-flipping starts, so we can insert  our new element and update references to
it.

Our objective  is to find  a element  *K* such that  its value at  the rightmost
position of  layer *Ln*,  is less-than new  item and its  subsequent node  has a
greater-equal value  or nil (  *K.key  < N.key <  (K.next.key or nil)*  ). Push
element *K*  to the stack and  with element *K*,  go down using *K.down*  to the
node below  ( at layer Ln-1  ) and repeat the  process ( forward searching  ) up
until  *L0* where  *K.down* is  `nil`  which indicates  that level  is *L0*.  We
terminate the process when *K.down* is nil.

At *L0*, *N* can be inserted after *K*.

Here is the  interesting part. We use coin flipping  function to randomly create
layers.

When  coin flip  function returns  0,  the whole  process is  finished but  when
returns 1, there are two possibilities:

1. Stack is empty ( Level is *L0* /- *Ln* or at uninitialized stage)
2. Stack has items ( traversing upward is possible )

In case 1:

A new layer M*  is created with a head node *NM* referencing  head node of layer
below  and *NM.next*  referencing new  element *N*.  New element  *N* referecing
element *N* at previous layer.

In case 2:

repeat until stack is empty Pop an item *F* from stack and update the references
accordingly.  *F.next* will be *K.next* and *K.next* will be *F*
	
when  stack  is  empty Create  a  new  layer  consisintg  of a  head  node  *NM*
referencing  head node  of layer  below  and *NM.next*  referencing new  element
*N*. New element *N* referencing element *N* at previous layer.
-->

## 插入

插入元素 *N* 和搜尋的的流程有點像. 從最高層 *Ln* 開始走訪直到 *L0*. 我們需要使用堆疊來持續追蹤我們走訪的路徑. 這能讓我們在硬幣投擲時可以往上層走訪, 才可以插入新元素並更新其參考指標.

我們的目的是找到某個元素 *K*, 其值是在當前階層小於目標元素中最右邊的值, 而它下一個節點會大於目標元素或者是 nil (*K.key < N.key < (K.next.key or nil)*). 先將 *K* 放到堆疊中並走訪 *K.down* 到下一層 (階層 Ln-1), 重複這流程直到 *K.down* 是 `nil`, 表示已經走訪到 *L0* 了, 結束流程.

在 *L0*, 元素 *N* 可以插入到 *K* 之後.

接下來是有趣的地方. 我們使用投擲硬幣的函式隨機的創建階層.

當投擲硬幣函式回傳 0, 整個流程結束, 當回傳 1, 有兩種可能:

1. 堆疊是空的 (階層是 *L0* /- *Ln* 或在尚未初始化的階段)
2. 堆疊非空 (可能往上層走訪)

狀況 1:

創建一個新的階層 *M*, 此階層的起頭節點 *NM* 參考到下一層的起頭節點, 而 *NM.next* 指向新元素 *N*. 新元素 *N* 指向下一層的 *N*.

狀況 2:

從堆疊中持續取出元素 *F* 然後更新該節點的參考指標, 直到堆疊清空.  *F* 指向 *K.next*, 而 *K.next* 指向 *F.next*.

當堆疊清空後建立一個新的階層, 起頭節點 *NM* 會參考到下一層的起頭節點, 而 *NM.next* 會指向新元素 *N*, 新元素 *N* 也會參考到下一層的 *N*.

<!--
###Example:

Inserting 13. with coin flips (0)
-->

### 範例:

插入 13. 硬幣投擲結果為 0

![Inserting first element](Images/Insert5.png)
![Inserting first element](Images/Insert6.png)
![Inserting first element](Images/insert7.png)
![Inserting first element](Images/Insert8.png)
![Inserting first element](Images/Insert9.png)

<!--
Inserting 20. with 4 times coin flips (1) 
-->

插入 20, 硬筆投擲 4 次都回傳 1

![Inserting first element](Images/Insert9.png)
![Inserting first element](Images/Insert10.png)
![Inserting first element](Images/Insert11.png)
![Inserting first element](Images/Insert12.png)

<!--
#Removing

Removing works similar to insert procedure.
-->
## 刪除

刪除動作和插入類似.

TODO

<!--
#See also
-->

## 相關閱讀

[Skip List on Wikipedia](https://en.wikipedia.org/wiki/Skip_list) 

Written for Swift Algorithm Club by [Mike Taghavi](https://github.com/mitghi)
