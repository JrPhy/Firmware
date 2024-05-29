大家都知道在電路訊號中都是使用 01 紀錄資料，從 GPIO 就知道要如何將輸入的訊號化為 0 與 1，主要就是用低電位與高電位。所以就由 GPIO 再進一步訂出通訊協定，通訊協定常用的有 UART、I<sup>2</sup>C、SPI，這篇會著重於 UART 通訊協定

## 1. UART/USART
全名為 Universal Asynchronous Receiver Transmitter，在兩個系統中分別由 Tx 傳輸線與 Rx 與接收線連接，可以同時接收與發送訊號，若加上同步的方式，即為 Universal Synchronous Asynchronous Receiver Transmitter，需要多一根線來傳輸時鐘訊號來做同步，如 SPI。

| System 1 | UART | System 2 |
| --- | --- | --- |
| Tx | --> | Rx |
| Rx | <-- | Tx |

當然這個的缺點也很明顯，如果有很多儀器要互相傳遞數據，那就需要將每個儀器都接上兩條線，如果是都要回傳到主機上，那主機會需要接很多的線。而且這種傳輸的距離通常很短，沒有時鐘線的話就需要兩台儀器定在某個傳輸速率(baud)，來告訴雙方資料該如何讀取，但這個速率通常很低。根據以上缺點也演化出了不同的通訊協定

1. 傳輸距離不足 --> RS232/RS485 (算是介面標準)
2. 傳輸速率過低 --> 加時鐘線，捨棄起始與停止 SPI
3. 一對多較複雜 --> 加時鐘線與地線，並聯多個裝置 I<sup>2</sup>C

## 2. 傳輸協定
UART 在傳輸前電路為高電為，發送時會先將電位拉低表示要開始發送資料，接下來再將資料發送出去，最後回復高電位表示結束。通常傳輸時會將資料每 8 bit 為一個單位傳輸，也就是剛好 1 byte，之後再根據實際的資料型態去轉型。例如要發送 'A' 給另一設備，會先轉為 8 bit 的二進制 01000001，轉為電訊號即為```|_|‾|_ _ _ _ _|‾```，在包含起始位與停止位即為```|_ _|‾|_ _ _ _ _|‾ ‾```

![image](https://media.geeksforgeeks.org/wp-content/uploads/20220921105947/UARTdataformat-660x170.png)

在傳輸時需要有起始位與停止位，自然速率就不會高。而 SPI 多了個時鐘線做這件事，所以傳輸速率可以高很多。

#### 1. 鮑率(baud)
單位為 bit/s，也就是每秒傳多少個數據，兩個儀器需要**相同的**鮑率在讀取時才不會錯誤。以 RS232 為例典型的「鮑率」是300, 1200, 2400, 9600, 19200, 38400, 115200等。以 9600 為例，每傳輸 1 bit 費時約 104 us，但在實際電路中會因 V, I, R 三者大小不同，使得從低電位到高電位的時間有延遲，所以可以從 52 us 開始去取每位。

#### 2. 奇偶較驗
有時會為了檢查數據正確性，就在原始資料後一位另外做檢查，因為訊號只有 0/1，所以可以檢查 1 的個數，奇數個為 1，偶數個為 0，例如 'A' 的二進制有兩個 1，所以較驗位為 0。當然也有可能兩個資料出錯，但是機率非常的低，所以通常一位的奇數較驗就很夠用了。所以每一次傳輸通常是發送 10 ~ 11 bits，資料是在第 2 ~ 9 個 bit 中，第一個皆為起始位，若無較驗位則為 10 bits，有則為 11 bits。

## 3. 程式碼
在使用 SMT32 時通常會使用他們所提供的 HAL 函示庫，裡面已經實作傳出的相關函數，也會搭配牠們的 CubeMX IDE 來做開發。裡面發送的函數有三個，會在最後面加上 Transmit，也有對應的接收函數 Recieve。

#### 1. HAL_UART_Transmit :輪詢方式，阻塞式發送函數(必須等資料傳送完成後 MCU 才去做其他事情)
HAL_UART_Transmit(UART_HandleTypeDef *huart, const uint8_t *pData, uint16_t Size, uint32_t Timeout)
```
//*huart : 看選擇哪個USART填入對應編號
//*pData : 要傳送的資料Buf指針
//Size : 資料長度
//Timeout : 逾時時間
```
在發送資料時，會把資料每 bit 放在 register 中，然後再由端口發送出去。因為是阻塞式的，會等所有訊息發完或超過逾時時間之後再去做其他事情，對應的阻塞是接收函數為 ```HAL_UART_Recieve(UART_HandleTypeDef *huart, const uint8_t *pData, uint16_t Size, uint32_t Timeout)```

#### 2.HAL_UART_Transmit_IT : 中斷方式，非阻塞發送函數
```
//*huart : 看選擇哪個USART填入對應編號
//*pData : 要傳送的資料Buf指針
//Size : 資料長度
```
將資料放入 register，如果還沒發送出去，那就先去做別的事，等 register 空了後再把其他資料放進 register，就不需要一直等待。對應的阻塞是接收函數為 ```HAL_UART_Recieve_IT(UART_HandleTypeDef *huart, const uint8_t *pData, uint16_t Size, uint32_t Timeout)```。使用非阻塞式發送與接收資料，因為資料還沒全部接收完，所以不能進行分析，所以就需要修改其對應的中段函數。
