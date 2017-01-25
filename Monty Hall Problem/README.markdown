# The Monty Hall Problem (蒙提霍爾問題)

<!--
Congrats! You've reached the final round of the popular [Monty Hall game show](https://en.wikipedia.org/wiki/Let%27s_Make_a_Deal). Monty, the show host, gives you the choice between 3 doors. Behind one of the doors is a prize (a new car? a trip to Hawaii? a microwave oven?), the other two are empty.

After you make your choice, Monty decides to make things a bit more interesting and opens one of the two doors that you didn't pick. Of course, the one he opens is empty. There are now two doors left, behind one of which is the coveted prize.

Now Monty gives you the opportunity to change your mind. Should you stick with your original choice, should you pick the other door, or doesn't it matter?

You'd think that changing your answer wouldn't improve your chances... but it does!

This is a very nonintuitive result. Maybe you have trouble believing this is true. Don't worry, when this problem was first proposed many professional mathematicians didn't believe it either, so you're in good company.

There's a simple way to verify this claim: we can write a program to test it out! We should be able to show who wins more often by playing the game a large number of times.

Here's the code (see the [playground](MontyHall.playground/Contents.swift) for the full thing). First, we randomly choose the door that has the prize:
-->

恭喜! 你到達了 [蒙提霍爾遊戲時間](https://en.wikipedia.org/wiki/Let%27s_Make_a_Deal) 的最後階段. 主持人蒙提給你三個門的選項. 在這些門後, 一是獎金 (一台新車? 一趟夏威夷的旅程? 一個微波爐?), 另外兩個門後是空的.

在你下完決定後, 蒙提決定讓事情變得更有趣一點, 他打開另外兩道門其中之一, 當然, 他開的門後會是空的. 那現在剩下兩道門, 一道是空的, 一道門後有獎金.

現在蒙提給你一次改變心意的機會. 你應該保持原本的選擇, 還是改選另一道門呢, 還是根本沒差呢?

你可能會想改變選項並不會增加你中獎的機率... 但事實上是會的!

這是一個非常不直觀的結論. 可能你會很難相信它. 別擔心, 很多專業的數學家在一開始也不相信.

有個簡單的證明: 我們可以寫個程式來模擬! 我們可以計算遊戲非常多次以後誰贏的次數比較多.

這裡是程式碼 (詳細請見 [playground](MontyHall.playground/Contents.swift)). 首先, 我們隨機選擇有獎的門:


```swift
  let prizeDoor = random(3)
```
<!--
We also randomly pick the choice of the player:
-->

隨機的選擇一扇門:

```swift
  let chooseDoor = random(3)
```

<!--
Next, Monty opens one of the empty doors. Obviously, he won't choose the door that the player chose or the one with the prize.
-->

接下來, 蒙提開啟一扇空門. 很明顯的, 他不會打開玩家選的, 也不會打開有獎的門.

```swift
  var openDoor = -1
  repeat {
    openDoor = random(3)
  } while openDoor == prizeDoor || openDoor == chooseDoor
```  

<!--
There are only two closed doors left, one of which has the prize. What happens when the player changes his mind and picks the other door?
-->

現在只剩下兩扇關著的門, 其中之一是有獎的. 這時候玩家改變選擇會怎樣?

```swift
  var changeMind = -1
  repeat {
    changeMind = random(3)
  } while changeMind == openDoor || changeMind == chooseDoor
```

<!--
Now we see which choice was the right one:
-->

最後我們來看是否有選到有獎的門:

```swift
  if chooseDoor == prizeDoor {
    winOriginalChoice += 1
  }
  if changeMind == prizeDoor {
    winChangedMind += 1
  }
```

<!--
If the prize is behind the player's original door choice, we increment `winOriginalChoice`. If the prize is behind the other door, then the player would have won if he changed his mind, and so we increment `winChangedMind`.

And that's all there is to it.

If you run the above code 1000 times or so, you'll find that the probability of choosing the prize without changing your mind is about 33%. But if you *do* change your mind, the probability of winning is 67% -- that is twice as large!

Try it out in the playground if you still don't believe it. ;-)

Here's why: When you first make a choice, your chances of picking the prize are 1 out of 3, or 33%

After Monty opens one of the doors, this gives you new information. However, it doesn't change the probability of your original choice being the winner. That chance remains 33% because you made that choice when you didn't know yet what was behind this open door.

Since probabilities always need to add up to 100%, the chance that the prize is behind the other door is now 100 - 33 = 67%. So, as strange as it may sound, you're better off switching doors!

This is hard to wrap your head around, but easily shown using a simulation that runs a significant number of times. Probability is weird.

By the way, you can simplify the code to this:
-->

如果獎品在玩家原本選擇的門, 我們將 `winOriginalChoice` 遞增. 如果獎品在另一個門, 那玩家就會因為改變心意而得獎, 所以我們遞增 `winChangedMind`.

這就是全部的模擬了.

如果你執行這段模擬 1000 次左右, 你會發現沒有改變心意的勝率大概是 33%. 但是如果你 *改變* 了你的選項, 勝率是 67% -- 兩倍!

如果你還是很難相信, 在 playground 中試試看! ;-)

解釋: 你在第一次選擇時, 你選中有獎的門的機率是 3 分之 1, 或 33%.

當蒙提開啟一扇沒獎的門, 也告訴你了一些新的資訊. 無論如何, 這件事並沒有改變你原本選項的中獎機率. 因為當你在選擇第一個門的時候, 並不知道其他兩個門的資訊, 所以原本的選項中獎機率還是 33%.

因為事件機率總和要是 100%, 這時候另一個們的機率是 100 - 33 = 67%. 所以, 儘管聽起來怪怪的, 但是最好還是換一個門.

這很難用解釋的讓你了解, 不果可以從模擬中輕易的看出結果的不同.

順帶一提, 你可以把程式簡化成這樣:


```swift
  let prizeDoor = random(3)
  let chooseDoor = random(3)
  if chooseDoor == prizeDoor {
    winOriginalChoice += 1
  } else {
    winChangedMind += 1
  }
```

<!--
Now it's no longer a simulation but the logic is equivalent. You can clearly see that the `chooseDoor` only wins 1/3rd of the time -- because it's a random number between 1 and 3 -- so changing your mind must win the other 2/3rds of the time.
-->

雖然現在沒有在模擬跑次數了, 但是邏輯是一樣的. 你可以很清楚的看到 `chooseDoor` 只有 1/3 的時間是贏的 -- 因為隨機數字是在 1 到 3 之間 -- 所以改變選項就會贏得剩下的 2/3 時間.

[Monty Hall Problem on Wikipedia](https://en.wikipedia.org/wiki/Monty_Hall_problem)

*Written for Swift Algorithm Club by Matthijs Hollemans*
