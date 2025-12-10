雖然底層軟體不常更新，但至少開發時會需要一直刷新做調整，而變成產品後也會需要更新，故障維修時也常常直接退回出廠設置，所以需要有個地方可以永久存放記憶體，不會因為斷電而重置，也要得到一定權限才能刷寫這塊記憶體，像一些 Android 手機再刷機就是先去要這個權限，之後再把要刷的 kernel 送進手機去做更動。當然刷機不一定都會成功，有時候可能電量不足被關機，此時至少要能退回到上一版本，不論是在程式裡面或是用 IAP。

而在開機過程中，BOOTLOADER 預設是進入系統的，所以做更新時必須要把 FLAG 設置成更新，在 u-boot 中可以在 ```u-boot/common/main.c``` 加入 flag 去判斷
```C++
void main_loop(void) {
    ...
    bootdelay_process();
    // check for update
    if (check_update_flag()) {
        printf("Update requested, entering OTA...\n");
        do_ota_update();
    } else {
        printf("Normal boot...\n");
        run_command(getenv("bootcmd"), 0);
    }
}
```

