# Boyer-Moore String Search (Boyer-Moore 字串搜尋)

<!--

Goal: Write a string search algorithm in pure Swift without importing Foundation or using `NSString`'s `rangeOfString()` method.
In other words, we want to implement an `indexOf(pattern: String)` extension on `String` that returns the `String.Index` of the first occurrence of the search pattern, or `nil` if the pattern could not be found inside the string.

For example:
-->


目標: 使用純 Swift 撰寫一個字串搜尋的演算法, 不使用 Foundation 或使用 `NSString` 的 `rangeOfString()` 方法.

換句話說, 我們要在 `String` class 上實作一個 `indexOf(pattern: String)` 的 extension, 回傳符合 pattern 的第一個字元的 `String.Index`, 如果 pattern 沒有吻合則回傳 `nil`.

```swift
// Input:
let s = "Hello, World"
s.indexOf(pattern: "World")

// Output:
<String.Index?> 7

// Input:
let animals = "🐶🐔🐷🐮🐱"
animals.indexOf(pattern: "🐮")

// Output:
<String.Index?> 6
```
<!--
> **Note:** The index of the cow is 6, not 3 as you might expect, because the string uses more storage per character for emoji. The actual value of the `String.Index` is not so important, just that it points at the right character in the string.

The [brute-force approach](../Brute-Force String Search/) works OK, but it's not very efficient, especially on large chunks of text. As it turns out, you don't need to look at _every_ character from the source string -- you can often skip ahead multiple characters.

The skip-ahead algorithm is called [Boyer-Moore](https://en.wikipedia.org/wiki/Boyer–Moore_string_search_algorithm) and it has been around for a long time. It is considered the benchmark for all string search algorithms.

Here's how you could write it in Swift:
-->

> **注意:** "🐮" 的索引值是 6 而不是 3 是因為 emoji 字串使用了更多的儲存空間. `String.Index` 的值不是那麼重要, 重要的是它有沒有正確的指向該字元在字串中的索引.

[brute-force 字串搜尋](../Brute-Force String Search/) 還可以, 但其實並沒有非常有效率, 特別是長字串. 事實上, 你並不需要走訪字串中的 *每一個字元* -- 通常都可以跳過很多字元.

這種跳過字元的演算法稱為 [Boyer-Moore](https://en.wikipedia.org/wiki/Boyer–Moore_string_search_algorithm), 而它已經存在許久. 已經變成是所有字串搜尋演算法的基準.


```swift
extension String {
    func index(of pattern: String) -> Index? {
        // Cache the length of the search pattern because we're going to
        // use it a few times and it's expensive to calculate.
        let patternLength = pattern.characters.count
        guard patternLength > 0, patternLength <= characters.count else { return nil }

        // Make the skip table. This table determines how far we skip ahead
        // when a character from the pattern is found.
        var skipTable = [Character: Int]()
        for (i, c) in pattern.characters.enumerated() {
            skipTable[c] = patternLength - i - 1
        }

        // This points at the last character in the pattern.
        let p = pattern.index(before: pattern.endIndex)
        let lastChar = pattern[p]

        // The pattern is scanned right-to-left, so skip ahead in the string by
        // the length of the pattern. (Minus 1 because startIndex already points
        // at the first character in the source string.)
        var i = index(startIndex, offsetBy: patternLength - 1)

        // This is a helper function that steps backwards through both strings
        // until we find a character that doesn’t match, or until we’ve reached
        // the beginning of the pattern.
        func backwards() -> Index? {
            var q = p
            var j = i
            while q > pattern.startIndex {
                j = index(before: j)
                q = index(before: q)
                if self[j] != pattern[q] { return nil }
            }
            return j
        }

        // The main loop. Keep going until the end of the string is reached.
        while i < endIndex {
            let c = self[i]

            // Does the current character match the last character from the pattern?
            if c == lastChar {

                // There is a possible match. Do a brute-force search backwards.
                if let k = backwards() { return k }

                // If no match, we can only safely skip one character ahead.
                i = index(after: i)
            } else {
                // The characters are not equal, so skip ahead. The amount to skip is
                // determined by the skip table. If the character is not present in the
                // pattern, we can skip ahead by the full pattern length. However, if
                // the character *is* present in the pattern, there may be a match up
                // ahead and we can't skip as far.
                i = index(i, offsetBy: skipTable[c] ?? patternLength, limitedBy: endIndex) ?? endIndex
            }
        }
        return nil
    }
}
```


<!--
The algorithm works as follows. You line up the search pattern with the source string and see what character from the string matches the *last* character of the search pattern:
-->

這演算法運作是這樣的. 先將要搜尋的字串和原始字串並排, 然後比對要搜尋字串的最後一個字元和原始字串中相同索引位置的字元:


```
source string:  Hello, World
search pattern: World
                    ^
```

<!--
There are three possibilities:

1. The two characters are equal. You've found a possible match.

2. The characters are not equal, but the source character does appear in the search pattern elsewhere.

3. The source character does not appear in the search pattern at all.

In the example, the characters `o` and `d` do not match, but `o` does appear in the search pattern. That means we can skip ahead several positions:
-->

有三種可能的結果:

1. 兩個字元相同. 你找到了!
2. 兩個字元不同, 但是原始字串中的那個字元有在要搜尋字串中出現.
3. 原始字串中的字元完全沒有在要搜尋字串中出現.

此例中, 字元 `o` 和 字元  `d` 不相同, 但是 `o` 的確有在要搜尋的字串中出現. 這表示我們可以跳過某些位置;

```
source string:  Hello, World
search pattern:    World
                       ^
```

<!--
Note how the two `o` characters line up now. Again you compare the last character of the search pattern with the search text: `W` vs `d`. These are not equal but the `W` does appear in the pattern. So skip ahead again to line up those two `W` characters:
-->

注意到現在兩個 `o` 字元對齊了. 再一次的比對要搜尋字串的最後字元和原始字串對應索引的字元: `d` vs `W`. 兩個字元不一樣, 但是 `W` 也確實有出現在要搜尋字串中. 所以用對齊來跳過字元, 將搜尋字串中的 `W` 對其原始字串中的 `W`:


```
source string:  Hello, World
search pattern:        World
                           ^
```

<!--
This time the two characters are equal and there is a possible match. To verify the match you do a brute-force search, but backwards, from the end of the search pattern to the beginning. And that's all there is to it.

The amount to skip ahead at any given time is determined by the "skip table", which is a dictionary of all the characters in the search pattern and the amount to skip by. The skip table in the example looks like:
-->

這時候, 要搜尋字元串的最後字元 `d` 和原始字串對應索引字元 `d` 相同了, 這表示有可能對齊到正確的位置 (因為對齊了 `W` 之後又對齊了 `d`, 但是中間沒有檢查). 為了驗證是否真的找到要搜尋的字串, 我們反過來使用 brute-force 字串搜尋法, 從要搜尋字串的最尾端到最前端.

而跳躍字數的依據是由 "skip table" 來決定, 它是個含有所有字元跟相對應跳躍字數的字典物件:

```
W: 4
o: 3
r: 2
l: 1
d: 0
```

<!--
The closer a character is to the end of the pattern, the smaller the skip amount. If a character appears more than once in the pattern, the one nearest to the end of the pattern determines the skip value for that character.

> **Note:** If the search pattern consists of only a few characters, it's faster to do a brute-force search. There's a trade-off between the time it takes to build the skip table and doing brute-force for short patterns.

Credits: This code is based on the article ["Faster String Searches" by Costas Menico](http://www.drdobbs.com/database/faster-string-searches/184408171) from Dr Dobb's magazine, July 1989 -- Yes, 1989! Sometimes it's useful to keep those old magazines around.

See also: [a detailed analysis](http://www.inf.fh-flensburg.de/lang/algorithmen/pattern/bmen.htm) of the algorithm.
-->

越接近尾端的字元, 跳躍的數量就越低. 如果某個字元出現兩次以上, 以最接近尾端的那個字元的跳躍數量來算.

> **注意:** 如果要搜尋的字串長度很短, 那直接使用 brute-force 搜尋會比較快一點. 在建立 skip table 和直接做 brute-force 之間需要利弊上的衡量.

這段程式碼是基於這篇 ["Faster String Searches" by Costas Menico](http://www.drdobbs.com/database/faster-string-searches/184408171) 文章, 擷取自 1989 年 7 月的 Dr Dobb's 雜誌 --  是的! 1989! 有時候保留這些古老的雜誌挺有用.

相關閱讀: [深入解析](http://www.inf.fh-flensburg.de/lang/algorithmen/pattern/bmen.htm) 演算法.

## Boyer-Moore-Horspool algorithm

<!--
A variation on the above algorithm is the [Boyer-Moore-Horspool algorithm](https://en.wikipedia.org/wiki/Boyer%E2%80%93Moore%E2%80%93Horspool_algorithm).

Like the regular Boyer-Moore algorithm, it uses the `skipTable` to skip ahead a number of characters. The difference is in how we check partial matches. In the above version, if a partial match is found but it's not a complete match, we skip ahead by just one character. In this revised version, we also use the skip table in that situation.

Here's an implementation of the Boyer-Moore-Horspool algorithm:
-->


[Boyer-Moore-Horspool 演算法](https://en.wikipedia.org/wiki/Boyer%E2%80%93Moore%E2%80%93Horspool_algorithm)是上述演算法的變化.

就跟一般的 Boyer-Moore 演算法一樣, 它使用 `skipTable` 去跳躍字元. 不同點在於檢查吻合的地方. 在上述的版本中, 當我們找到了部分吻合但還沒確認完全吻合時, 我們一次跳過一個字元來比對, 在這個版本中, 我們也將 skip table 用在該情況中.

這裡是 Boyer-Moore-Horspool 演算法的實作:


```swift
extension String {
    func index(of pattern: String) -> Index? {
        // Cache the length of the search pattern because we're going to
        // use it a few times and it's expensive to calculate.
        let patternLength = pattern.characters.count
        guard patternLength > 0, patternLength <= characters.count else { return nil }

        // Make the skip table. This table determines how far we skip ahead
        // when a character from the pattern is found.
        var skipTable = [Character: Int]()
        for (i, c) in pattern.characters.enumerated() {
            skipTable[c] = patternLength - i - 1
        }

        // This points at the last character in the pattern.
        let p = pattern.index(before: pattern.endIndex)
        let lastChar = pattern[p]

        // The pattern is scanned right-to-left, so skip ahead in the string by
        // the length of the pattern. (Minus 1 because startIndex already points
        // at the first character in the source string.)
        var i = index(startIndex, offsetBy: patternLength - 1)

        // This is a helper function that steps backwards through both strings
        // until we find a character that doesn’t match, or until we’ve reached
        // the beginning of the pattern.
        func backwards() -> Index? {
            var q = p
            var j = i
            while q > pattern.startIndex {
                j = index(before: j)
                q = index(before: q)
                if self[j] != pattern[q] { return nil }
            }
            return j
        }

        // The main loop. Keep going until the end of the string is reached.
        while i < endIndex {
            let c = self[i]

            // Does the current character match the last character from the pattern?
            if c == lastChar {

                // There is a possible match. Do a brute-force search backwards.
                if let k = backwards() { return k }

                // Ensure to jump at least one character (this is needed because the first
                // character is in the skipTable, and `skipTable[lastChar] = 0`)
                let jumpOffset = max(skipTable[c] ?? patternLength, 1)
                i = index(i, offsetBy: jumpOffset, limitedBy: endIndex) ?? endIndex
            } else {
                // The characters are not equal, so skip ahead. The amount to skip is
                // determined by the skip table. If the character is not present in the
                // pattern, we can skip ahead by the full pattern length. However, if
                // the character *is* present in the pattern, there may be a match up
                // ahead and we can't skip as far.
                i = index(i, offsetBy: skipTable[c] ?? patternLength, limitedBy: endIndex) ?? endIndex
            }
        }
        return nil
    }
}
```

<!--
In practice, the Horspool version of the algorithm tends to perform a little better than the original. However, it depends on the tradeoffs you're willing to make.

Credits: This code is based on the paper: [R. N. Horspool (1980). "Practical fast searching in strings". Software - Practice & Experience 10 (6): 501–506.](http://www.cin.br/~paguso/courses/if767/bib/Horspool_1980.pdf)
-->


操作上, Horsool 版本有比原本好的趨勢, 但無論如何, 這都要看你願意交換多少資源來考慮.

這段程式碼是基於這篇論文: [R. N. Horspool (1980). "Practical fast searching in strings". Software - Practice & Experience 10 (6): 501–506.](http://www.cin.br/~paguso/courses/if767/bib/Horspool_1980.pdf)

_Written for Swift Algorithm Club by Matthijs Hollemans, updated by Andreas Neusüß_, [Matías Mazzei](https://github.com/mmazzei).
