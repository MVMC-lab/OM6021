## 實驗步驟/程式
本單元將實際演練：
1. 量測單極性ADC線性誤差：如何在使用ASA電腦ADC擷取訊號前，自我校正偏移誤差，以及增益誤差。
2. 數位錄音：使用ASA電腦ADC擷取感測訊號並回傳到PC，以MATLAB繪出波形，並存檔。

- - -

### 1.量測單極性ADC線性誤差
利用輸入以及輸入量測所得數位結果可以計算出偏移誤差，以及增益誤差。並將數據與PC連線傳送給MATLAB。其實驗步驟如下：  

#### ASA單板電腦端程式
 1. 設定選擇，參考為內部電壓。
 2. 設定選擇，非連續轉換。
 3. 設定10位元ADC量測。
 4. 永久迴路執行ADC校正量測直到詢問是否繼續量測答案為否。
    - 設定選擇，輸入訊號為。
    - 觸發ADC轉換。
    - 延遲足夠轉換時間。
    - 讀取ADC轉換結果，並記憶。
    - 設定選擇，輸入訊號為。
    - 觸發ADC轉換。
    - 延遲足夠轉換時間。
    - 讀取ADC轉換結果並記憶。
    - 向文字人機送出量測結果。
    - 詢問是否繼續量測。

#### PC-MATLAB人機操作步驟
 1. 完成ASA單板電腦與PC電腦通訊連線
 2. 開啟MATLAB
 3. 完成串列埠通訊設定及開啟
 4. 以scanf()監視並等待單板電腦回傳量測值
 5. 由回傳值，計算實質增益。
 6. 以printf()回應是否繼續量測，如是回2。

#### 本段於實驗報告要提供
 1. 含程式註解comment之程式碼。
 2. MATLAB 對話畫面截圖。

 - - -

### 2.數位錄音
本段，撰寫一個主程式，先呼叫驅動函式完成所有硬體之設定後，再透過人機，詢問欲透過ADC擷取資料筆數，然後輪詢等待，計時音頻中斷完成資料擷取暫存於SDC內，之後再透過呼叫人機驅動函式，將暫存於SDC卡內之擷取之資料傳送回MATLAB文字人機。  

除了主程式之外，需要有一個定時觸發ADC轉換的計時中斷服務函式，以及一個ADC轉換完成中斷服務函式，於完成ADC後讀取一筆轉換結果。  

本實驗，預備以聲頻4KHz取樣數據，在正式撰寫程式前，要先以4KHz推算，選擇那一個基本時脈，並計算除頻值，以取得最接近4KHz的中斷。  

#### ASA單板電腦ADC觸發函式
 1. 呼叫ADC 觸發驅動函式，觸發ADC開始轉換

#### ASA單板電腦ADC擷取函式
 1. 呼叫ADC 擷取驅動函式，取得ADC0輸入類比訊號轉換結果。
 2. 轉換結果存入廣域取樣暫存值。
 3. 取樣計數值變數增加1。

#### ASA單板電腦端主程式
 1. 設定ADC選擇，參考為內部電壓。
 2. 設定ADC選擇，非連續轉換。
 3. 設定ADC 量測10位元。
 4. 設定ADC選擇ADC0通道。
 5. 選擇計時器0，選擇使用內部震盪除頻時脈。
 6. 寫入計時器除頻值，使計時器以音頻產生中斷。
 7. 透過人機，詢問欲擷取ADC總筆數。
 8. 令取樣計數值變數 = 0。
 9. 將ADC觸發函式登記為計時中斷服務函式。
 10. 將ADC擷取函式登記為ADC中斷服務函式。
 11. 開啟SDC檔案準備蓋寫。
 12. 致能，ADC，致能ADC中斷，致能計時中斷，
 13. 進入迴路，直到((取樣計數值變數 ==欲擷取ADC總筆數)才關閉ASA_SDC檔案離開迴路
    - 輪詢取樣計數值變數，若數值增1時，將廣域取樣暫存值存入ASA_SDC 己開蓋寫檔案。
 14. 以人機函式，與人機電腦對話，並
    - 開啟ASA_SDC檔案允讀，再分批，由包傳回所有擷取之資料

#### 實驗配線PC-MATLAB人機操作步驟
 1. 完成ADC感測訊號到ASA單板電腦配線。
    - 第一次的感測訊號，來自產波器，量取產波器產生的0到5V三角波訊號，接入ADC0。
    - 第二次的感測訊號，來自電容麥克風經由訊號前處理電路處理放大後接入ADC0。
 2. 完成ASA_SDC配線。
 3. 完成ASA單板電腦與PC電腦通訊連線
 4. 開啟MATLAB
 5. 完成串列埠通訊設定及開啟
 6. 以對話框與ASA單板對話，並取回ADC量測數據。
 7. 分別以，理論量測電壓公式，以及實質量測電壓公式，由量測所得碼Code推算出量測電壓，並繪圖。
 8. 將量測數據存到PC檔案中。

註：
雖然理論上ADC 量測電壓值可以由全域電壓 $$V_{FS}$$ 及ADC結果碼透過  
 **理論量測電壓公式** $$ V(Code) = V_{FS} \frac{ Code }{ 2^b } $$ 求得。  

但若要更準確，則必需要經過線性誤差補償來提高精度。利用0點量測值 $$ Code_{0V} $$ 以及實質增益 $$ GAIN = \frac{V_{bg}}{ Code_{V_{bg}} - Code_{0V} } $$ 可以推算出，  
**實質量測電壓公式** $$ V(Code) = ( Code_{V_{bg}} - Code_{0V} ) \times GAIN $$    

#### 本段於實驗報告要提供
 1. 含程式註解comment之程式碼。
 2. MATLAB 對話畫面截圖。
 3. 數據繪圖。