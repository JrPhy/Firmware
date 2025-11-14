## 一、MCU/SOC/PC 的開機
當按下硬體的電源鍵後會並不會進入系統，而是會先初始化一些設備，開機載入程式 (Bootloader) 通常放在非揮發性記憶體的固定起始位址，隨著不同的 MCU 或 SOC 而有不同，例如 ARM 為0x000000004， MIPS為0xBFC00000，而 ARM Cortex M3 則根據 0x00000004，可以在各廠家的使用手冊上找到，這樣 CPU 在上電或重置時就能直接執行 Bootloader 的初始化程式。當然起始位置會。Bootloader 會去初始化一些外設設備，如時鐘系統 (Clock)、GPIO、UART、SPI、I2C 等必要接口，甚至還有 WIFI 跟 BLE，就可以拿來做更新。也會去建立記憶體映射表，也就是虛擬位置到實際地址的表，再去跳轉到主程式的部分。而在 PC 上則是會先進到 BIOS/UEFI，裡面會有許多 Bootloader 裝在 MBR 上，且會有預設的 OS，也可安裝多個 OS 讓使用者選擇。所以 Bootloader 其實是一個非常小的程式，放在開機後記憶體的起始位置，初始化一些設備並建立記憶體映射表後再跳轉進到主程式。

## 二、Bootloader
在 MCU 或 SOC 開機後會直接進到 bootloader，現在較常見的有 U-boot 與 Redboot，主要會分成兩個部分，在 [U-Boot](https://github.com/u-boot/u-boot) 裡，```u-boot.lds(arch/arm/cpu/u-boot.lds)``` 是一個鏈接腳本 (Linker Script)，它的作用是告訴編譯器/鏈接器程式的各個段 (section) 要放到記憶體的哪個位置，不同架構的 MCU/SOC 都有自己的 u-boot.lds。

#### 1. _start
從 u-boot.lds 的程式碼可以知道 ```ENTRY(_start)``` 即為 UBoot 起始點，就可以到同目錄底下的任一資料夾裡的 ```start.S``` 去看，不過共同的邏輯是***設定堆疊、關閉中斷、清除 BSS、跳到 _main***
