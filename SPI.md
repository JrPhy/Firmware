全名為 Serial Peripheral Interface。SPI 常用在 ADC, DAC, SRAM 等，是一種同步，全雙工的通訊協定。走線上比 UART 多了一根時鐘線，用來同步接收端的時間訊號，也因為有了時間訊號，所以傳輸時不再需要傳起始位與停止位，所以傳輸速率會比 UART 與 I<sup>2</sup>C 快，但在結構上就多了時間線。若為一對多則是用 CS 線來選定要發送的設備，且 CS 線只會與唯一一個設備被連接。發送時間訊號的為 Master，接收時間訊號的為 Slave。因為訊號是由 由 Master 傳給 Slave，故 Master 的 Tx 稱為 MOSI(Master Out Slave In)，此時 Slave 的 Rx 端稱為 SDI/SI(Slave (Data) In)，反過來則為 MISO 與 SDO。

| Slave 2 | SPI | Master | SPI | Slave 1 |
| --- | --- | --- | --- | --- |
| CLK | <-- | CLK | --> | CLK |
| SDI | <-- | MOSI | --> | SDI |
| SDO | --> | MISO | <-- | SDO |
|   |  | CS 1 | --> | CS 1 |
| CS 2 | <-- | CS 2 |  | CS 1 |

## 2. 傳輸協定
