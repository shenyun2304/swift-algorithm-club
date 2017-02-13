# Shunting Yard Algorithm (調度場演算法)

<!--
Any mathematics we write is expressed in a notation known as *infix notation*. For example:
-->
在這裡任何一個數學式都是用 *中序式* 來表示. 舉例來說:

**A + B * C**

<!--
The operator is placed in between the operands, hence the expression is said to be in *infix* form. If you think about it, any expression that you write on a piece of paper will always be in infix form. This is what we humans understand.

Multiplication has higher precedence than addition, so when the above expression is evaluated you would first multiply **B** and **C**, and then add the result to **A**. We humans can easily understand the precedence of operators, but a machine needs to be given instructions about each operator.

To change the order of evaluation, we can use parentheses:
-->

運算子會在運算元的中間, 所以這種表達式稱為 *中序式*. 想一下, 好像每一種我們寫在紙上的表達式都是中序式. 這是人類較好理解的樣式.

乘法比加法有更高的優先權, 所以上述的算式計算起來會先算 **B** 乘以 **C**, 然後再把結果和 **A** 相加. 人類可以很容易地理解這種運算子的優先順序, 但是對機器而言, 就需要對每個運算子給定動作指令.

要改變計算的順序, 可以使用括號:

**(A + B) * C**

<!--
Now **A** is first added to **B** and then the sum is multiplied by **C**.

If you were to write an algorithm that parsed and evaluated expressions in infix notation you will realize that it's a tedious process. You'd have to parse the expression multiple times to know what operation to perform first. As the number of operators increase so does the complexity.
-->

現在 **A** 會先和 **B** 相加, 然後結果在和 **C** 相乘.

如果你要寫個演算法來計算中序計算式, 你會發現那是個乏味的過程. 你必須要把表達式解析很多次才能知道哪些運算子要先做. 隨著運算子的增加, 複雜度也增加.

<!--
## Postfix notation

In postfix notation, also known as Reverse Polish Notation or RPN, the operators come after the corresponding operands. Here is the postfix representation of the example from the previous section:
-->


## 後序表達式

也稱作逆波蘭表達式或RPN, 運算子會緊接在運算元之後. 這是上述中序式用後序式來呈現的樣子:

**A B C * +**

<!--
An expression in postfix form will not have any parentheses and neither will you have to worry about scanning for operator precedence. This makes it easy for the computer to evaluate expressions, since the order in which the operators need to be applied is fixed.

Evaluating a postfix expression is easily done using a stack. Here is the pseudocode:

1. Read the postfix expression token by token
2. If the token is an operand, push it onto the stack
3. If the token is a binary operator,
    1. Pop the two topmost operands from the stack
    2. Apply the binary operator to the two operands
    3. Push the result back onto the stack
4. Finally, the value of the whole postfix expression remains in the stack

Using the above pseudocode, the evaluation of **A B C * +** would be as follows:
-->

在後序式中不會有括號也不需要考慮運算子的優先順序. 對電腦來說這種表達式比較好理解, 因為要怎麼使用運算子的順序都是固定的.

要計算後序式只需要用堆疊就可以辦到. 來看看虛擬碼:

1. 一個一個讀取運算式中的元素
2. 如果元素是運算元, 把元素放到堆疊中
3. 如果元素是二元運算子
	1. 從堆疊中取出最上層的兩個運算元
	2. 對兩個運算元執行二元運算子的操作
	3. 把運算結果放到堆疊中
4. 計算結果會是堆疊中剩下的元素

使用以上的虛擬碼, **A B C * +** 的計算會是這樣:


| Expression    | Stack   |
| ------------- | --------|
| A B C * +     |         |
| B C * +       | A       |
| C * +         | A, B    |
| * +           | A, B, C |
| +             | A, D    |
|               | E       |

<!--
Where **D = B * C** and **E = A + D.**

As seen above, a postfix operation is relatively easy to evaluate as the order in which the operators need to be applied is pre-decided.
-->

其中 **D = B * C**, **E = A + D**

可以看出, 後序式在計算上相對簡單, 因為在運算子要執行時, 先後順序是已經在事前就確定了.

<!--
## Converting infix to postfix

The shunting yard algorithm was invented by Edsger Dijkstra to convert an infix expression to postfix. Many calculators use this algorithm to convert the expression being entered to postfix form.

Here is the psedocode of the algorithm:

1. For all the input tokens:
    1. Read the next token
    2. If token is an operator (x)
        1. While there is an operator (y) at the top of the operators stack and either (x) is left-associative and its precedence is less or equal to that of (y), or (x) is right-associative and its precedence is less than (y)
            1. Pop (y) from the stack
            2. Add (y) output buffer
        2. Push (x) on the stack
    3. Else if token is left parenthesis, then push it on the stack
    4. Else if token is a right parenthesis
        1. Until the top token (from the stack) is left parenthesis, pop from the stack to the output buffer
        2. Also pop the left parenthesis but don’t include it in the output buffer
    7. Else add token to output buffer
2. Pop any remaining operator tokens from the stack to the output

Let's take a small example and see how the pseudocode works. Here is the infix expression to convert:
-->

## 中序轉後序

調度場演算法是由 Edsger Dijkstra 發明用來將中序表達式轉成後序表達式. 很多計算機都用這個演算法來將使用者輸入的中序表達式轉換成後序式來執行計算.

演算法的虛擬碼:

1. 對所有的輸入的元素:
	1. 讀取下一個元素
	2. 如果元素是運算子 (x)
		1. 當有一個運算子 (y) 在運算子堆疊的最上面, 且 (x) 是左相關運算子且優先權小於等於 (y) 或 (x) 是右相關運算子且優先權小於 (y)
			1. 從堆疊中取出 (y)
			2. 把 (y) 放到輸出緩衝之中
		2. 把 (x) 放到運算子堆疊中
	3. 如果是左括號, 放到運算子堆疊中
	4. 如果是右括號
		1. 連續取出運算子堆疊中的元素放到輸出緩衝中直到遇到左括號
		2. 取出左括號但是不需要放到輸出緩衝之中
	5. 以上皆非時, 則把元素放到輸出緩衝中
2. 將所有在運算子堆疊中的元素依序取到輸出緩衝之中

來做個小範例看看虛擬碼怎麼執行. 這是我們要轉換的中序表達式:

**4 + 4 * 2 / ( 1 - 5 )**

<!--
The following table describes the precedence and the associativity for each operator. The same values are used in the algorithm.
-->

以下的表格描述了運算子的優先權和相關性. 演算法裡面也是用一樣的值.


| Operator | Precedence   | Associativity   |
| ---------| -------------| ----------------|
| ^        | 10           | Right           |
| *        | 5            | Left            |
| /        | 5            | Left            |
| +        | 0            | Left            |
| -        | 0            | Left            |

<!--
Here we go:
-->

開始囉:

| Token | Action                                      | Output            | Operator stack |
|-------|---------------------------------------------|-------------------|----------------|
| 4     | Add token to output                         | 4                 |                |
| +     | Push token to stack                         | 4                 | +              |
| 4     | Add token to output                         | 4 4               | +              |
| *     | Push token to stack                         | 4 4               | * +            |
| 2     | Add token to output                         | 4 4 2             | * +            |
| /     | Pop stack to output, Push token to stack | 4 4 2 *           | / +            |
| (     | Push token to stack                         | 4 4 2 *           | ( / +          |
| 1     | Add token to output                         | 4 4 2 * 1         | ( / +          |
| -     | Push token to stack                         | 4 4 2 * 1         | - ( / +        |
| 5     | Add token to output                         | 4 4 2 * 1 5       | - ( / +        |
| )     | Pop stack to output, Pop stack           | 4 4 2 * 1 5 -     | /  +           |
| end   | Pop entire stack to output                  | 4 4 2 * 1 5 - / + |                |

<!--
We end up with the postfix expression:
-->

最後得到的後序式:

**4 4 2 * 1 5 - / +**

<!--
# See also
-->

## 相關閱讀
 
[Shunting yard algorithm on Wikipedia](https://en.wikipedia.org/wiki/Shunting-yard_algorithm)

*Written for the Swift Algorithm Club by [Ali Hafizji](http://www.github.com/aliHafizji)*
*Migrated to Swift 3 by Jaap Wijnen*
