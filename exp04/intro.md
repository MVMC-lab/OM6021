# 實驗四
## (PWM，橋式驅動電路)

### 實驗目的

本次實驗介面利用ASA單板本身計時計數器具有的PWM模式，輸出PWM電壓訊號，再以類比低通濾波器，將PWM載波頻率濾除，即可得到平滑的電壓類比訊號，實現數位轉換類比DAC的功能。若需要以電流來驅動線圈型致動器時，則需使用橋式驅動電路，來實現驅動輸出足夠功率電流推動致動器。因此本實驗也介紹半橋以及全橋驅動電路。配合前面單元的人機介面，得以建構一個以ASA單板電腦為核心的數位控制系統。以聲音訊號為對向，我們將能夠建立數位放音系統。

### 預習讀物
 1. 網頁維基百科關於C言語
 2.  http://en.wikipedia.org/wiki/C(programming_language)
 3. C語言學習筆記網頁 http://openhome.cc/Gossip/CGossip/
 4. ATmega128(L) Complete.pdf

### 儀器材料
 1. PC電腦
 2. AVRM128硬體實驗板 www.viewmove.com
 3. AVR Studio 4.0 + M128程式下載軟體
 4. Windows 內建附屬應用程式超級終機
