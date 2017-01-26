# Palindromes (回文)

<!--
A palindrome is a word or phrase that is spelled the exact same when reading it forwards or backward. Palindromes are allowed to be lowercase or uppercase, contain spaces, punctuation, and word dividers.

Algorithms that check for palindromes are a common programming interview question. 
-->

回文是一種正向反向字母組合順序都一樣的句子.
可以允許大寫或小寫, 空白, 標點符號, 分隔字元.

回文的演算法在面試中是很常見的問題.


<!--
## Example 

The word racecar is a valid palindrome, as it is a word spelled the same when backgrounds and forwards. The examples below shows valid cases of the palindrome `racecar`.
-->

## 範例

單字 racecar 是個合格的回文, 因為它正向和反向的字母組合順序是一樣的.
以下展示合格的回文 `racecar`.


```
raceCar
r a c e c a r
r?a?c?e?c?a?r?
RACEcar
```

<!--
## Algorithm 

To check for palindromes, a string's characters are compared starting from the beginning and end then moving inward toward the middle of the string while maintaining the same distance apart. In this implementation of a palindrome algorithm, recursion is used to check each of the characters on the left-hand side and right-hand side moving inward.
-->

## 演算法

要檢查回文, 從字串的第一個字元和最後一個字元開始比對, 然後一個一個字元的往中間比對. 實作中, 遞迴用來向內比對左右字元的.

<!--
## The code 

Here is a recursive implementation of this in Swift: 
-->

## 程式碼

在 Swift 中使用遞迴實作:

```swift
func isPalindrome(_ str: String) -> Bool {
  let strippedString = str.replacingOccurrences(of: "\\W", with: "", options: .regularExpression, range: nil)
  let length = strippedString.characters.count

  if length > 1 {
    return palindrome(strippedString.lowercased(), left: 0, right: length - 1)
  }

  return false
}

private func palindrome(_ str: String, left: Int, right: Int) -> Bool {
  if left >= right {
    return true
  }

  let lhs = str[str.index(str.startIndex, offsetBy: left)]
  let rhs = str[str.index(str.startIndex, offsetBy: right)]

  if lhs != rhs {
    return false
  }

  return palindrome(str, left: left + 1, right: right - 1)
}
```

<!--
This algorithm has a two-step process.

1. The first step is to pass the string to validate as a palindrome into the `isPalindrome` method. This method first removes occurrences of non-word pattern matches `\W` [Regex reference](http://regexr.com). It is written with two \\ to escape the \ in the String literal. 
-->

演算法有兩個步驟.

1.第一步是把整個字串當參數傳到 `isPalindrome` 方法中. 這個方法先移除了非字母 `\W` [正規表示式](http://regexr.com) 字符. 在字串中用兩個 \\ 來跳脫字串字面值 \.

```swift
let strippedString = str.replacingOccurrences(of: "\\W", with: "", options: .regularExpression, range: nil)
```

<!--
The length of the string is then checked to make sure that the string after being stripped of non-word characters is still in a valid length. It is then passed into the next step after being lowercased.

2. The second step is to pass the string in a recursive method. This method takes a string, a left index, and a right index. The method checks the characters of the string using the indexes to compare each character on both sides. The method checks if the left is greater or equal to the right if so the entire string has been run through without returning false so the string is equal on both sides thus returning true. 
-->

接下來檢查字串長度來確認在移除非字母字符後還是合格的字串. 再把字串轉成小寫後把字串當參數傳到下一步驟.

2.第二步是把字串放到遞迴方法中. 這個方法同時接收一個左索引和一個右索引. 這方法會檢查兩個索引指向的字元是否相同. 每次進入就會先檢查左索引是否大於又索引, 如果左大於右, 表示整個字串走訪過程中沒有回傳錯誤, 所以字串的左端開始和右端開始的字母順序是相同的, 回傳 true.

```swift
if left >= right {
  return true
}

```
<!--
If the check doesn't pass it continues to get the characters at the specified indexes and compare each. If they are not the same the method returns false and exits. 
-->

如果左小於右, 那就繼續比對字元的步驟, 如果比對字元不相同則回傳 false 並離開方法.


```swift
let lhs = str[str.index(str.startIndex, offsetBy: left)]
let rhs = str[str.index(str.startIndex, offsetBy: right)]

if lhs != rhs {
  return false
}
```

<!--
If they are the same the method calls itself again and updates the indexes accordingly to continue to check the rest of the string. 
-->

如果字元相同則更新左右索引然後再次呼叫自己.

```swift
return palindrome(str, left: left + 1, right: right - 1)
```

Step 1:
`race?C ar -> raceCar -> racecar`

Step 2:

```
|     |
racecar -> r == r

 |   |
racecar -> a == a

  | |
racecar -> c == c

   |
racecar -> left index == right index -> return true
```

<!--
## Additional Resources
-->

## 進階資源

[Palindrome Wikipedia](https://en.wikipedia.org/wiki/Palindrome)


*Written by [Joshua Alvarado](https://github.com/https://github.com/lostatseajoshua)*
