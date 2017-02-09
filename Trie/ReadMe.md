# Trie (前綴樹)

<!--
## What is a Trie?

A `Trie`, (also known as a prefix tree, or radix tree in some other implementations) is a special type of tree used to store associative data structures. A `Trie` for a dictionary might look like this:
-->

## 前綴樹是什麼?

是一種儲存關聯資料的樹狀結構. 一個 `前綴樹` 可能像這樣:

![A Trie](images/trie.png)

<!--
Storing the English language is a primary use case for a `Trie`. Each node in the `Trie` would represent a single character of a word. A series of nodes then make up a word.
-->

英文單字主要就是用 `前驅樹` 來儲存. 每個 `前驅樹` 的節點表示單一個英文字母. 可以用一序列節點組合來構成一個單字.

<!--
## Why a Trie?

Tries are very useful for certain situations. Here are some of the advantages:

* Looking up values typically have a better worst-case time complexity.
* Unlike a hash map, a `Trie` does not need to worry about key collisions.
* Doesn't utilize hashing to guarantee a unique path to elements.
* `Trie` structures can be alphabetically ordered by default.
-->

## 為什麼要用 Trie?

前驅樹在某些情況非常有用. 這裡是它的優點:

* 查找某個值基本上有個比較好的最糟時間複雜度.
* 和雜湊表不同, `前驅樹` 不需要考慮鍵值衝突.
* 不需要使用雜湊來保證元素的唯一路徑.
* `Trie` 結構可以預設依照字母順序排列.

<!--
## Common Algorithms

### Contains (or any general lookup method)

`Trie` structures are great for lookup operations. For `Trie` structures that model the English language, finding a particular word is a matter of a few pointer traversals:
-->

## 一般演算法

### 是否存在 (或任何查找方法)

`Trie` 結構對於查找操作表現很棒. 因為 `前驅樹` 架構了英文單字, 要找到某個單字只需要走訪幾個指標:

```swift
func contains(word: String) -> Bool {
	guard !word.isEmpty else { return false }

	// 1
	var currentNode = root
  
	// 2
	var characters = Array(word.lowercased().characters)
	var currentIndex = 0
 
	// 3
	while currentIndex < characters.count, 
	  let child = currentNode.children[characters[currentIndex]] {

	  currentNode = child
	  currentIndex += 1
	}

	// 4
	if currentIndex == characters.count && currentNode.isTerminating {
	  return true
	} else {
		return false
	}
}
```

<!--
The `contains` method is fairly straightforward:

1. Create a reference to the `root`. This reference will allow you to walk down a chain of nodes.
2. Keep track of the characters of the word you're trying to match.
3. Walk the pointer down the nodes.
4. `isTerminating` is a boolean flag for whether or not this node is the end of a word. If this `if` condition is satisfied, it means you are able to find the word in the `trie`.
-->

`contains` 方法挺直觀的:

1. 建立一個 `root` 的參考指標. 這個指標可以讓你沿線向下走訪節點.
2. 追蹤你要比對的字母.
3. 向下走訪節點.
4. `isTerminating` 是個布林值, 表示這個節點是否是某個單字的結尾. 如果這個 `if` 條件成立, 表示你可以在 `前驅樹` 中找到該單字.

<!--
### Insertion

Insertion into a `Trie` requires you to walk over the nodes until you either halt on a node that must be marked as `terminating`, or reach a point where you need to add extra nodes.
-->

### 插入

`前驅樹` 中的插入操作需要走訪節點直到你遇到一個必須要標註為 `terminating` 的節點, 或到結尾了你需要新增一個節點為止.


```swift
func insert(word: String) {
  guard !word.isEmpty else {
    return
  }

  // 1
  var currentNode = root

  // 2
  for character in word.lowercased().characters {
    // 3
    if let childNode = currentNode.children[character] {
      currentNode = childNode
    } else {
      currentNode.add(value: character)
      currentNode = currentNode.children[character]!
    }
  }
  // Word already present?
  guard !currentNode.isTerminating else {
    return
  }

  // 4
  wordCount += 1
  currentNode.isTerminating = true
}
```

<!--
1. Once again, you create a reference to the root node. You'll move this reference down a chain of nodes.
2. Begin walking through your word letter by letter
3. Sometimes, the required node to insert already exists. That is the case for two words inside the `Trie` that shares letters (i.e "Apple", "App"). If a letter already exists, you'll reuse it, and simply traverse deeper down the chain. Otherwise, you'll create a new node representing the letter.
4. Once you get to the end, you mark `isTerminating` to true to mark that specific node as the end of a word.
-->

1. 一樣, 建立一個根節點的參考指標. 這個指標可以讓你沿線向下走訪節點.
2. 依照單子的每個字母依序走訪.
3. 有些時候, 插入的節點已經存在. 這表示有兩個單字有共同的相同順序字母 (例如 "Apple", "App"). 如果字母已經存在, 直接使用它, 然後簡單地繼續往下層走訪. 如果字母不存在, 那就會建立一個新的節點表示該字母.
4. 一旦走訪到結尾, 把 `isTerminating` 設為 true 來表示該節點是某個單字的結尾.

<!--
### Removal

Removing keys from the trie is a little tricky, as there are a few more cases you'll need to take into account. Nodes in a `Trie` may be shared between different words. Consider the two words "Apple" and "App". Inside a `Trie`, the chain of nodes representing "App" is shared with "Apple". 

If you'd like to remove "Apple", you'll need to take care to leave the "App" chain in tact.
-->

### 移除

移除操作有點小棘手, 因為你需要考慮很多種型情. 在 `前驅樹` 中的節點可能在不同單字之間共享字母. 想想兩個單字 "Apple" 和 "App". 在 `前驅樹` 中, 表達 "App" 的節點鏈結和 "Apple" 是共享的.

如果你想要移除 "Apple", 你需要保持 "App" 原封不動.

```swift
func remove(word: String) {
  guard !word.isEmpty else {
    return
  }

  // 1
  guard let terminalNode = findTerminalNodeOf(word: word) else {
    return
  }

  // 2
  if terminalNode.isLeaf {
    deleteNodesForWordEndingWith(terminalNode: terminalNode)
  } else {
    terminalNode.isTerminating = false
  }
  wordCount -= 1
}
```

<!--
1. `findTerminalNodeOf` traverses through the Trie to find the last node that represents the `word`. If it is unable to traverse through the chain of characters, it returns `nil`.
2. `deleteNodesForWordEndingWith` traverse backwords, deleting the nodes represented by the `word`.
-->

1. `findTerminalNodeOf` 走訪整個前驅樹找到要移除 `單字` 的最後一個字母節點. 如果在字母鍊中找不到, 回傳 `nil`.
2. `deleteNodesForWordEndingWith` 反過來走訪, 刪除可以表示該 `單字` 的節點.

<!--
### Time Complexity

Let n be the length of some value in the `Trie`.

-->

### 時間複雜度

假設 n 是 `前驅樹` 中某個值的長度.

* `contains` - Worst case O(n)
* `insert` - O(n)
* `remove` - O(n)

<!--
### Other Notable Operations

* `count`: Returns the number of keys in the `Trie` - O(1)
* `words`: Returns a list containing all the keys in the `Trie` - O(1)
* `isEmpty`: Returns `true` if the `Trie` is empty, `false` otherwise - O(1)
-->

### 其他值得注意的操作

* `count`: 回傳該 `前驅樹` 單字的數量 - O(1)
* `words`: 回傳 `前驅樹` 中所有的單字 - O(1)
* `isEmpty`: 如果 `前驅樹` 是空的回傳 `true`, 反之回傳 `false` - O(1)


See also [Wikipedia entry for Trie](https://en.wikipedia.org/wiki/Trie).

*Written for the Swift Algorithm Club by Christian Encarnacion. Refactored by Kelvin Lau*

# Changes by Rick Zaccone

* Added comments to all methods
* Refactored the `remove` method
* Renamed some variables.  I have mixed feelings about the way Swift infers types.  It's not always apparent what type a variable will have.  To address this, I made changes such as renaming `parent` to `parentNode` to emphasize that it is a node and not the value contained within the node.
* Added a `words` property that recursively traverses the trie and constructs an array containing all of the words in the trie.
* Added a `isLeaf` property to `TrieNode` for readability.
* Implemented `count` and `isEmpty` properties for the trie.
* I tried stress testing the trie by adding 162,825 words.  The playground was very slow while adding the words and eventually crashed.  To fix this problem, I moved everything into a project and wrote `XCTest` tests that test the trie.  There are also several performance tests.  Everything passes.
