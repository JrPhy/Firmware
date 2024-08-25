在寫多執行緒或是多進程程式時，會因為 CPU 核心數量與執行緒數量的關係，使得程式會依照優先順序執行。當然也會因為人為操作去調整每個部份的優先順序，例如[上下文交換(CONTEXT SWITCH)](https://github.com/JrPhy/Multiple_Thread/blob/main/%E4%B8%8A%E4%B8%8B%E6%96%87%E4%BA%A4%E6%8F%9B%E8%88%87%E5%8E%9F%E5%AD%90%E6%93%8D%E4%BD%9C.md)時就是發生了中斷。在一般電腦架構下 CPU 與 OS 會做掉需要中斷的事情，所以開發者只須小心是否會有[競爭條件與死鎖發生](https://github.com/JrPhy/Multiple_Thread/blob/main/%E7%AB%B6%E7%88%AD%E6%A2%9D%E4%BB%B6%E8%88%87%E9%8E%96.md)。而在底層就叫需要知道到底發生了什麼事，一般軟體的中斷為軟體中斷，也就是會把當下的情況記錄在 PCB 中，等有空在去取 PCB 中的值繼續執行。除了軟體中斷外，還有外部中斷與內部中斷。

## 1. 外部中斷
在一些較低階的嵌入式硬體等電器，
