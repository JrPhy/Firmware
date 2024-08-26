看門狗實際上是一組計時器，主要是避免程式陷入無窮迴圈或無預期的錯誤造成系統死機，所以可以設定一個時間，當超過時間就跳回到某個狀態。又分成視窗看門狗 WWDG 與獨立看門狗 IWDG。

## 1. 獨立看門狗 IWDG
顧名思義是利用獨立於其他計數器的部分來計時，通常會寫在主無窮迴圈中，也就是 main 中的無窮迴圈。
```C
int main(void) {
    //...
    IWDG_Config(IWDG_Prescaler_64 ,625);
    while(1)                            
	{	   
		if( Key_Scan(KEY1_GPIO_PORT,KEY1_PIN) == KEY_ON)
		{
			// ...
			IWDG_Feed();		
			// 喂狗後亮綠燈，若沒餵狗則會在時間到後再跳回上面
			LED_GREEN;
		}
	}
```
