雖然底層軟體不常更新，但至少開發時會需要一直刷新做調整，而變成產品後也會需要更新，故障維修時也常常直接退回出廠設置，所以需要有個地方可以永久存放記憶體，不會因為斷電而重置，也要得到一定權限才能刷寫這塊記憶體，像一些 Android 手機再刷機就是先去要這個權限，之後再把要刷的 kernel 送進手機去做更動。當然刷機不一定都會成功，有時候可能電量不足被關機，此時至少要能退回到上一版本，不論是在 OTA 或是用 IAP。

## 一、OTA 流程
OTA 是透過網路下載然後再檢查檔案簽章與完整度，以現在較新的做法是會去分成 A/B 兩區，假設現在在跑 A，則 A 為啟動區 (active slot)，那就把新的韌體寫到 B，並標記 B 為待驗區 (pending slot)，完成後就可以去重啟 bootloader 然後檢查，如果 B 可以正常使用那就把 B 標記為運行區並把 A 標成備份或非啟動區 (backup slot)，否則就回到 A。下次就是寫入 A。在一些手機等記憶體較大的裝置通常是下載一個全新的 FW，而在一些 MCU 這種記憶體較少的可能會只下載更新包，不過因為這種程式設計較為複雜，所以現在非常少用，不然就是用 IAP，也就是回購買店家幫你接線升級。

## 二、IAP 流程

而在開機過程中，BOOTLOADER 預設是進入系統的，所以做更新時必須要把 FLAG 設置成更新，在 u-boot 中可以在 ```u-boot/common/main.c``` 加入 flag 去判斷
```C++
void main_loop(void) {
    ...
    if (check_update_flag()) {
        run_command("tftpboot 0x80000000 firmware.img", 0);
        run_command("mmc dev 0", 0);
        run_command("mmc erase 0x1000 0x8000", 0);
        run_command("mmc write 0x80000000 0x1000 0x8000", 0);
        run_command("reset", 0);
    }
    bootdelay_process();
    run_command(getenv("bootcmd"), 0);
    // check for update
}
```
run_command 裡的指令就是 UBOOT SHELL 的指令，也可以去改 setenv + saveenv，這是一般開發的做法，如果是大規模 OTA 則會去直接改動 U‑Boot 指令，在```configs/<board>_defconfig```中加上
```
CONFIG_BOOTCOMMAND="tftpboot 0x80000000 firmware.img; \
                    mmc dev 0; \
                    mmc erase 0x1000 0x8000; \
                    mmc write 0x80000000 0x1000 0x8000; \
                    reset"
```
```
CONFIG_BOOTCOMMAND="..." → 定義 U‑Boot 開機後要自動執行的指令序列。這會成為環境變數 bootcmd 的預設值。
tftpboot 0x80000000 firmware.img; → 從 TFTP server 下載檔案 firmware.img，放到 RAM 位址 0x80000000。
mmc dev 0; → 選擇 eMMC 裝置編號 0，準備操作。
mmc erase 0x1000 0x8000; → 擦除 eMMC 上從位址 0x1000 開始、長度 0x8000 的區塊。這裡代表要清掉舊韌體所在的分區。
mmc write 0x80000000 0x1000 0x8000; → 把剛剛下載到 RAM 的映像檔，寫入到 eMMC 的分區（起始位址 0x1000，大小 0x8000）。
reset → 完成更新後，重啟系統，讓新韌體生效。
```
