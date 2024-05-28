大家都知道在電路訊號中都是使用 01 紀錄資料，從 GPIO 就知道要如何將輸入的訊號化為 0 與 1，主要就是用低電位與高電位。所以就由 GPIO 再進一步訂出通訊協定，通訊協定常用的有 UART、I<sup>2</sup>C、SPI，這篇會著重於 UART 通訊協定

## 1. UART/USART
全名為 Universal Asynchronous Receiver Transmitter，若加上同步的方式，即為 Universal Synchronous Asynchronous Receiver Transmitter，需要多一根線來傳輸時鐘訊號來做同步

| System 1 | UART | System 2 |
| --- | --- | --- |
| Tx | --> | Rx |
| Rx | <-- | Tx |
| CLK | <--> | CLK |

## 2.
