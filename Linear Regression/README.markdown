# Linear Regression (迴歸分析)

<!--
Linear regression is a technique for creating a model of the relationship between two (or more) variable quantities.

For example, let's say we are planning to sell a car. We are not sure how much money to ask for. So we look at recent advertisments for the asking prices of other cars. There are a lot of variables we could look at - for example: make, model, engine size. To simplify our task, we collect data on just the age of the car and the price:
-->

建立兩個 (或更多) 數值變數之間關聯模型的一種技術.

舉例來說, 讓我們假設計畫要賣車. 但是並不知道應該訂多少價格. 所以我們查看最近廣告的車輛和它們的價格. 而有很多變數我們可以參考 - 譬如: 製成, 模具, 引擎大小. 為了簡化工作, 我們只收集車齡和車價:

Age (in years)| Price (in £)
--------------|-------------
10 | 500
8 | 400
3 | 7,000
3 | 8,500
2 | 11,000
1 | 10,500

<!--
Our car is 4 years old. How can we set a price for our car based on the data in this table?

Let's start by looking at the data plotted out:
-->

我們的車齡已經 4 年了. 那我們依據這張表格, 可以將我們的車定價在多少呢?

先從資料點狀圖來看看:

![graph1](Images/graph1.png)

<!--
We could imagine a straight line drawn through the points on this graph. It's not (in this case) going to go exactly through every point, but we could place the line so that it goes as close to all the points as possible.

To say this in another way, we want to make the distance from the line to each point as small as possible. This is most often done by minimising the square of the distance from the line to each point.

We can describe the straight line in terms of two variables:

1. The point at which it crosses the y-axis i.e. the predicted price of a brand new car. This is the *intercept*.
2. The *slope* of the line - i.e. for every year of age, how much does the price change.

This is the equation for our line:

`carPrice = slope * carAge + intercept`


How can we find the best values for the intercept and the slope? Let's look at two different ways to do this.
-->

我們可以想像有一條直線穿過這些點. 但是它並不是真的有碰到每個點 (在此例子中), 但我們可以把這條線盡可能的靠近每個點.

換句話說, 我們要讓給個點到這條線的距離越小越好. 這點通常以最小平方法來達成.

我們可以用兩個變數來描述這個直線:

1. 和 y 軸的焦點, 即預估新車的價格. 這就是 *截距*.
2. 直線的 *斜率* - 即每年車價的變化.

所以這條線的方程式:

`carPrice = slope * carAge + intercept`

那如何找到最佳的截距和斜率呢? 讓我們看看兩種做法:

<!--
## An iterative approach
One approach is to start with some arbitrary values for the intercept and the slope. We work out what small changes we make to these values to move our line closer to the data points. Then we repeat this multiple times. Eventually our line will approach the optimum position.

First let's set up our data structures. We will use two Swift arrays for the car age and the car price:
-->

## 迭代趨近
一種趨近法是從任意的的截距和斜率開始. 我們計算對這些值得小改變可以使我們的直線更靠近資料點. 然後重複很多次. 最終我們的直線會趨近最佳位置.


```swift
let carAge: [Double] = [10, 8, 3, 3, 2, 1]
let carPrice: [Double] = [500, 400, 7000, 8500, 11000, 10500]
```

<!--
This is how we can represent our straight line:
-->

我們可以這樣表示直線:

```swift
var intercept = 0.0
var slope = 0.0
func predictedCarPrice(_ carAge: Double) -> Double {
    return intercept + slope * carAge
}

```

<!--
Now for the code which will perform the iterations:
-->

下列程式碼展示迭代部分:

```swift
let numberOfCarAdvertsWeSaw = carPrice.count
let numberOfIterations = 100
let alpha = 0.0001

for n in 1...numberOfIterations {
    for i in 0..<numberOfCarAdvertsWeSaw {
        let difference = carPrice[i] - predictedCarPrice(carAge[i])
        intercept += alpha * difference
        slope += alpha * difference * carAge[i]
    }
}
```

<!--
```alpha``` is a factor that determines how much closer we move to the correct solution with each iteration. If this factor is too large then our program will not converge on the correct solution.

The program loops through each data point (each car age and car price). For each data point it adjusts the intercept and the slope to bring them closer to the correct values. The equations used in the code to adjust the intercept and the slope are based on moving in the direction of the maximal reduction of these variables. This is a *gradient descent*.

We want to minimse the square of the distance between the line and the points. We define a function `J` which represents this distance - for simplicity we consider only one point here. This function `J` is proprotional to `((slope.carAge + intercept) - carPrice)) ^ 2`.

In order to move in the direction of maximal reduction, we take the partial derivative of this function with respect to the slope, and similarly for the intercept. We multiply these derivatives by our factor alpha and then use them to adjust the values of slope and intercept on each iteration.

Looking at the code, it intuitively makes sense - the larger the difference between the current predicted car Price and the actual car price, and the larger the value of ```alpha```, the greater the adjustments to the intercept and the slope.

It can take a lot of iterations to approach the ideal values. Let's look at how the intercept and slope change as we increase the number of iterations:
-->

```alpha``` 是個決定在每次迭代時, 要多靠近我們想得到的答案的因子. 如果這個因子太大, 那最後收斂的結果就不會是好的答案.

這個程式走訪每個資料點 (車齡及車價). 每個資料點都會調整一下截距和斜率. 在程式碼中使用的方程式是基於讓這些直線往最大減少距離的方向上移動. 這就是 *梯度下降法*.

我們想要最小化資料點和直線間距離的平方. 我們定義函式 `J` 代表距離 - 為了簡化我們這邊只考慮一個點. `J` 和 `((slope.carAge + intercept) - carPrice)) ^ 2` 是成正比的.


為了讓直線朝向最大減少距離的方向上移動, 我們對這個函式中的斜率和截距做偏導數. 將這些導數和因子 alpah 相乘, 然後迭代每次的斜率和截距.

再看看程式碼, 它直覺性的有感 - 資料點和預測點的距離和 alpha 越大, 則調整的斜率和截距幅度也越大.

有可能要執行很多次迭代才能趨近理想的值. 讓我們來看迭代次數和截距及斜率的對照吧:


Iterations | Intercept | Slope | Predicted value of a 4 year old car
:---------:|:---------:|:-----:|:------------------------:
0 | 0 | 0 | 0
2000 | 4112 | -113 | 3659 
6000 | 8564 | -764 | 5507 
10000 | 10517 | -1049 | 6318 
14000 | 11374 | -1175 | 6673 
18000 | 11750 | -1230 | 6829 

<!--
Here is the same data shown as a graph. Each of the blue lines on the graph represents a row in the table above.
-->

這是對同一份資料的圖. 每條藍線表示上表中對應的迭代次數

![graph2](Images/graph2.png)

<!--
After 18,000 iterations it looks as if the line is getting closer to what we would expect (just by looking) to be the correct line of best fit. Also, each additional 2,000 iterations has less and less effect on the final result - the values of the intercept and the slope are converging on the correct values.
-->

經過 18,000 次迭代後, 它看起來越來越像我們期望的最佳解 (單純只是看起來). 還有, 在之後再增加 2000 次迭代對於結果的影像越來越小 - 截距和斜率差不多收斂到正確的值.

<!--
##A closed form solution

There is another way we can calculate the line of best fit, without having to do multiple iterations. We can solve the equations describing the least squares minimisation and just work out the intercept and slope directly. 

First we need some helper functions. This one calculates the average (the mean) of an array of Doubles:
-->

## 公式解

有另一個方法可以計算最佳解且不需要多次迭代. 利用最小平方法可以直接算出截距和斜率.

先建立一些有幫助的函式. 這個可以計算 Double 陣列的平均數:

```swift
func average(_ input: [Double]) -> Double {
    return input.reduce(0, +) / Double(input.count)
}
```

<!--
We are using the ```reduce``` Swift function to sum up all the elements of the array, and then divide that by the number of elements. This gives us the mean value.

We also need to be able to multiply each element in an array by the corresponding element in another array, to create a new array. Here is a function which will do this:
-->

我們使用 Swift 原生的 ```reduce``` 函式來加總陣列中的元素, 然後除以元素個數. 回傳平均.

我們也需要一個可以將兩個陣列對應元素相乘的函式:


```swift
func multiply(_ a: [Double], _ b: [Double]) -> [Double] {
    return zip(a,b).map(*)
}
```

<!--
We are using the ```map``` function to multiply each element.

Finally, the function which fits the line to the data:
-->

使用 ```map``` 函式去做兩兩相乘的動作.

最後, 這個函式找到最適的直線:

```swift
func linearRegression(_ xs: [Double], _ ys: [Double]) -> (Double) -> Double {
    let sum1 = average(multiply(ys, xs)) - average(xs) * average(ys)
    let sum2 = average(multiply(xs, xs)) - pow(average(xs), 2)
    let slope = sum1 / sum2
    let intercept = average(ys) - slope * average(xs)
    return { x in intercept + slope * x }
}
```

<!--
This function takes as arguments two arrays of Doubles, and returns a function which is the line of best fit. The formulas to calculate the slope and the intercept can be derived from our definition of the function `J`. Let's see how the output from this line fits our data:
-->

此函式接受兩個陣列當參數, 並回傳最適直線的函式. 這個公式計算的斜率和截距可以由函式 `J` 中定義得知. 讓我們來看看結果:

![graph3](Images/graph3.png)

<!--
Using this line, we would predict a price for our 4 year old car of £6952.
-->

使用這要線, 我們可以預測 4 年車可以賣到 £6952.

<!--
##Summary

We've seen two different ways to implement a simple linear regression in Swift. An obvious question is: why bother with the iterative approach at all? 

Well, the line we've found doesn't fit the data perfectly. For one thing, the graph includes some negative values at high car ages! Possibly we would have to pay someone to tow away a very old car... but really these negative values just show that we have not modelled the real life situation very accurately. The relationship between the car age and the car price is not linear but instead is some other function. We also know that a car's price is not just related to its age but also other factors such as the make, model and engine size of the car. We would need to use additional variables to describe these other factors. 

It turns out that in some of these more complicated models, the iterative approach is the only viable or efficient approach. This can also occur when the arrays of data are very large and may be sparsely populated with data values.
-->

## 摘要

我們已經看到在 Swift 中兩種實作簡單迴歸分析的解法. 一個明顯的問題是: 為甚麼還要用迭代趨近?

嗯, 其實我們找到的直線並沒有非常完美的適合我們的資料. 舉例來說, 直線道某年度後會有負數的價格! 我們再出售舊車的時候反而還要付錢... 但這些負值只表示我們還沒把真實情況模型化的很精確. 車齡跟價格之間的關係說不定不是簡單的線性關係, 可能是其他函式. 我們也沒考慮到其他可能影響車價的因素, 像是製成, 引擎大小. 我們需要更多的變數來解釋這些因子.

這表示在某些複雜的模型下, 迭代趨近可能是唯一可行或有效率的一種方法. 當資料量非常大而且又稀鬆時, 也可能發生這種情況.

*Written for Swift Algorithm Club by James Harrop*
