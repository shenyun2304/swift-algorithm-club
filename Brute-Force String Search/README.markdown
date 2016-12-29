# Brute-Force String Search (Brute-Force 字串搜尋)

<!--
How would you go about writing a string search algorithm in pure Swift if you were not allowed to import Foundation and could not use `NSString`'s `rangeOfString()` method?

The goal is to implement an `indexOf(pattern: String)` extension on `String` that returns the `String.Index` of the first occurrence of the search pattern, or `nil` if the pattern could not be found inside the string.
 
For example:
-->

如果要你使用純 Swift 來製作一個字串搜尋的演算法, 不能使用 `NSString` 的 `rangeOfString()` 方法, 你會怎麼做?

目標是實作一個 `String` 的 extension `indexOf(pattern: String)`, 會回傳要搜尋字串在原始字串中的第一個索引 `String.Index` 或 `nil` (當原始字串內不含要搜尋的字串).

舉個例子:

```swift
// Input: 
let s = "Hello, World"
s.indexOf("World")

// Output:
<String.Index?> 7

// Input:
let animals = "🐶🐔🐷🐮🐱"
animals.indexOf("🐮")

// Output:
<String.Index?> 6
```

<!--
> **Note:** The index of the cow is 6, not 3 as you might expect, because the string uses more storage per character for emoji. The actual value of the `String.Index` is not so important, just that it points at the right character in the string.

Here is a brute-force solution:
-->

> **注意:** 🐮 的索引是 6 不是 3, 因為 emoji 字串使用了比一般字串更多的字元來儲存. 不過 `String.Index` 的實際值並不是很重要, 只要他能夠正確的指向字串的索引位置就好.

這裡是 brute-force 解法:

```swift
extension String {
  func indexOf(_ pattern: String) -> String.Index? {
    for i in self.characters.indices {
        var j = i
        var found = true
        for p in pattern.characters.indices{
            if j == self.characters.endIndex || self[j] != pattern[p] {
                found = false
                break
            } else {
                j = self.characters.index(after: j)
            }
        }
        if found {
            return i
        }
    }
    return nil
  }
}
```

<!--
This looks at each character in the source string in turn. If the character equals the first character of the search pattern, then the inner loop checks whether the rest of the pattern matches. If no match is found, the outer loop continues where it left off. This repeats until a complete match is found or the end of the source string is reached.

The brute-force approach works OK, but it's not very efficient (or pretty). It should work fine on small strings, though. For a smarter algorithm that works better with large chunks of text, check out [Boyer-Moore](../Boyer-Moore/) string search.
-->


這方法走訪了原始字串的每一個字元. 如果走訪的字元和要搜尋字串的第一個字元相同, 就進入內部迴圈一一的比較剩下來的字元. 如果有一個不正確, 則跳出內部迴圈, 外部迴圈繼續往下一個字元走訪. 步驟一直持續到找到完全吻合或已經走訪到原始字串的結尾字元.

brute-force 表現還可以, 不過在效率上非常糟糕. 但是在短字串上應該還好. 有更聰明的演算法來處理更大的字串, 看看[Boyer-Moore 字串搜尋](../Boyer-Moore) 演算法




*Written for Swift Algorithm Club by Matthijs Hollemans*
