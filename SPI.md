全名為 Serial Peripheral Interface。SPI 常用在 ADC, DAC, SRAM 等，是一種同步，全雙工的通訊協定。走線上比 UART 多了一根時鐘線，用來同步接收端的時間訊號，也因為有了時間訊號，所以傳輸時不再需要傳起始位與停止位，所以傳輸速率會比 UART 與 I<sup>2</sup>C 快，但在結構上就多了時間線。若為一對多則是用 CS 線來選定要發送的設備，且 CS 線只會與唯一一個設備被連接。發送時間訊號的為 Master，接收時間訊號的為 Slave。因為訊號是由 由 Master 傳給 Slave，故 Master 的 Tx 稱為 MOSI(Master Out Slave In)，此時 Slave 的 Rx 端稱為 SDI/SI(Slave (Data) In)，反過來則為 MISO 與 SDO。

| Slave 2 | SPI | Master | SPI | Slave 1 |
| --- | --- | --- | --- | --- |
| CLK | <-- | CLK | --> | CLK |
| SDI | <-- | MOSI | --> | SDI |
| SDO | --> | MISO | <-- | SDO |
|   |  | CS 1 | --> | CS 1 |
| CS 2 | <-- | CS 2 |  | CS 1 |

![https://zh.wikipedia.org/wiki/%E5%BA%8F%E5%88%97%E5%91%A8%E9%82%8A%E4%BB%8B%E9%9D%A2#/media/File:SPI_three_slaves.svg](https://upload.wikimedia.org/wikipedia/commons/f/fc/SPI_three_slaves.svg)

## 1. 傳輸協定
SPI 主要是靠時鐘訊號來做讀取，也可以暫停再開始。要發送資料時，會先將 CS 的電位改變，要拉高還是拉低完全由晶片手冊決定，在此以拉低為例。此時時中線會一直發時鐘訊號，再時鐘線翻轉時，也就是上升沿或下降沿時的讀取，在此以上升沿為例。資料線也會一直發送訊號出去，所以讀取訊號時的電路如下圖
![SPI 讀取電路時的電位](https://github.com/JrPhy/Firmware/blob/main/pic/SPI.jpg) https://en.wikipedia.org/wiki/Serial_Peripheral_Interface \
總共會有四種模式

| MODE | CS | CLK | MOSI | Slave 1 |
| --- | --- | --- | --- | --- |
| 0 | 低電位 | 上升沿 | --> | CLK |
| 1 | 低電位 | 下降沿 | --> | SDI |
| 2 | 高電位 | 上升沿 | <-- | SDO |
| 3 | 高電位 | 下降沿 | --> | CS 1 |

