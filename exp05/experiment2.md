## 實驗步驟及程式 - 低通濾波器實作

本段落我們將執行低通濾波器的設計與實作測試，由於低通濾波最常用，而且因為感測位置與電路位置常常需要長導線傳輸訊號感測器輸出的小訊號，所以除了濾波以外也要同時注意到共模雜訊的防治。所以差動訊號濾波也是一個重點。

---

### 7.一階低通濾波器設計實作
先取用$$R=10K,C=0.015uF$$參考前述背景知識介紹之一階低通濾波器電路實作出RC低通濾波器並量測以下特性：
- 正弦波反應(Sine-Wave response)：
  - 量測頻率響應：以峰峰電壓10Volt，波頻為1Hz、 10Hz、 100Hz、 500Hz、 1KHz、5KHz、 10KHz、頻率的正弦波為輸入，量取輸入輸出之波幅及兩者間的，增益，以及相位移
  - 找出截止頻率：以增益0.7071為截止頻率，由前述響應中，增益大於及小於0.7071為起始兩頻率，二分逼近，找出截止頻率，及其相位移。於載頻率往上，量測不同頻率之增益，推算印證20dB頻率下降率。

- 方波反應(Square-Wave response)：以0V到10V，50%責任週期方波，1Hz、10Hz、100Hz、1KHz波為輸入，觀察記錄其輸出波。後波形推算其
  - 時間常數：上升緣上升至$$10(1-e^{-1})V=6.3212V$$所需時間或下降緣下降到$$10(e^{-1})V=3.6788V$$所需時間。
  - 10%誤差穩定時間：上升緣上升至$$10(1-0.1)V=9V$$或下降緣下降到$$10(0.1)V=1V$$所需時間。
  - 上升率slew rate：上升緣斜率每秒上升伏特。

- 自行設計並實作截止頻率2KHz之一階低通濾波器並執行前述弦波響應，與方波響應，並計算出其時域特徵參數，與頻域特徵參數。

### 8.二階低通濾波器設計實作
先取用$$R=10K,C=0.015uF$$參考前述背景知識介紹之Butterworth二階通濾波器電路，選擇$$R_1=1K\Omega$$則$$R_2=5600\Omega$$實作出Butterworth低通濾波器並量測以下特性：
- 正弦波反應(Sine-Wave response)：
  - 量測頻率響應：以峰峰電壓10Volt，波頻為1Hz、 10Hz、 100Hz、 500Hz、 1KHz、 5KHz、10KHz頻率的正弦波為輸入，量取輸入輸出之波幅及兩者間的，增益，以及相位移
  - 找出截止頻率：以增益0.7071為截止頻率，由前述響應中，增益大於及小於0.7071為起始兩頻率，二分逼近，找出截止頻率，及其相位移。於載頻率往上，量測不同頻率之增益，推算印證40dB頻率下降率。

- 方波反應(Square-Wave response)：以0V到10V，50%責任週期方波，1Hz、10Hz、100Hz、1KHz波為輸入，觀察記錄其輸出波。後波形推算其
  - 時間常數：上升緣上升至$$10(1-e^{-1})V=6.3212V$$所需時間或下降緣下降到$$10(e^{-1})V=3.6788V$$所需時間。
  - 10%誤差穩定時間：上升緣上升至$$10(1-0.1)V=9V$$或下降緣下降到$$10(0.1)V=1V$$所需時間。
  - 上升率slew rate：上升緣斜率每秒上升伏特。

- 自行設計並實作截止頻率2KHz之二階Butterworth低通濾波器並執行前述弦波響應，與方波響應，並計算出其時域特徵參數，與頻域特徵參數。


### 9.二階差動濾波器設計實作
參考前述背景知識介紹之二階差動濾波器，先取用$$R=10K,C=0.015uF$$，選擇$$R_1=1K\Omega$$則$$R_2=5600\Omega$$，$$R_3=R_4=1K\Omega$$，實作出二階差動濾波器低通濾波器並量測以下特性：

- 正弦波反應(Sine-Wave response)：同二階濾波器。
- 方波反應(Square-Wave response)：同二階濾波器。
- 自行設計並實作截止頻率2KHz之二階差動低通濾波器並執行前述弦波響應，與方波響應，並計算出其時域特徵參數，與頻域特徵參數。並將電路實作在MABOR BUS洞洞板上。
