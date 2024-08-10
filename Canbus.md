與 I<sup>2</sup>C 的結構類似[圖一]，CANBUS 也是只用兩條線來達成與其他單晶片做通訊，不過其通訊方式與 I<sup>2</sup>C 不同，是利用電壓差分來傳遞訊號，且通常是兩條線絞在一起[圖二]，即便受到電磁干擾也是兩條線一起干擾，但是對差分值就沒影響，所以對於電磁訊號抗干擾的程度比較高，傳輸距離也比較長，常用於車用裝置，當然現在也不限於車用裝置。\
![img](https://www.macnica.com/apac/galaxy/zh_tw/products-support/technical-articles/controller-area-network/_jcr_content/root/container/container/container/imagepack/image.coreimg.jpeg/1675328997165/image001.jpeg)\
[圖一](https://www.macnica.com/apac/galaxy/zh_tw/products-support/technical-articles/controller-area-network/)\
![img](https://i1.kknews.cc/bEBNTBAOCpkaN5Il7LOfwKonPqwzH9yZGh-Ksf0/0.jpg)\
[圖二](https://kknews.cc/zh-hk/car/vzggao4.html)

## 1. 傳輸協定
CANBUS 會有兩條總線電路，兩邊分別連接電阻。一開始兩條電線沒有電位差，而當其中一個節點發訊號時兩條線就會有電位差，當差值 V<sub>diff</sub> > 閾值 V<sub>thre</sub> 時就為邏輯 1，反之為 0。\
![img](https://wiki.csie.ncku.edu.tw/embedded/CAN_01.png)\
其結構發送出去的訊號是每個節點都可以接收到，為**廣播模式**。有點類似在開視訊會議時，一人在講話所有人都能聽到，所以並沒有**主從的關係**，而 I<sup>2</sup>C 有主從關係。
#### 1. 仲裁機制(優先序)
但是多人在講話時就會大家一起聽到，所以 CANBUS 的節點會給一組序號，如果儀器同時發出訊號，則依靠序號的優先級發送，高低順序為由小到大，所以序號並不會重複。
#### 2. 檢查可否發送
而當發送其他節點還在發送訊號時，有另個節點也想發送訊號，此時就會先檢測目前是否有再發送訊號，如果有就等待，沒有就看是否有其他優先序更高的節點要發送。發送的訊號在每 4 bits 後會放入一個 0 來做檢測，其利用了 & 來做檢測，所以在節點要發訊號前會將訊號拉高為 1，跟 0 做 & 就是 0，所以就無法發送，表示現在總線尚有其他節點正在發送訊號，就會去等待。下圖中在第 5 bit 時 node 1 要去發訊號，但因為總線上還有訊號尚未傳完，所以會被設為監聽。
![img](https://www.icpdas.com/upload/00_web_img/CAN_Bus/can_series_guide_1.jpg)\
[圖三](https://www.icpdas.com/tw/product/guide+Industrial__Communication+Fieldbus__Communication+CAN__Bus)\
所以在 canbus 有以下[口訣](https://www.youtube.com/watch?v=Plbw-nAMa3A)
```
發前先監聽、空閒即發送、邊發邊檢測、衝突時退避
```
