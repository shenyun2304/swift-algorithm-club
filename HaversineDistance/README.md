# Haversine Distance (Haversine 距離: 球面上兩點距離)

<!--
Calculates the distance on a sphere between two points given in latitude and longitude using the haversine formula.

The haversine formula can be found on [Wikipedia](https://en.wikipedia.org/wiki/Haversine_formula)

The Haversine Distance is implemented as a function as a class would be kind of overkill.

`haversineDinstance(la1: Double, lo1: Double, la2: Double, lo2: Double, radius: Double = 6367444.7) -> Double`

- `la1` is the latitude of point 1 in degrees.
- `lo1` is the longitude of point 1 in degrees.
- `la2` is the latitude of point 2 in degrees.
- `lo2` is the longitude of point 2 in degrees.
- `radius` is the radius of the sphere considered in meters, which defaults to the mean radius of the earth (from [WolframAlpha](http://www.wolframalpha.com/input/?i=earth+radius)).

The function contains 3 closures in order to make the code more readable and comparable to the Haversine formula given by the Wikipedia page mentioned above.

1. `haversine` implements the haversine, a trigonometric function.
2. `ahaversine` the inverse function of the haversine.
3. `dToR` a closure converting degrees to radians.

The result of `haversineDistance` is returned in meters.
-->

計算求面上的兩點間距離使用經度和緯度和 haversine 公式.

[haversine 公式](https://en.wikipedia.org/wiki/Haversine_formula)

用類別函數實作 Haversine Distance 是一種過度方法.

`haversineDinstance(la1: Double, lo1: Double, la2: Double, lo2: Double, radius: Double = 6367444.7) -> Double`

- `la1` 是點 1 的緯度.
- `lo1` 是點 1 的經度.
- `la2` 是點 2 的緯度.
- `lo2` 試點 2 的經度.
- `radius` 是球體的半徑(公尺), 預設是地球的半徑 ([WolframAlpha](http://www.wolframalpha.com/input/?i=earth+radius)).

函式中包含了 3 個閉包是為了讓程式碼可讀性更高及更容易和 Haversine 公式做對照.

1. `haversine` 實作了 haversine 三角公式.
2. `ahaversine` 是 haversine 的反函式.
3. `dToR` 閉包是用來轉換角度與徑度.

`haversinseDistance` 回傳的單位是公尺

*Written for Swift Algorithm Club by Jaap Wijnen.*
