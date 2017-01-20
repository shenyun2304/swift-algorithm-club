# Longest Common Subsequence (最長相同子序列)

<!--
The Longest Common Subsequence (LCS) of two strings is the longest sequence of characters that appear in the same order in both strings.

For example the LCS of `"Hello World"` and `"Bonjour le monde"` is `"oorld"`. If you go through both strings from left-to-right, you'll find that the characters `o`, `o`, `r`, `l`, `d` appear in both strings in that order.

Other possible subsequences are `"ed"` and `"old"`, but these are all shorter than `"oorld"`. 

> **Note:** This should not be confused with the Longest Common Substring problem, where the characters must form a substring of both strings, i.e they have to be immediate neighbors. With a subsequence, it's OK if the characters are not right next to each other, but they must be in the same order.

One way to find the LCS of two strings `a` and `b` is using dynamic programming and a backtracking strategy.
-->

兩個字串的最長相同子序列 (LCS) 是指兩個字串中, 相同且順序也相同的所有字元.

舉例來說 `"Hello World"` 和 `"Bonjour le monde"` 的 LCS 是 `"oorld"` 如果你從左到右走訪兩個字串, 你會發現字元 `o`, `o`, `r`, `l`, `d` 在兩個字串中都有, 而且先後順序相同.

其他可能的相同子序列有 `"ed"` 和 `"old"`, 但是這些都比 `"oorld"` 還短.

> **注意:** 這通常會和最長相同子字串搞混, 這個是要在兩個字串的所有子字串中, 最長的那一個. 在子序列中, 字元並不一定要緊鄰著其他字元, 但他們的先後順序要一樣.

一個找到字串 `a` 和字串 `b` 的 LCS 的方法是動態編程和回溯策略.

<!--
## Finding the length of the LCS with dynamic programming

First, we want to find the length of the longest common subsequence between strings `a` and `b`. We're not looking for the actual subsequence yet, only how long it is.

To determine the length of the LCS between all combinations of substrings of `a` and `b`, we can use a *dynamic programming* technique. Dynamic programming basically means that you compute all possibilities and store them inside a look-up table.

> **Note:** During the following explanation, `n` is the length of string `a`, and `m` is the length of string `b`.

To find the lengths of all possible subsequences, we use a helper function, `lcsLength(_:)`. This creates a matrix of size `(n+1)` by `(m+1)`, where `matrix[x][y]` is the length of the LCS between the substrings `a[0...x-1]` and `b[0...y-1]`.

Given strings `"ABCBX"` and `"ABDCAB"`, the output matrix of `lcsLength(_:)` is the following:
-->

## 用動態編程來找到 LCS 的長度

首先, 我們想找到字串 `a` 跟字串 `b` LCS 的長度. 還沒開始找真正的子序列, 只是要知道多長.

要在 `a` 跟 `b` 的所有子字串的組合中決定 LCS 的長度我們可以利用 *動態編程* 技巧. 這技巧基本上是要計算所有的可能性然後把它們存在一個內部的查照表.

> **注意:** 接下來的解釋, `n` 是字串 `a` 的長度, `m` 是字串 `b` 的長度.

要找到所有可能子序列的長度, 我們使用一個幫助函式, `lcsLength(_:)`. 這建立一個 `(n+1) x (m+1)` 的矩陣, 而 `matrix[x][y]` 表示在 `a[0...x-1]` 和 `b[0...y-1]` 之間 LCS 的長度.

來看看 `"ABCBX"` 和 `"ABDCAB"` 這兩個字串, 輸出矩陣像這樣:

```
|   | Ø | A | B | D | C | A | B |
| Ø | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| A | 0 | 1 | 1 | 1 | 1 | 1 | 1 |  
| B | 0 | 1 | 2 | 2 | 2 | 2 | 2 |
| C | 0 | 1 | 2 | 2 | 3 | 3 | 3 |
| B | 0 | 1 | 2 | 2 | 3 | 3 | 4 |
| X | 0 | 1 | 2 | 2 | 3 | 3 | 4 |
```

<!--
In this example, if we look at `matrix[3][4]` we find the value `3`. This means the length of the LCS between `a[0...2]` and `b[0...3]`, or between `"ABC"` and `"ABDC"`, is 3. That is correct, because these two substrings have the subsequence `ABC` in common. (Note: the first row and column of the matrix are always filled with zeros.)

Here is the source code for `lcsLength(_:)`; this lives in an extension on `String`:
-->

在這個例子中, 如果我們看 `matrix[3][4]` 會得到 `3`. 這表示在 `a[0...2]` 和 `b[0...3]` 之間, 或者說在 `"ABC"` 和 `"ABCD"` 之間的 LCS 長度是 3. 這是正確的, 因為這兩個字串有 `ABC` 這個共同的子序列. (注意: 第一列和第一行永遠都是 0.)

```swift
func lcsLength(_ other: String) -> [[Int]] {

  var matrix = [[Int]](repeating: [Int](repeating: 0, count: other.characters.count+1), count: self.characters.count+1)

  for (i, selfChar) in self.characters.enumerated() {
	for (j, otherChar) in other.characters.enumerated() {
	  if otherChar == selfChar {
		// Common char found, add 1 to highest lcs found so far.
		matrix[i+1][j+1] = matrix[i][j] + 1
	  } else {
		// Not a match, propagates highest lcs length found so far.
		matrix[i+1][j+1] = max(matrix[i][j+1], matrix[i+1][j])
	  }
	}
  }

  return matrix
}
```

<!--
First, this creates a new matrix -- really a 2-dimensional array -- and fills it with zeros. Then it loops through both strings, `self` and `other`, and compares their characters in order to fill in the matrix. If two characters match, we increment the length of the subsequence. However, if two characters are different, then we "propagate" the highest LCS length found so far.

Let's say the following is the current situation:
-->

首先, 這要建立新的矩陣 -- 一個 2 維陣列 -- 然後元素全部都是 0. 然後迭代兩個字串, `self` 和 `other`, 然後一一比較字元來填滿矩陣. 如果兩個字元相同, 我們就增加子序列的值. 無論如何, 如果兩個字元不同, 那我們就 "傳播" 到目前為止最長的 LCS 長度.

讓我們假設現在迭代進度是這樣:

```
|   | Ø | A | B | D | C | A | B |
| Ø | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| A | 0 | 1 | 1 | 1 | 1 | 1 | 1 |  
| B | 0 | 1 | 2 | 2 | 2 | 2 | 2 |
| C | 0 | 1 | 2 | * |   |   |   |
| B | 0 |   |   |   |   |   |   |
| X | 0 |   |   |   |   |   |   |
```

<!--
The `*` marks the two characters we're currently comparing, `C` versus `D`. These characters are not the same, so we propagate the highest length we've seen so far, which is `2`:
-->

`*` 標記我們正在比對的兩個字元, `C` 對上 `D`. 這兩個字元不同, 所以我們傳播目前最長的 LCS 值, 也就是 `2`:

```
|   | Ø | A | B | D | C | A | B |
| Ø | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| A | 0 | 1 | 1 | 1 | 1 | 1 | 1 |  
| B | 0 | 1 | 2 | 2 | 2 | 2 | 2 |
| C | 0 | 1 | 2 | 2 | * |   |   |
| B | 0 |   |   |   |   |   |   |
| X | 0 |   |   |   |   |   |   |
```

<!--
Now we compare `C` with `C`. These are equal, and we increment the length to `3`:
-->

接下來我們比對 `C` 和 `C`. 相同, 所以我們將長度增加到 `3`:

```
|   | Ø | A | B | D | C | A | B |
| Ø | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| A | 0 | 1 | 1 | 1 | 1 | 1 | 1 |  
| B | 0 | 1 | 2 | 2 | 2 | 2 | 2 |
| C | 0 | 1 | 2 | 2 | 3 | * |   |
| B | 0 |   |   |   |   |   |   |
| X | 0 |   |   |   |   |   |   |
```

<!--
And so on... this is how `lcsLength(_:)` fills in the entire matrix.
-->

一直持續值到 `lcsLength(_:)` 填滿整個矩陣.

<!--
## Backtracking to find the actual subsequence

So far we've calculated the length of every possible subsequence. The length of the longest subsequence is found in the bottom-left corner of matrix, at `matrix[n+1][m+1]`. In the above example it is 4, so the LCS consists of 4 characters.

Having the length of every combination of substrings makes it possible to determine *which* characters are part of the LCS itself by using a backtracking strategy.

Backtracking starts at `matrix[n+1][m+1]` and walks up and left (in this priority) looking for changes that do not indicate a simple propagation.
-->

## 回溯並找到真的子序列

到目前為止我們計算了每個可能的子序列. 而最長子序列的長度就在最右下角的角落, 就是在 `matrix[n+1][m+1]`. 在例子中這個值是 4, 所以 LCS 共有 4 個字元.

擁有該長度之下的每種子字串的組合讓決定 *哪些* 字元是 LCS 的一部分變得可能, 使用回溯策略.

回溯從 `matrix[n+1][m+1]` 開始, 先上後左, 尋找非簡單傳播的改變.


```
|   |  Ø|  A|  B|  D|  C|  A|  B|
| Ø |  0|  0|  0|  0|  0|  0|  0|
| A |  0|↖ 1|  1|  1|  1|  1|  1|  
| B |  0|  1|↖ 2|← 2|  2|  2|  2|
| C |  0|  1|  2|  2|↖ 3|← 3|  3|
| B |  0|  1|  2|  2|  3|  3|↖ 4|
| X |  0|  1|  2|  2|  3|  3|↑ 4|
```

<!--
Each `↖` move indicates a character (in row/column header) that is part of the LCS.

If the number on the left and above are different than the number in the current cell, no propagation happened. In that case `matrix[i][j]` indicates a common char between the strings `a` and `b`, so the characters at `a[i - 1]` and `b[j - 1]` are part of the LCS that we're looking for.

One thing to notice is, as it's running backwards, the LCS is built in reverse order. Before returning, the result is reversed to reflect the actual LCS.

Here is the backtracking code:
-->

每個 `↖` 表示有一個字元屬於 LCS.

如果左上的和當前的數字不同, 表示沒有傳播發生. 這時候表示 `matrix[i][j]` 是字串 `a` 和 `b` 的共同字元, 所以 `a[i - 1]` 和 `b[j - 1]` 就是我們要的共同字元之一.

值得注意的是, 既然是反過來走訪, 這個 LCS 是反向的. 所以在回傳之前, 要轉回來成真的 LSC 再回傳.

```swift
func backtrack(_ matrix: [[Int]]) -> String {
  var i = self.characters.count
  var j = other.characters.count
  
  var charInSequence = self.endIndex
  
  var lcs = String()
  
  while i >= 1 && j >= 1 {
	// Indicates propagation without change: no new char was added to lcs.
	if matrix[i][j] == matrix[i][j - 1] {
	  j -= 1
	}
	// Indicates propagation without change: no new char was added to lcs.
	else if matrix[i][j] == matrix[i - 1][j] {
	  i -= 1
	  charInSequence = self.index(before: charInSequence)
	}
	// Value on the left and above are different than current cell.
	// This means 1 was added to lcs length.
	else {
	  i -= 1
	  j -= 1
	  charInSequence = self.index(before: charInSequence)
	  lcs.append(self[charInSequence])
	}
  }
  
  return String(lcs.characters.reversed())
}
```  

<!--
This backtracks from `matrix[n+1][m+1]` (bottom-right corner) to `matrix[1][1]` (top-right corner), looking for characters that are common to both strings. It adds those characters to a new string, `lcs`.

The `charInSequence` variable is an index into the string given by `self`. Initially this points to the last character of the string. Each time we decrement `i`, we also move back `charInSequence`. When the two characters are found to be equal, we add the character at `self[charInSequence]` to the new `lcs` string. (We can't just write `self[i]` because `i` may not map to the current position inside the Swift string.)

Due to backtracking, characters are added in reverse order, so at the end of the function we call `reversed()` to put the string in the right order. (Appending new characters to the end of the string and then reversing it once is faster than always inserting the characters at the front of the string.)
-->

從 `matrix[n+1][m+1]` (右下角) 到 `matrix[1][1]` (左上角), 把找到的相同字元加入到新的字串 `lcs` 中.

`cahrInSequence` 變數是當前字船的字元索引. 一開始它指向最後一個字元. 每次 `i` 遞減, 我們將 `charInSequence` 向前一個移動字元. 當兩個字元比對後市相同的, 我們將該字元用 `self[charInSequence]` 抓出來放到新字串 `lcs` 中.
(我們不能用 `self[i]`, 因為 `i` 不一定對應當前字串的索引位置.)

因為回溯的關係, 字元是反向加入到 `lcs` 之中的, 所以在最後呼叫了 `reversed()` 將字元變為正確的順序. (在字串尾端插入字元然後一次性的反轉它會比總是在字串前端插入字元還有效率)


<!--
## Putting it all together

To find the LCS between two strings, we first call `lcsLength(_:)` and then `backtrack(_:)`:
-->

## 通通加起來

要找到兩個字串的 LCS, 我們先呼叫 `lcsLength(_:)` 然後 `backtrack(_:)`:

```swift
extension String {
  public func longestCommonSubsequence(_ other: String) -> String {

    func lcsLength(_ other: String) -> [[Int]] {
      ...
    }
    
    func backtrack(_ matrix: [[Int]]) -> String {
      ...
    }

    return backtrack(lcsLength(other))
  }
}
```

<!--
To keep everything tidy, the two helper functions are nested inside the main `longestCommonSubsequence()` function.

Here's how you could try it out in a Playground:
-->

讓事情簡單一點, 這兩個函式包在 `longestCommonSubsequence()` 函式中.

這就是你可以在 playground 中測試的:

```swift
let a = "ABCBX"
let b = "ABDCAB"
a.longestCommonSubsequence(b)   // "ABCB"

let c = "KLMK"
a.longestCommonSubsequence(c)   // "" (no common subsequence)

"Hello World".longestCommonSubsequence("Bonjour le monde")   // "oorld"
```

*Written for Swift Algorithm Club by Pedro Vereza*
