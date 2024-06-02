相較於 SPI，I<sup>2</sup>C 在電路上是將主設備與從設備並聯，而非拉專用的線去與從設備連接，所以 I<sup>2</sup>C 在電路上會比 SPI 簡潔很多。
![image](https://upload.wikimedia.org/wikipedia/commons/thumb/3/3e/I2C.svg/1920px-I2C.svg.png) ([WIKI](https://zh.wikipedia.org/zh-tw/I%C2%B2C)) \
因為是同時與多個訊號連接，所以在發訊號時會先從主設備發送名稱給從設備，對應的從設備收到後會回應。雖然 I<sup>2</sup> 也可以用同一根線收發訊號，但是沒有辦法同時收發，稱為**半雙工**模式，且因為每個設備的時間精度不同，所以是主從設備訂一個頻率，雙方根據此頻率去讀取訊號，稱為**同步**模式。所以在傳輸訊號上就沒有 SPI 快。

## 1. 傳輸協定
I<sup>2</sup>C 電路上一條是時間線另一條是數據線，待命時兩條線都是在高電位。當數據線要開始收發數據時，會先將數據線的電為拉低，接著再根據定義的時間頻率決定發出的訊號是多少，且只有在時間線為高電位時，數據線的電位才有意義。收/發完後會告訴設備通信結束，此時會將數據線拉高。
![image](https://i0.wp.com/www.strongpilab.com/wp-content/uploads/2016/03/i2c_bus_data.png) (https://www.strongpilab.com/i2c-introduction/) \

## 2. 資料格式
首先會先發送 7 bits 給所有設備，並有 1 bit 的資料說明設備是否有回應，0 無 1 有。接著是每 8 bits = 1 byte 發出去，並帶有 1 bit 的資料說明是否成功，0 無 1 有，最後在發送一個停止位代表結束，所以共為 (7+1)+(8+1)*n+1。\
![image](https://edit.wpgdadawant.com/uploads/news_file/blog/2021/4387/tinymce/_______2021-06-15_______6_24_02.png) (https://www.wpgdadatong.com/blog/detail/44387)
