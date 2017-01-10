# Fizz Buzz

<!--
Fizz buzz is a group word game for children to teach them about division. Players take turns to count incrementally, replacing any number divisible by three with the word "fizz", and any number divisible by five with the word "buzz".

Fizz buzz has been used as an interview screening device for computer programmers.
-->

Fizz buzz 是個教導孩子除法的遊戲. 玩家輪流遞增的數數字, 把可以被 3 整除的數字改成 "fizz", 然後可以被 5 整除的數字改成 "buzz".

Fizz buzz 曾經被用來作為電腦程式員的面試題目.

<!--
## Example

A typical round of fizz buzz:
-->

## 範例

一個典型的 fizz buzz:

`1`, `2`, `Fizz`, `4`, `Buzz`, `Fizz`, `7`, `8`, `Fizz`, `Buzz`, `11`, `Fizz`, `13`, `14`, `Fizz Buzz`, `16`, `17`, `Fizz`, `19`, `Buzz`, `Fizz`, `22`, `23`, `Fizz`, `Buzz`, `26`, `Fizz`, `28`, `29`, `Fizz Buzz`, `31`, `32`, `Fizz`, `34`, `Buzz`, `Fizz`, ...

<!--
## 	Modulus Operator

The modulus operator `%` is the key to solving fizz buzz.

The modulus operator returns the remainder after an integer division. Here is an example of the modulus operator:
-->

## 取餘運算子

`%` 運算子是解 fizz buzz 的關鍵.

這個運算子會回傳除法運算後的餘數. 以下是取餘運算子的例子:

| Division      | Division Result            | Modulus         | Modulus Result  |
| ------------- | -------------------------- | --------------- | ---------------:|
| 1 / 3       | 0 with a remainder of 3  | 1 % 3         | 1             |
| 5 / 3       | 1 with a remainder of 2  | 5 % 3         | 2             |
| 16 / 3      | 5 with a remainder of 1  | 16 % 3        | 1             |

<!--
A common approach to determine if a number is even or odd is to use the modulus operator:
-->

使用取餘運算子來判斷一個數是奇數還是偶數:

| Modulus       | Result          | Swift Code                      | Swift Code Result | Comment                                       |
| ------------- | ---------------:| ------------------------------- | -----------------:| --------------------------------------------- |
| 6 % 2       | 0               | `let isEven = (number % 2 == 0)`  | `true`            | If a number is divisible by 2 it is *even*    |
| 5 % 2       | 1               | `let isOdd = (number % 2 != 0)`   | `true`            | If a number is not divisible by 2 it is *odd* |


<!--
## Solving fizz buzz

Now we can use the modulus operator `%` to solve fizz buzz.

Finding numbers divisible by three:
-->

## 解 fizz buzz

現在我們可以使用取餘運算子 `%` 來解決 fizz buzz.

判斷可以被 3 整除:

| Modulus | Modulus Result | Swift Code    | Swift Code Result |
| ------- | --------------:| ------------- |------------------:|
| 1 % 3 | 1            | `1 % 3 == 0`  | `false`           |
| 2 % 3 | 2            | `2 % 3 == 0`  | `false`           |
| 3 % 3 | 0            | `3 % 3 == 0`  | `true`            |
| 4 % 3 | 1            | `4 % 3 == 0`  | `false`           |

<!--
Finding numbers divisible by five:
-->

判斷可以被 5 整除:

| Modulus | Modulus Result | Swift Code    | Swift Code Result |
| ------- | --------------:| ------------- |------------------:|
| 1 % 5 | 1            | `1 % 5 == 0`  | `false`           |
| 2 % 5 | 2            | `2 % 5 == 0`  | `false`           |
| 3 % 5 | 3            | `3 % 5 == 0`  | `false`           |
| 4 % 5 | 4            | `4 % 5 == 0`  | `false`           |
| 5 % 5 | 0            | `5 % 5 == 0`  | `true`            |
| 6 % 5 | 1            | `6 % 5 == 0`  | `false`           |

<!--
## The code

Here is a simple implementation in Swift:
-->

## 程式碼:

簡單展示 Swift 中的實作:

```swift
func fizzBuzz(_ numberOfTurns: Int) {
  for i in 1...numberOfTurns {
    var result = ""

    if i % 3 == 0 {
      result += "Fizz"
    }

    if i % 5 == 0 {
      result += (result.isEmpty ? "" : " ") + "Buzz"
    }

    if result.isEmpty {
      result += "\(i)"
    }

    print(result)
  }
}
```

<!--
Put this code in a playground and test it like so:
-->

在 playground 中測試一下:

```swift
fizzBuzz(15)
```

<!--
This will output:
-->

這會印出:

	1, 2, Fizz, 4, Buzz, Fizz, 7, 8, Fizz, Buzz, 11, Fizz, 13, 14, Fizz Buzz

<!--
## See also
-->

## 相關閱讀

[Fizz buzz on Wikipedia](https://en.wikipedia.org/wiki/Fizz_buzz)

*Written by [Chris Pilcher](https://github.com/chris-pilcher)*
