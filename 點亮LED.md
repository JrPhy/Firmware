使用不同的單晶片點亮 LED 的方式都不同，這取決於開發商幫你做掉了多少。

## 1. STM32
需要去設置 GPIO port 的狀態以及頻率 https://www.cnblogs.com/zxr-blog/p/17957466#_lab2_0_0
```C
#include "led.h"
void main(void) {
    GPIO_InitTypeDef GPIO_InitStruct;//结构图定义
    RCC_APB2PeriphClockCmd(LED_G_CLK,ENABLE);//总线2时钟开关
    GPIO_InitStruct.GPIO_Pin = GPIO_Pin_1;//引脚
    GPIO_InitStruct.GPIO_Mode = GPIO_Mode_Out_PP;//模式为推挽输出
    GPIO_InitStruct.GPIO_Speed = GPIO_Speed_50MHz;//设置频率为50HZ
    GPIO_Init(LED_G_PORT,  &GPIO_InitStruct);//GPIO初始化
    while (1) {
        GPIO_SetBits(GPIOB, GPIO_Pin_1);
    }
}
```

## 2. 8051
8051 的每個 port 都是預設高電位，所以只要將連接的 port 拉低電位即可點亮
```C
#include <reg51.h>
void main(void) {
    while (1) {
        P1^1 = 1; // 輸出低電位
    }
}
```
