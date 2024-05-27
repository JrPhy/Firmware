GPIO(General Purpose Input/Output)是一種泛用型的輸入輸出裝置，每個晶片會有不同的 GPIO 端口。除了單純的輸入輸出外，還可以組合成常見的通訊協定，如 I2C, SPI, UART...... 等。內部會連接電子元件，如電容、電阻與電晶體來保護整個系統。在 STM32 中分別支援四種輸入與四種輸出模式\
![image](https://wiki.st.com/stm32mcu/nsfr_img_auth.php/thumb/0/04/Package_MCU_blue.png/225px-Package_MCU_blue.png)\

![image](https://github.com/JrPhy/Firmware/blob/main/pci/Basic_structure_of_a_standard_IO_port_bit.png)

## 1. Push-Pull 和 Open-drain
上圖為一標準 I/O 端口，看到下方的 Output driver 虛線框框，裡面有三種狀態，分別是 Push-pull, Open-drain or disable。VDD 為工作電壓，VSS 為接地，中間串接了 P/N MOS。兩個 MOS 會組成四種狀態，O 代表輸出

| O | 高電位 | 低電位 | 高阻抗 | 短路 |
| --- | --- | --- | --- |
| P | 打開 | 關閉 | 關閉 | 打開 |
| N | 關閉 | 打開 | 關閉 | 打開 |

當兩個 MOS 都是打開時，電流會直接從 VDD 到 VSS，也就是直接短路燒毀，所以只剩前面三種狀態。而當 VDD 為開 VSS 為閉，那麼電流往外流出去，稱為 Push，反過來則稱為 Pull。因為這種電流會直接流出去，相較於開漏輸出大，常用來驅動一個電器，且上升沿與下降沿的時間較短。

https://bbs.huaweicloud.com/blogs/406974
