# Bucket Sort (桶排序)

<!--
Bucket Sort, also known as Bin Sort, is a distributed sorting algorithm, which sort elements from an array by performing these steps:  

1) Distribute the elements into buckets or bins.  
2) Sort each bucket individually.  
3) Merge the buckets in order to produce a sorted array as the result.

See the algorithm in action [here](https://www.cs.usfca.edu/~galles/visualization/BucketSort.html) and [here](http://www.algostructure.com/sorting/bucketsort.php).

The performance for execution time is:  
-->

這是一個分散式的排序演算法, 依照以下步驟:

1) 將元素分佈到桶子裡.
2) 將每個桶子內部排序.
3) 將桶子合併獲得最後的陣列.

在 [這裡](https://www.cs.usfca.edu/~galles/visualization/BucketSort.html) 和 [這裡](http://www.algostructure.com/sorting/bucketsort.php) 可以看到桶排序的動作


| Case  | Performance |
|:-------------: |:---------------:|
| Worst       |  O(n^2) |
| Best      | 	Omega(n + k)        |
|  Average | 	Theta(n + k)       |

<!--
Where **n** = the number of elements and **k** is the number of buckets.

In the *best case*, the algorithm distributes the elements uniformly between buckets, a few elements are placed on each bucket and sorting the buckets is **O(1)**. Rearranging the elements is one more run through the initial list.  

In the *worst case*, the elements are sent all to the same bucket, making the process take **O(n^2)**.
-->

**n** = 陣列中元素個數

**k** = 桶子數

在 *最好* 的情況下, 這演算法會把元素平均的分佈在桶子裡, 每個桶子內只含有少量元素, 而桶子內部的排序是 **O(1)**. 整個重新組合就再多一步驟就好了.

在 *最糟* 的情況下, 元素都被放到同一個桶子內, 那複雜度就會變成 **O(n^2)**.

<!--
## Pseudocode

A [pseudocode](https://en.wikipedia.org/wiki/Bucket_sort#Pseudocode) of the algorithm can be as follows:  
-->

## 偽程式碼:

[桶排序的偽程式碼](https://en.wikipedia.org/wiki/Bucket_sort#Pseudocode) 像這樣:

    function bucketSort(array, n) is
        buckets ← new array of n empty lists
        for i = 0 to (length(array)-1) do
            insert array[i] into buckets[msbits(array[i], k)]
        for i = 0 to n - 1 do
            nextSort(buckets[i]);
        return the concatenation of buckets[0], ...., buckets[n-1]


<!--
## Graphically explained

1) Distribute elements in buckets:
-->

## 圖片解釋

1) 將元素分佈到桶子中:

![distribution step](https://upload.wikimedia.org/wikipedia/commons/6/61/Bucket_sort_1.png)

<!--
2) Sorting inside every bucket and merging:
-->

2) 桶內排序和合併:

![sorting each bucket and merge](https://upload.wikimedia.org/wikipedia/commons/3/39/Bucket_sort_2.png)

<!--
## An example

### Input  

Suppose we have the following list of elements: `[2, 56, 4, 77, 26, 98, 55]`. Let's use 10 buckets. To determine the capacity of each bucket we need to know the *maximum element value*, in this case `98`.  

So the buckets are: 

* `bucket 1`: from 0 to 9
* `bucket 2`: from 10 to 19
* `bucket 3`: from 20 to 29
*  and so on.   
-->

## 範例

### 輸入

假設我們有以下的元素: `[2, 56, 4, 77, 26, 98, 55]`. 讓我們使用 10 個桶子. 要決定每個桶子的內容我們需要知道 *最大的元素*, 在這裡就是 `98`.

所以桶子內容是:

* `桶 1`: 從 0 到 9
* `桶 2`: 從 10 到 19
* `桶 3`: 從 20 到 29
*  以此類推.

<!--
### Distribution

Now we need to choose a distribution function.  

`bucketNumber = (elementValue / totalNumberOfBuckets) + 1`  

Such that by applying that function we distribute all the elements in the buckets.  

In our example it is like the following:  

1. Apply the distribution function to `2`. `bucketNumber = (2 / 10) + 1 = 1`
2. Apply the distribution function to `56`. `bucketNumber = (56 / 10) + 1 = 6`
3. Apply the distribution function to `4`. `bucketNumber = (4 / 10) + 1 = 1`
4. Apply the distribution function to `77`. `bucketNumber = (77 / 10) + 1 = 8`
5. Apply the distribution function to `26`. `bucketNumber = (26 / 10) + 1 = 3`
6. Apply the distribution function to `98`. `bucketNumber = (98 / 10) + 1 = 10`
7. Apply the distribution function to `55`. `bucketNumber = (55 / 10) + 1 = 6`

Our buckets will be filled now:  
-->

### 分佈

現在我們要選擇一個分佈函數.

`桶子號碼 = (元素值 / 桶子總數) + 1`

我們可以用這個函數將元素分佈到各個桶子中.

在例子中的元素會像這樣分佈:

1. `2`: `桶子號碼 = (2 / 10) + 1 = 1`
2. `56`: `桶子號碼 = (56 / 10) + 1 = 6`
3. `4`: `桶子號碼 = (4 / 10) + 1 = 1`
4. `77`: `桶子號碼 = (77 / 10) + 1 = 8`
5. `26`: `桶子號碼 = (26 / 10) + 1 = 3`
6. `98`: `桶子號碼 = (98 / 10) + 1 = 10`
7. `55`: `桶子號碼 = (55 / 10) + 1 = 6`

現在桶子內容會像這樣:

**1**  <img src="https://pixabay.com/static/uploads/photo/2014/03/24/17/21/pail-295491_960_720.png" width="40">: `[2, 4]`  
**2**  <img src="https://pixabay.com/static/uploads/photo/2014/03/24/17/21/pail-295491_960_720.png" width="40">: `[]`  
**3**  <img src="https://pixabay.com/static/uploads/photo/2014/03/24/17/21/pail-295491_960_720.png" width="40">: `[26]`  
**4**  <img src="https://pixabay.com/static/uploads/photo/2014/03/24/17/21/pail-295491_960_720.png" width="40">: `[]`  
**5**  <img src="https://pixabay.com/static/uploads/photo/2014/03/24/17/21/pail-295491_960_720.png" width="40">: `[]`  
**6**  <img src="https://pixabay.com/static/uploads/photo/2014/03/24/17/21/pail-295491_960_720.png" width="40">: `[55, 56]`  
**7**  <img src="https://pixabay.com/static/uploads/photo/2014/03/24/17/21/pail-295491_960_720.png" width="40">: `[]`  
**8**  <img src="https://pixabay.com/static/uploads/photo/2014/03/24/17/21/pail-295491_960_720.png" width="40">: `[77]`  
**9**  <img src="https://pixabay.com/static/uploads/photo/2014/03/24/17/21/pail-295491_960_720.png" width="40">: `[]`  
**10** <img src="https://pixabay.com/static/uploads/photo/2014/03/24/17/21/pail-295491_960_720.png" width="40">: `[98]`  

<!--
We can choose to insert the elements in every bucket in order, or sort every bucket after distributing all the elements.  
-->

我們可以選擇在分佈元素時就在各個桶子內排序, 或者也可以全部分佈完以後再對每個桶子內部排序.

<!--
### Put the elements back in the list

Finally we go through all the buckets and put the elements back in the list:  
-->

### 將元素放回

最後我們走訪每個桶子, 然後把元素放回去:

  `[2,  4,  26,  55,  56,  77,  98]`  


<!--
## Swift implementation

Here is a diagram that shows the functions, data structures and protocols for our bucker sort implementation:
-->

## Swift 實作

這張圖展示桶排序的實作內容:

![classes](Docs/BucketSort.png)

<!--
#### Main function

`bucketSort()` is a generic function that can apply the algorithm to any element of type `T`, as long as `T` is `Sortable`.
-->

#### 主程式

`bucketSort()` 是一個泛型函數適用 `T` 型別的物件, `T` 要是 `Sortable`.


```swift
public func bucketSort<T:Sortable>(elements: [T],
                                distributor: Distributor,
                                     sorter: Sorter,
                                    buckets: [Bucket<T>]) -> [T] {
	precondition(allPositiveNumbers(elements))
	precondition(enoughSpaceInBuckets(buckets, elements: elements))

	var bucketsCopy = buckets
	for elem in elements {
		distributor.distribute(elem, buckets: &bucketsCopy)
	}

	var results = [T]()

	for bucket in bucketsCopy {
		results += bucket.sort(sorter)
	}

	return results
}
```

#### Bucket

```swift
public struct Bucket<T:Sortable> {
	var elements: [T]
	let capacity: Int

	public init(capacity: Int) {
		self.capacity = capacity
		elements = [T]()
	}

	public mutating func add(item: T) {
		if (elements.count < capacity) {
			elements.append(item)
		}
	}

	public func sort(algorithm: Sorter) -> [T] {
		return algorithm.sort(elements)
	}
}
```

#### Sortable

```swift
public protocol Sortable: IntConvertible, Comparable {
}
```

#### IntConvertible

The algorithm is designed to sort integers, so all the elements to be sorted should be mapped to an integer value.

```swift
public protocol IntConvertible {
	func toInt() -> Int
}
```

#### Sorter

```swift
public protocol Sorter {
	func sort<T:Sortable>(items: [T]) -> [T]
}
```

#### Distributor

```swift
public protocol Distributor {
	func distribute<T:Sortable>(element: T, inout buckets: [Bucket<T>])
}
```

<!--
### Custom Sorter and Distributor

The current implementation make use of the following implementations for *Sorter* and *Distributor*.  
-->

### 客製化 Sorter 和 Distributor

目前的實作是根據以下的 *Sorter* 和 *Distributor*.


*Sorter*  

```swift
public struct InsertionSorter: Sorter {
	public func sort<T:Sortable>(items: [T]) -> [T] {
		var results = items
		for i in 0 ..< results.count {
			var j = i
			while ( j > 0 && results[j-1] > results[j]) {

				let auxiliar = results[j-1]
				results[j-1] = results[j]
				results[j] = auxiliar

				j -= 1
			}
		}
		return results
	}
}
```

*Distributor*  

```swift
/*
 * An example of a simple distribution function that send every elements to
 * the bucket representing the range in which it fits.
 *
 * If the range of values to sort is 0..<49 i.e, there could be 5 buckets of capacity = 10
 * So every element will be classified by the ranges:
 *
 * -  0 ..< 10
 * - 10 ..< 20
 * - 20 ..< 30
 * - 30 ..< 40
 * - 40 ..< 50
 *
 * By following the formula: element / capacity = #ofBucket
 */
public struct RangeDistributor: Distributor {
	 public func distribute<T:Sortable>(element: T, inout buckets: [Bucket<T>]) {
	 let value = element.toInt()
	 let bucketCapacity = buckets.first!.capacity

	 let bucketIndex = value / bucketCapacity
	 buckets[bucketIndex].add(element)
	}
}
```

<!--
### Make your own version

By reusing this code and implementing your own *Sorter* and *Distributor* you can experiment with different versions.

## Other variations of Bucket Sort

The following are some of the variation to the general [Bucket Sort](https://en.wikipedia.org/wiki/Bucket_sort) implemented here:
-->

### 建立你的排序

把 *Sorter* 和 *Distributor* 修改成你需要的排序和分佈演算法, 你就可以擁有你自己版本的桶排序.

## 桶排序其他變化

以下是一些 [桶排序](https://en.wikipedia.org/wiki/Bucket_sort) 的變化:

- [Proxmap Sort](https://en.wikipedia.org/wiki/Bucket_sort#ProxmapSort)
- [Histogram Sort](https://en.wikipedia.org/wiki/Bucket_sort#Histogram_sort)
- [Postman Sort](https://en.wikipedia.org/wiki/Bucket_sort#Postman.27s_sort)
- [Shuffle Sort](https://en.wikipedia.org/wiki/Bucket_sort#Shuffle_sort)

*Written for Swift Algorithm Club by Barbara Rodeker. Images from Wikipedia.*
