# Set Cover (Unweighted) (覆蓋集合-無加權)

<!--
If you have a group of sets, this algorithm finds a subset of those sets within that group whose union will cover an initial set that you're trying to match. The initial set is also known as the universe.

For example, suppose you have a universe of `{1, 5, 7}` and you want to find the sets which cover the universe within the following group of sets:
-->

如果你有一組集合, 這個演算法可以找到一些在那些集合的子集合, 它的聯集將會包含到你想要比對的初始集合. 這個初始集合就是宇集.

舉例來說, 假設你有 `{1, 5, 7}` 這個宇集, 而你想要在下列集合組中找到有哪集合會包含到這個宇集:

> {8, 4, 2}
> {3, 1}
> {7, 6, 5, 4}
> {2}
> {1, 2, 3}

<!--
You can see that the sets `{3, 1} {7, 6, 5, 4}` when unioned together will cover the universe of `{1, 5, 7}`. Yes, there may be additional elements in the sets returned by the algorithm, but every element in the universe is represented in the cover itself.

There may be cases where no cover exists. For example, if your universe is `{7, 9}`, there is no combination of sets within the group above that will yield a cover.
-->

可以看到 `{3, 1} {7, 6, 5, 4}` 的聯集將會包含到 `{1, 5, 7}`. 是的, 聯集中還有其他元素, 但宇集中的每個元素在裡面都有出現.

<!--
## The algorithm

The Greedy Set Cover algorithm (unweighted) is provided here. It's known as greedy because it uses the largest intersecting set from the group of sets first before examining other sets in the group. This is part of the reason why the cover may have additional elements which are not part of the universe.

The function (named `cover`) is provided as an extension of the Swift type `Set`. The function takes a single parameter, which is an array of sets. This array represents the group, and the set itself represents the universe.

One of the first things done in `cover` is to make a copy of the universe in `remainingSet`. Then, the algorithm enters a `while` loop in which a call to `largestIntersectingSet` is made. The value returned from `largestIntersectingSet` is the set which has the most elements in common with the remaining universe identified by `remainingSet`. If all sets have nothing in common, `largestIntersectingSet` returns `nil`.

If the result from `largestIntersectingSet` is not nil, that result is subtracted from `remainingSet` (reducing its size), and the loop continues until `remainingSet` has zero length (meaning a cover has been found) or until `largestIntersectingSet` returns `nil`.

If there is no cover within the group of sets, `cover` returns `nil`.
-->

## 演算法

這邊提供的是貪婪覆蓋集合演算法 (無加權). 被稱為貪婪是因為此演算法在檢視集合組中其他集合之前先用集合組中最大的交集. 這也是為什麼最後的覆蓋會不屬於宇集的其他元素.

此 `cover` 方法是由 Swift 中 `Set` 型別的擴充提供. 這方法接受一個集合陣列 (內容元素是集合的陣列) 做參數. 這個陣列代表集合組, 而集合本身代表宇集.

在 `cover` 方法中第一件要做的事就是建立宇集的副本, 並存在 `remainingSet` 變數中. 然後演算法會進入 `while` 迴圈並且呼叫 `largestIntersectingSet`. `largestIntersectingSet` 的回傳值是一個包含最多宇集尚未包含元素的集合, 宇集中尚未包含元素的集合用 `remainingSet` 變數來儲存. 如果集合組中所有的集合都沒有包含到 `remainingSet` 中的元素, 那 `largestIntersectingSet` 會回傳 `nil`.

如果 `largestIntersectingSet` 沒有回傳 `nil`, 那將回傳的集合元素將會從 `remainingSet` 中移除, 然後再次執行迴圈直到 `remainingSet` 空了 (表示已經覆蓋了全部元素) 或 `largestIntersectingSet` 回傳 `nil`.

如國集合組沒有完全覆蓋, 那 `cover` 方法會回傳 `nil`.


<!--
## See also
-->

## 相關閱讀

[Set cover problem on Wikipedia](https://en.wikipedia.org/wiki/Set_cover_problem)

*Written for Swift Algorithm Club by [Michael C. Rael](https://github.com/mrael2)*