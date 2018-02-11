## 使用M128上的ADC
本篇會介紹如何使用M128上的ADC功能，分為使用ASA LIB之用法、以及直接讀寫暫存器之用法。

 - - -

### M128 ADC 介紹

#### ADC硬體功能方塊
ASA M128單板電腦使用的AVR Mega128微控制器，以10BIT SAR ADC為核心。參考以下硬體功能方塊圖，除了構成SAR ADC所需的10BIT DAC、比較器Comparator、以及連續逼近邏輯電路SAR Logic以外。也額外提供了確保ADC轉換時期訊號不變的取樣保值電路Sample Hold S/H、切換選擇單極性Single Ended S.E.，雙極性Bipolar Bip. 輸入的多工器Multiplexer  MUX、配合雙極性輸入訊號放大用之差動放大器Differential Amplifier 。
SAR ADC 中10BIT DAC的參考訊號REF，可以經由參考訊號源多工器REF. MUX選擇來自電源，內建2.56V參考電壓，或外部精準參考電壓。雙極/單極共同一個正類比輸入positive，一個負類比輸入 negative 。前者可由正極輸入多工器Positive MUX，選擇ADC0~ADC7 8支腳，外加接地與半導體精準能階參考電壓BandGap Reference共10種不同來源。後者則可由負極輸入多工器選擇 ADC0~ADC3三支腳其中的一支。

圖1 M128 ADC 硬體功能方塊圖

#### ADC 規格
 - 靜態轉換精度規格如下
    - 解析度(Resolution)：10位元10-bit
    - 積分非線性誤差 (Integral Non-linearity)：0.5 LSB
    - 絕對精度 (Absolute Accuracy)：±2 LSB
    - 動態範圍ADC Input Voltage Range：0 - VCC
    - 內建精準參考電壓Selectable ADC Reference Voltage：2.56 V
 - 動態響應規格
    - 轉換時間 Conversion Time：13 - 260 μs
    - 最高精度轉換速率 kSPS at Maximum Resolution：Up to 15 kSPS
 - 多工器通道數規格
    - 單極輸入多工通道Multiplexed  Single Ended Input Channels：8
    - 雙極輸入多工通道 Multiplexed  Differential Input Channels：7
 - 差動放大器規格
    - 雙差動放大通道2 Differential Input Channels ：可選擇增益 Optional Gain 10倍 或200倍。

 - 數位規格：
    - 可選擇向左對齊：10位元資料，分兩位元組讀取時，先低BYTE，後高BYTE，當只需讀取8位元資料時，可以選擇向左對齊。只讀取高BYTE。
    - 可選擇自動轉換或單筆受控轉換：自動轉換模式下，會在轉換完畢自動進行下一筆轉換。單筆受控轉換，則只有軟體透過旗標寫入促成轉換才開始轉換，完成後即等待下次旗標再被寫入。
    - ADC轉換完畢中斷：類比轉換數位完成時，產生中斷促使軟體讀走轉換所得數位資料。
    - 睡眠模式雜訊消除：為避免微控器內部電路運作電訊號干擾ADC轉換，可設定在轉換期間，其他電路進入睡眠模式，以降低干擾。

- - -

### 透過ASALIB使用ADC
ADC驅動函式介面

#### M128_ADC_set
- 呼叫介面：  
  ` char M128_ADC_set(char LSByte,char Mask,char Shift,char Data); `
- 簡介：本函式用於設定ADC所需之設定
    1. ADC輸入訊號來源選擇
    2. 參考電壓來源選擇
    3. 讀取8位元或10位元轉換結果
    4. 選擇設定ADC工作時脈除頻
    5. 禁致能ADC,禁致能ADC中斷
    6. 選擇連續轉換或觸發轉換
    7. ADC轉換觸發：不論連續/非連續ADC均需觸發ADC轉換。
    8. ADC中斷旗標清除：以輪詢ADC中斷旗標方式讀取ADC轉換結果後要呼叫本函式清除中斷旗標。
    9. 設定ADC接腳為輸入
- 參數：
    - LSByte : 詳見M128_ADC_set參數快查表
    - Mask   : 詳見M128_ADC_set參數快查表
    - Shift  : 詳見M128_ADC_set參數快查表
    - Data   : 詳見M128_ADC_set參數快查表
- 回傳：  
    - 0：無誤
    - 1：錯誤：不支援參數所選設定。
    - 2：警告：ADC接腳先前設為輸出，已覆蓋設定為輸入，可能影響其他DIO程式正確性。

##### M128_ADC_set參數快查表
| 功能 | LSByte | Mask | Shift | Data |
| :--  | :--    | :--  | :--   | :--  |
| 輸入訊號來源選擇      | 200 | 0x1F | 0 | 詳情請見ADC輸入訊號來源選擇表 |
| 參考電壓來源選擇      | 200 | 0xC0 | 6 | 外部參考：0 <br> 電壓元接腳：1 <br> 內部2.56V：3 |
| 8/10位元轉換選擇      | 200 | 0x20 | 5 | 10位元：0 <br> 8位元：1 |
| 選擇工作時脈除頻      | 201 | 0x07 | 0 | Data = 1~7，除頻值=$$2^Data$$ 特例:Data=0除頻值=2 <br> ADC工作頻率= $$F_{OSC} / 2^Data$$ |
| 選擇連續轉換或觸發轉換 | 201 | 0x20 | 5 | 連續轉換：0 <br> 觸發轉換：1 |
| 禁致能ADC            | 201 | 0x80 | 7 | 禁能：0 <br> 致能：1 |
| 禁致能ADC中斷         | 201 | 0x08 | 3 | 禁能：0 <br> 致能：1 |
| ADC轉換觸發           | 201 | 0x40 | 6 | 1 |
| ADC中斷旗標清除        | 201 | 0x10 | 4 | 1 |
| 檢查並設定ADC接腳為輸入| 202 | 0x0F | 0 | 0 |

##### ADC輸入訊號來源選擇表  

<table>
    <tr>
        <td colspan="1">  </td>
        <th colspan="2"> 1x </th>
        <th colspan="2"> 10x </th>
        <th colspan="2"> 200x </th>
    </tr>
    <tr>
        <th> $$ V^+ $$ </th>
        <th> $$ V^- $$ </th>
        <th> Data      </th>
        <th> $$ V^- $$ </th>
        <th> Data      </th>
        <th> $$ V^- $$ </th>
        <th> Data      </th>
    </tr>
    <!-- ADC0 -->
    <tr>
        <th rowspan="3"> ADC0 </th>
        <td> Single Ended </td>
        <td> 0x00 </td>
        <td rowspan="3"> ADC0(校正用) </th>
        <td rowspan="3"> 0x80 </td>
        <td rowspan="3"> ADC0(校正用) </th>
        <td rowspan="3"> 0x0A </td>
    </tr>
    <tr>
        <td> ADC1 </td>
        <td> 0x10 </td>
    </tr>
    <tr>
        <td> ADC2 </td>
        <td> 0x18 </td>
    </tr>
    <!-- ADC1 -->
    <tr>
        <th rowspan="3"> ADC1 </th>
        <td> Single Ended </td>
        <td> 0x01 </td>
        <td rowspan="3"> ADC0 </th>
        <td rowspan="3"> 0x09 </td>
        <td rowspan="3"> ADC0 </th>
        <td rowspan="3"> 0x0B </td>
    </tr>
    <tr>
        <td> ADC1(校正用) </td>
        <td> 0x11 </td>
    </tr>
    <tr>
        <td> ADC2 </td>
        <td> 0x19 </td>
    </tr>
    <!-- ADC2 -->
    <tr>
        <th rowspan="3"> ADC2 </th>
        <td> Single Ended </td>
        <td> 0x02 </td>
        <td rowspan="3"> ADC2(校正用) </th>
        <td rowspan="3"> 0x0C </td>
        <td rowspan="3"> ADC2(校正用) </th>
        <td rowspan="3"> 0x0E </td>
    </tr>
    <tr>
        <td> ADC1(校正用) </td>
        <td> 0x12 </td>
    </tr>
    <tr>
        <td> ADC2 </td>
        <td> 0x1A </td>
    </tr>
    <!-- ADC3 -->
    <tr>
        <th rowspan="3"> ADC3 </th>
        <td> Single Ended </td>
        <td> 0x03 </td>
        <td rowspan="3"> ADC2 </th>
        <td rowspan="3"> 0x0D </td>
        <td rowspan="3"> ADC2 </th>
        <td rowspan="3"> 0x0F </td>
    </tr>
    <tr>
        <td> ADC1 </td>
        <td> 0x13 </td>
    </tr>
    <tr>
        <td> ADC2 </td>
        <td> 0x1B </td>
    </tr>
    <!-- ADC4 -->
    <tr>
        <th rowspan="3"> ADC4 </th>
        <td> Single Ended </td>
        <td> 0x04 </td>
        <td rowspan="12" colspan="4" > N/A </th>
    </tr>
    <tr>
        <td> ADC1 </td>
        <td> 0x14 </td>
    </tr>
    <tr>
        <td> ADC2 </td>
        <td> 0x1C </td>
    </tr>
    <!-- ADC5 -->
    <tr>
        <th rowspan="3"> ADC5 </th>
        <td> Single Ended </td>
        <td> 0x05 </td>
    </tr>
    <tr>
        <td> ADC1 </td>
        <td> 0x15 </td>
    </tr>
    <tr>
        <td> ADC2 </td>
        <td> 0x1D </td>
    </tr>
    <!-- ADC6 -->
    <tr>
        <th rowspan="2"> ADC6 </th>
        <td> Single Ended </td>
        <td> 0x06 </td>
    </tr>
    <tr>
        <td> ADC1 </td>
        <td> 0x16 </td>
    </tr>
    <!-- ADC7 -->
    <tr>
        <th rowspan="2"> ADC7 </th>
        <td> Single Ended </td>
        <td> 0x07 </td>
    </tr>
    <tr>
        <td> ADC1 </td>
        <td> 0x17 </td>
    </tr>
    <!-- 1.23V (VBG) -->
    <tr>
        <th> 1.23V ($$ V_{BG} $$) </th>
        <td> Single Ended </td>
        <td> 0x1E </td>
    </tr>
    <!-- 0V (GND) -->
    <tr>
        <th> 0V (GND) </th>
        <td> Single Ended </td>
        <td> 0x1F </td>
    </tr>
</table>

#### M128_ADC_get
- 呼叫介面：  
  ` char M128_ADC_get(char LSByte,char Bytes,void *Data_p); `
- 簡介：呼叫本函式可讀取ADC轉換結果，並依據10bit/8BIT設定及單極性/差動的預設，轉換格式。將會把結果轉換成二補數形式。
- 參數：  

|      | LSByte | Bytes | *Data_p 之byte數 |
| :--- | :--    | :--   | :--         |
| 8 bit| 101    | 1     | 1 |
| 10bit| 100    | 1     | 2 |
- 回傳：  
    - 0：無誤
    - 1：錯誤：不支援參數所選設定。
    - 2：警告：ADC 位元組數設定與讀取不吻合。

##### 資料後處理公式：
由ADC讀值計算輸入電壓或壓差。  
單極：$$ V_{in} = \frac{ V_{ref} \times ADC }{ 2^n} $$ 其中n為位元數8或10。  
差動：$$ V_{pos} - V_{neg} = \frac{ V_{ref} \times ADC }{ GAIN \times 2^n} $$  

#### M128_ADC_fgt
- 呼叫介面：  
  ` char M128_ADC_fgt(char LSbyte,char Mask,char Shift,char *Data_p); `
- 簡介：呼叫本函式，可以讀取ADC轉換結束中斷旗標，用於以輪詢ADC中斷旗標方式讀取ADC轉換結果。
- 參數：
    - LSByte：涉及201(ADCSRA)bit4，故LSByte=201
    - Mask  ：涉及201(ADCSRA)bit4，故Mask=b0001 0000=0x10
    - Shift ：涉及201(ADCSRA)bit4，故Shift=4
- 回傳：  
    - 0：無誤
    - 1：錯誤：不支援所提供暫存器參數之選擇功能。


#### M128_ADC_isr()
- 呼叫介面：  
  ` char M128_ADC_isr(char Number, void (*function)(void)); `
- 簡介：ADC中斷服務登錄函式，可將欲在中斷觸發後執行的函式登錄為ADC中斷的ISR函式。
- 參數：
    - Number：中斷編號。本模組只有一個，編號0。故Number=0 。
    - function：愈登記之函式。
- 回傳：  
    - 0：無誤
    - 1：錯誤：不支援所提供暫存器參數之選擇功能。

- - -

### 透過暫存器直接使用ADC

待更新。
