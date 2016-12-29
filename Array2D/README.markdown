# Array2D (二維陣列)
<!--
In C and Objective-C you can write the following line,
-->

在 C 和 Objective-C 中, 你可以撰寫以下這段程式碼,

	int cookies[9][7];

<!--	
to make a 9x7 grid of cookies. This would create a two-dimensional array of 63 elements. To find the cookie at column 3, row 6, you'd write:
-->

要創建一個 9x7 的餅乾格. 需要創建一個有63元素的二維陣列. 想要選取第3欄, 第6行的餅乾, 你可以這樣寫:

	myCookie = cookies[3][6];
	
<!--
Unfortunately, you can't write the above in Swift. To create a multi-dimensional array in Swift you'd have to do something like this:
-->

很可惜的, 在 Swift 中無法這樣撰寫, 在 Swift 中要創建一個多維度的陣列, 你需要向以下這樣撰寫:

```swift
var cookies = [[Int]]()
for _ in 1...9 {
  var row = [Int]()
  for _ in 1...7 {
    row.append(0)
  }
  cookies.append(row)
}
```
<!--
And then to find a cookie:
-->

而可以這樣找到餅乾:

```swift
let myCookie = cookies[3][6]
```

<!--
Actually, you could create the array in a single line of code, like so:
-->

事實上, 你可以在一行程式碼中創建陣列, 像這樣:

```swift
var cookies = [[Int]](repeating: [Int](repeating: 0, count: 7), count: 9)
```

<!--
but that's just ugly. To be fair, you can hide the ugliness in a helper function:
-->

但是那樣很醜, 你還是可以有辦法隱藏在函式提示中醜陋的地方:

```swift
func dim<T>(count: Int, _ value: T) -> [T] {
  return [T](repeating: value, count: count)
}
```

<!--
And then creating the array looks like this:
-->

然後想這樣的創建陣列:

```swift
var cookies = dim(9, dim(7, 0))
```

<!--
Swift infers that the datatype of the array should be `Int` because you specified `0` as the default value of the array elements. To use a string instead, you'd write:
-->

當你宣告元素預設值是 `0` 的時候, Swift 會推斷在陣列中的資料型態是 `Int`, 如果你要的是字串型態, 你可以這樣寫:

```swift
var cookies = dim(9, dim(7, "yum"))
```

<!--
The `dim()` function makes it easy to go into even more dimensions:
-->

`dim()` 函式讓創建多維陣列變得更簡單:

```swift
var threeDimensions = dim(2, dim(3, dim(4, 0)))
```

<!--
The downside of using multi-dimensional arrays in this fashion -- actually, multiple nested arrays -- is that it's easy to lose track of what dimension represents what.

So instead let's create our own type that acts like a 2-D array and that is more convenient to use. Here it is, short and sweet:
-->

這樣創建多維陣列 -- 實際上是多個插入陣列 -- 的缺點是滿容易忘記哪層陣列是代表什麼.

所以讓我們來創建自己的2維陣列型態吧, 就像這樣:


```swift
public struct Array2D<T> {
  public let columns: Int
  public let rows: Int
  private var array: [T]

  public init(columns: Int, rows: Int, initialValue: T) {
    self.columns = columns
    self.rows = rows
    array = .init(repeating: initialValue, count: rows*columns)
  }

  public subscript(column: Int, row: Int) -> T {
    get {
      return array[row*columns + column]
    }
    set {
      array[row*columns + column] = newValue
    }
  }
}
```

<!--
`Array2D` is a generic type, so it can hold any kind of object, not just numbers.

To create an instance of `Array2D` you'd write:
-->

`Array2D` 宣告為通用型態, 所以他可以容納各種型態的物件, 不只是數字.

你可以像這樣的創建一個 `Array2D` 的物件:

```swift
var cookies = Array2D(columns: 9, rows: 7, initialValue: 0)
```

<!--
Thanks to the `subscript` function, you can do the following to retrieve an object from the array:
-->

感謝 `subscript` 函式, 你可以像下面這樣使用來獲得物件:

```swift
let myCookie = cookies[column, row]
```

<!--
Or change it:
-->

或變更它:

```swift
cookies[column, row] = newCookie
```

<!--
Internally, `Array2D` uses a single one-dimensional array to store the data. The index of an object in that array is given by `(row x numberOfColumns) + column`. But as a user of `Array2D` you don't have to worry about that; you only have to think in terms of "column" and "row", and let `Array2D` figure out the details for you. That's the advantage of wrapping primitive types into a wrapper class or struct.

And that's all there is to it.
-->

在內部來看, `Array2D` 使用了一個一維陣列來儲存資料. 內含的元素索引是以 `(列數 * 欄數) + 欄數` 來計算. 但是在使用 `Array2D` 上完全不需要考慮這些; 你只需要以 "欄" 和 "列" 來操作, 讓 `Array2D` 去處理細節. 這是將原始類型包裝到 class 或 struct 中的優點.


*Written for Swift Algorithm Club by Matthijs Hollemans*
