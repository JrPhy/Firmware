為一種主從或 server/client 的傳輸協定，主要可以再細分為三種協定 RTU、ASCII、Modbus TCP。一般使用 RS232/485 或是 RJ45 來連界主機與設備。\
1. Modbus RTU 是一種為使用二進位表示法來進行資料的傳遞與交換，也是比較多人使用的，因為方便，也不需要轉換為ASCII
2. Modbus ASCII 則是一種對於人類來說，可讀性較高的協定。
3. Modbus TCP 是藉由乙太網路 TCP/IP 的方式來進行傳遞資料，是在 TCP/IP 上面多包一層。

RTU 與 ASCII 在傳遞資料的結尾皆需要加上 CRC （錯誤檢查機制），而TCP/IP 本身就自帶 CRC。
