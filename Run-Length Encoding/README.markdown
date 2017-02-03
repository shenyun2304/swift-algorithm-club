# Run-Length Encoding (RLE) (變動長度編碼)

<!--
RLE is probably the simplest way to do compression. Let's say you have data that looks like this:
-->

RLE 可能是壓縮最簡單的方法. 假設我們的資料長這樣 ：

	aaaaabbbcdeeeeeeef...
	
<!--
then RLE encodes it as follows:
-->

RLE 編碼完後變這樣:

	5a3b1c1d7e1f...

<!--
Instead of repeating bytes, you first write how often that byte occurs and then the byte's actual value. So `5a` means `aaaaa`. If the data has a lot of "byte runs", that is lots of repeating bytes, then RLE can save quite a bit of space. It works quite well on images.

There are many different ways you can implement RLE. Here's an extension of `NSData` that does a version of RLE inspired by the old [PCX image file format](https://en.wikipedia.org/wiki/PCX).

The rules are these:

- Each byte run, i.e. when a certain byte value occurs more than once in a row, is compressed using two bytes: the first byte records the number of repetitions, the second records the actual value. The first byte is stored as: `191 + count`. This means encoded byte runs can never be more than 64 bytes long.

- A single byte in the range 0 - 191 is not compressed and is copied without change.

- A single byte in the range 192 - 255 is represented by two bytes: first the byte 192 (meaning a run of 1 byte), followed by the actual value.

Here is the compression code. It returns a new `NSData` object containing the run-length encoded bytes:
-->

相較於重複字元, 先寫入該字元發生次數, 然後再寫入該字元的值. 
所以 `5a` 表示 `aaaaa`. 如果資料有很多連續重複的字元, 那 RLE 可以節省很大一筆空間. 在圖片上也可以.

有很多方法可以實現 RLE. 受到舊 [PCX 圖檔規格](https://en.wikipedia.org/wiki/PCX) 的啟發, 這邊用 `NSData` 的擴充來實作. 

規則如下:

- 每一個在同一列重複發生的字元, 用兩個 byte 來壓縮: 第一個 byte 紀錄重複次數, 第二個 byte 紀錄該字元值. 第一個 byte 儲存格式是 `191 + count`. 這表示要被編碼的 byte 不能超過 64 個.
- 單一 byte 重複次數在 0 - 191 之間, 不進行壓縮, 直接複製原始值.
- 單一 byte 重複次數在 192 - 255 之間 用兩個 byte 來表示: 第一個 byte 是 192 (表示重複 1 個 byte), 後面跟隨著字元 byte 值.

這裡是壓縮的程式碼. 回傳新的 `NSData` 包含變動長度編碼:

```swift
extension NSData {
  public func compressRLE() -> NSData {
    let data = NSMutableData()
    if length > 0 {
      var ptr = UnsafePointer<UInt8>(bytes)
      let end = ptr + length
      
      while ptr < end {                        // 1
        var count = 0
        var byte = ptr.memory
        var next = byte
        
        while next == byte && ptr < end && count < 64 {   // 2
          ptr = ptr.advancedBy(1)
          next = ptr.memory
          count += 1
        }
      
        if count > 1 || byte >= 192 {          // 3
          var size = 191 + UInt8(count)
          data.appendBytes(&size, length: 1)
          data.appendBytes(&byte, length: 1)
        } else {                               // 4
          data.appendBytes(&byte, length: 1)
        }
      }
    }
    return data
  }
}
```

<!--
How it works:

1. We use an `UnsafePointer` to step through the bytes of the original `NSData` object.

2. At this point we've read the current byte value into the `byte` variable. If the next byte is the same, then we keep reading until we find a byte value that is different, or we reach the end of the data. We also stop if the run is 64 bytes because that's the maximum we can encode.

3. Here, we have to decide how to encode the bytes we just read. The first possibility is that we've read a run of 2 or more bytes (up to 64). In that case we write out two bytes: the length of the run followed by the byte value. But it's also possible we've read a single byte with a value >= 192. That will also be encoded with two bytes.

4. The third possibility is that we've read a single byte < 192. That simply gets copied to the output verbatim.

You can test it like this in a playground:
-->

運作流程:

1. 使用 `UnsafePointer` 走訪原本 `NSData` 物件的每個 byte.
2. 這個時候讀取當前的 byte 值到 `byte` 變數裡. 如果下一個 byte 是相同的, 就持續讀取值到不同的 byte 值, 或者到達資料結尾. 或者在讀取 64 個 byte 後也停止, 因為那是我們可以壓縮的最大值.
3. 這裡我們決定如何將剛剛讀取的資料編碼. 第一種可能是讀取超過 2 或更多 byte (最多到 64 個). 這種情況我們就寫入兩個 byte: 發生次數和字元 byte 值. 但也有可能我們讀取到一個 byte 但是其值 >= 192. 這種情況也寫入兩個 byte.
4. 第三種可能是讀取到單一 byte < 192. 那樣了話就直接寫入該字元 byte 值.

你可以在 playground 中測試看看:

```swift
let originalString = "aaaaabbbcdeeeeeeef"
let utf8 = originalString.dataUsingEncoding(NSUTF8StringEncoding)!
let compressed = utf8.compressRLE()
```

<!--
The compressed `NSData` object should be `<c461c262 6364c665 66>`. Let's decode that by hand to see what has happened:
-->

壓縮後的 `NSData` 物件應該是 `<c461c262 6364c665 66>`. 然後我解開來看發生什麼事:

	c4    This is 196 in decimal. It means the next byte appears 5 times.
	61    The data byte "a".
	c2    The next byte appears 3 times.
	62    The data byte "b".
	63    The data byte "c". Because this is < 192, it's a single data byte.
	64    The data byte "d". Also appears just once.
	c6    The next byte will appear 7 times.
	65    The data byte "e".
	66    The data byte "f". Appears just once.

<!--
So that's 9 bytes encoded versus 18 original. That's a savings of 50%. Of course, this was only a simple test case... If you get unlucky and there are no byte runs at all in your original data, then this method will actually make the encoded data twice as large! So it really depends on the input data.

Here is the decompression code:
-->

所以總共 9 個位元組對上原本的 18 個位元組. 省了 50% 的空間. 當然, 這只是簡單的測試... 如果你運氣不好加上沒有重複的字元, 那這個方法會把資料變的兩倍大! 所以這真的要看輸入資料的狀況.

這邊是解壓縮的程式碼:

```swift
  public func decompressRLE() -> NSData {
    let data = NSMutableData()
    if length > 0 {
      var ptr = UnsafePointer<UInt8>(bytes)
      let end = ptr + length

      while ptr < end {
        var byte = ptr.memory                 // 1
        ptr = ptr.advancedBy(1)
        
        if byte < 192 {                       // 2
          data.appendBytes(&byte, length: 1)

        } else if ptr < end {                 // 3
          var value = ptr.memory
          ptr = ptr.advancedBy(1)

          for _ in 0 ..< byte - 191 {
            data.appendBytes(&value, length: 1)
          }
        }
      }
    }
    return data
  }
```

<!--
1. Again this uses an `UnsafePointer` to read the `NSData`. Here we read the next byte; this is either a single value less than 192, or the start of a byte run.

2. If it's a single value, then it's just a matter of copying it to the output.

3. But if the byte is the start of a run, we have to first read the actual data value and then write it out repeatedly.

To turn the compressed data back into the original, you'd do:
-->

1. 使用 `UnsafePointer` 來讀取 `NSData`. 這邊我們讀取一個 byte; 它有可能小於 192 或開始重複字元.
2. 當重複字元開始時, 先讀取該字元 byte 值 (`value`) 然後重複的寫入該值.

要把壓縮後的資料解壓縮, 可以這樣做:


```swift
let decompressed = compressed.decompressRLE()
let restoredString = String(data: decompressed, encoding: NSUTF8StringEncoding)
```

<!--
And now `originalString == restoredString` must be true!

Footnote: The original PCX implementation is slightly different. There, a byte value of 192 (0xC0) means that the following byte will be repeated 0 times. This also limits the maximum run size to 63 bytes. Because it makes no sense to store bytes that don't occur, in my implementation 192 means the next byte appears once, and the maximum run length is 64 bytes.

This was probably a trade-off when they designed the PCX format way back when. If you look at it in binary, the upper two bits indicate whether a byte is compressed. (If both bits are set then the byte value is 192 or more.) To get the run length you can simply do `byte & 0x3F`, giving you a value in the range 0 to 63. 
-->

現在 `originalString == restoredString` 一定是 true!

備註: 原始的 PCX 實作有點不同. 在 byte 值 192 (0xC0) 的時候表示接下來的 byte 將重複 0 次. 這也將最大重複次數縮小到 63 次. 因為實在沒道理去儲存一個沒發生的 byte, 所以在這邊的實作 192 表示下一個 byte 發生 1 次, 而最大重複次數是 64 次.

這可能是在設計 PCX 規格時的一個交易. 如果詳細看二進位碼, 最高的兩個位元表示該 byte 值是否是被壓縮的. (如果兩個位元都是 1 表示該 byte 值是 192 以上.) 要拿到重複次數你可以簡單的執行 `byte & 0x3F`, 會給你 0 到 63 的值.

*Written for Swift Algorithm Club by Matthijs Hollemans*
