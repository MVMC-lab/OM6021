## 背景知識 II ADC介紹

- - -

### 使用ADC、DAC介面卡
以電腦處理類比訊號，所需要的動作是ADC，DAC的轉換。雖然現在ADC、DAC的動作均是以ADC、DAC之IC來完成，但對於不同等級的電腦有不同的支援。對大電腦，工作站一直到個人電腦等可以處理許多不同工作，具有規格化擴充匯流排(Bus)，如VME Bus、AT Bus的電腦，通常可以買得到ADC、DAC介面卡。使用這些電腦及介面卡可以省掉ADC、DAC IC硬體相關的工作，有些介面卡廠商甚至提供配合軟體，使用者做ADC,DAC動作就像使用一般套裝軟體一樣容易，比如說National Instrument的介面卡均可以用Labivew套裝軟體來設定及驅動。然而使用套裝軟體畢竟是隔一層，當要自行發展特殊應用時，雖省掉驅動軟體寫作的工作，卻會受到套裝軟體的限制。如果不使用套裝軟體，則必須要自己寫驅動程式。介面卡廠商對於驅動程式寫作的支援又可分為二層次，較高日階的支援是提供副程式或函式庫以供呼叫，較低階的則提供介面卡功能控制暫存器位址，由使用者自行以輸出入位址讀寫方式控制ADC、DAC卡的硬體動作及設定工作模式。  

至於單晶微電腦或單板電腦，主要使用於小型機械上，要求的是體積小巧，因此不可能使用ADC、DAC介面卡，而必需要在電路板上直接使用ADC，DAC IC。綜合上述的說明，電腦處理類比訊號時依使用者的目的及背景的不同，可以由高階至低階分為以下幾個層次:
 - 使用介面卡及現成應用軟體。
 - 使用介面卡，以呼叫現成驅動程式自行撰寫應用軟體。
 - 使用介面卡，以讀寫介面卡的控制及狀態暫存器方式撰寫驅動程式及應用軟體。
 - 直接使用ADC DAC IC。
以上4種方式愈高階愈不需要專業技術，而愈低階則彈性愈大動作也愈快。

### ADC,DAC卡的選用及規格
以電腦做ADC，DAC 動作時，要考慮的是量測訊號的特性及誤差需求， 依需求擇取符合的規格。在符合規格的介面卡之中再依成本及擴充性的考量選取適當介面卡。  

#### ADC卡的規格及其函義；
 - 解析度 Resolution：以Bits為單位，對b BITs的卡而言其可解析度為其輸入範圍除以$$2^b$$。通常可卡上所使用的ADC IC上編號會標示，對較簡易的ADC卡會以DAC來做ADC此時DAC上也有。
 - 精確度 Accuracy： 也以BITs為單位，理想值等於解析度值，由於介面卡電路上不可能百分之百完美，實際上的精確度至少會比解析度小一個Bit。
 - 轉換時間 Conversion Time：指每一筆資料處理所需之時間，單位通常為$$us$$。ADC IC本身轉換需要時間，而其它輔則的電路也需要時間，對於卡而言轉換時間應指整體時間而不是單指ADC IC轉換時間，此點應要求廠商說明清楚。
 - 通道數 Channel：實驗中常有許多量測點或不同的感應器要使用同一面卡來量測，此時 ADC介面卡可以用多功器以同一顆 ADC IC量不同通道的訊號。  輸入訊號有時是單線輸入有時是差分輸入，這兩種不同的輸入方式可指供的通道數相差一倍。
 - 觸發源 Trig. Source：觸發取樣轉換的訊號來源有三種，一種是以軟體在對輸入位址做讀的動作是所產生的IO Read訊號解發，稱為軟體觸發 (Software Triger)，另外一種是由外部電路指供訊號觸發，稱為外訊觸發 (External Triger)，最後一種是內部計時器觸發(Internal Timer Triger)。
 - 讀取方式 Data Transfer：介面卡將類比訊號轉為數位之後必需再送至電腦才能進行計算及貯存，其轉送方式有三種，一種是電腦以輪詢方式讀取轉換完畢旗標，在旗標指出轉換完畢時去讀取， 第二種是以中斷方式，當ADC卡轉換完畢時會對電腦提出中斷要求，電腦再應要求讀取資料,最後一種是在卡轉換資料累積至一定量時向電腦提出直接記憶存取要求。在電腦讓比匯流排的時機，直接將數位訊號存入記憶體。
 - 增益控制 Gain Cotrol：透過增益控制可以調整卡接受輸入訊號的範圍。控制的方式有的是調Jumper，有的是撥Switch，最新的也可以以軟體設定。
 - 取樣保值 Sample and Hold：如果取樣變動較快的訊號時，由於轉換需要時間，如果在轉換時間中訊號仍在變化會造成轉換資料的不穩定，必須先將類比訊號取樣並在轉換期間內保持此一訊號至轉換完畢。
 - 同步取樣 Simultaneous Sample and Hold： 當介面卡以多功器切換道通方式來取不同的感應器資料，由於每個通道轉換的時間是輪的並非同步，這種不同步有時會產生困擾。如果要令各個通道的訊號同步，必須給每一個通道一個取樣保值電路，而在觸發時同時觸發各取樣電路，而後以多功器輪流轉換各通道。

#### 我們可以依以下步驟決定ADC的各項規格:   
 - 決定ADC卡輸入通道數目及是否差分輸入: 計算必須量測的數目，參考 感測器型式及訊號線設定決定是否差分輸入。
 - 決定ADC卡訊號動態範圍:
    - 預估各量測訊號的變動範圍，選擇適當感測器。
    - 參考感測器規格，及預估訊號變動範圍計算各感測器輸出變動範圍。  
    - 比較感測器變動範圍及 ADC輸入範圍，僅量讓各感測器的輸出範圍接近並小於相應通道輸入變動範圍的90\%，至少要讓比例大到ADC 的精確度能符合容許誤差的要求。
    - 如果項中比較結果有任何通道的訊號變動範圍過小，則必須要使用訊號放大電路。而放大比則以放大至變動範圍 ADC卡的最大變動範圍之90%為佳。
 - 決定ADC卡解析度:
    - 依量測容許誤差及感測器增益計算感測器輸出訊號的最大容許誤差 感測器本身精確度應能符合要求。
    - 對感測器輸出最大容許誤差取以2為底的對數函數。
    - ADC卡的精確度必需要大於上述各通道對數函數值。
    - 如要求最大的解析的通道訊號變動範圍與該通道的變動範圍比不大時,可以以放大該通道訊號的方式來降低解析度要求。
 - 決定ADC轉換時間: 依據量測訊號的頻寬3倍至4倍頻率的倒數，乘以通道數，ADC卡的轉換時間必須小於計算所得值並保留一些裕度。注意: 由於電腦軟體動作及多功器 動作，決定ADC卡轉換時間要保守一點。
 - 是否要取樣保值及同步取樣保值: 取樣保值與否看轉換時間與訊號變動快慢
 - 是否同步取樣則視取樣週期及轉換時間之間的比值。

#### DAC 卡的規格及函義:
 - 解析度： 同ADC卡。
 - 精確度：同ADC卡。
 - 轉換時間：同ADC卡，一般DAC轉換所需時間比ADC卡短。
 - 通道,電流電壓： DAC卡可選擇不同通道為電壓或電流輸出。
 - 資料傳送：通常有輸出位址寫出或直接記憶存取。   

DAC 的BITs數通常相同於同時使用的ADC卡。如果沒有同時使用的ADC卡則由輸出訊號的變動範圍，容許誤差以及放大比率來計算。   

- - -

### 多種不同工作原理的ADC電路  
ADC的基本動作較DAC來得複雜及多樣，我們所知的ADC轉換電路可以有以下幾種不同的工作原理。

#### 直接比較法又稱快閃法 Direct，or Flash
參考圖2 直接比較法，由分壓電 組將參考電壓以最小解析電壓大小等分，再以比較器比較輸入電壓與各等分電壓大小，所得結果會是大於輸入電壓的均為0，小於輸入電壓的均為1，這些比較結果經由XOR邏輯之後僅由相應於輸入電壓大小上下所XOR出來的值為1其它為0，這些值經由邏輯處理可以得到二進位數值。比如說3BIT的轉換器必須要有7個比較器，得到8個數位值經邏輯轉換才能得到3BIT的二進位值。直接法，由於訊號直接比較，速度最快，而且不需要取樣保值電路。但是每一等分即要一個比較器，電路比較複雜成本較高。  

![直接法Direct](./img/know2_img2.png)
圖2 直接法Direct，或稱快閃法Flash

#### 粗微分級法 Subranging
粗微分級的方式，如同機械旋扭的粗調微調一般，將ADC轉換分為粗微不同級，各自以較低BITs數的快閃法來轉換，再將轉換結果合成輸出。參考圖3 以兩個4bits的快閃來做8BITS的ADC，首先訊號以粗調ADC轉換得到MSB的4個BITS，這4BITS以DAC電路轉回類比訊號。輸入訊號減這個值後以微調4BITS做ADC轉換。這粗微BITS合成即為所求。由於粗微兩級中有時會有重疊進位情況，如更詳細討論粗微兩級可能必需要有重疊Bit再以邏輯判斷輸出。這種方式比直接比較法省，比如是BITS的電路快閃法要有的比較器，而等分兩段的粗微分級只要個比較器。由於分斷比較，這種轉換會慢一點，而且如果輸入訊號變動快時可能需要取樣保值。  

![](./img/know2_img3.png)
圖3 粗微分級法Subranging

#### 連續逼近法 Successive Approximations Register SAR
連續逼近法可以說是粗微分級每個快閃僅用一個比較器，而且所有各級的比較器又分時共用。參考圖 4 SAR電路以二分逼近法以DAC送出類比數值與輸入值比較，由粗而細逐次調整數位值直到LSB為止。 由於逐次比較又要邏輯判斷這種方式又更加慢，而且更需要取樣保值電路。這種方式的好處是省掉比較器。  

![連續逼近法Successive Approximations](./img/know2_img4.png)
圖4 連續逼近法Successive Approximations

#### 積分法或稱雙斜率法 Integrating，or Double Slope
積分法與前述方法有很大的差異，它利用計算充電放電時間的長短來從事ADC動作。參考圖5電路以輸入電壓大小積分，也就是對積分器充電,這時輸出端電壓會以輸入電壓大小為斜率上昇,如果將充電時間固定下來，則積分器輸出端最終電壓應正比於輸入電壓大小。 充電完畢之後以固定負電壓放電，於是輸出電壓以固定的斜率下降，輸出電壓下降至零電位所費時間會與最終充電電壓成正比，也就是正比於輸入電壓。 計時器在開始放電時開始計時，在比較器查覺放電至零時終止計時輸出所計時間即為ADC結果。這種方式的ADC，由於轉換速度慢而且對不同的訊號強度轉換時間不同因此比較適合做取樣頻率慢而週期不要求固定的場合，由於使用積分器有濾波的作用， 對於有高頻雜訊大的訊號，而訊號本身為低頻的場合有不錯的效果。圖6為雙斜率ADC波形的積分電壓波形，以固定時間用輸入電壓為充電積分斜率，其最終充電電壓高度正比於輸入電壓。以固定的負參考電壓斜率放電積分，其放電時間正比於充電電壓，即正比於輸入電壓。

![積分法/雙斜率法ADC](./img/know2_img5.png)
圖5 積分法/雙斜率法ADC

![積分法/雙斜率法波形](./img/know2_img6.png)
圖6 積分法/雙斜率法波形

#### 超高取樣法 Oversampling，or Sigma Delta
超高取樣法，結合了數位訊號處理的技巧，能做非常高解析度的ADC動作。參考圖7輸入電壓與前一次取樣單BIT ADC,DAC相減，以所得值決定積分器輸出應下降或上 昇，也決定上昇下降的快慢，積分器的輸出比地高時，單BIT的ADC結果為1，否則為0，這結果同時經由DAC被反饋至前面與輸入值相減。這個結構所得出的單BIT ADC將會是在0與1之間漂動的值，而0，或 1的多與少，端視輸入電壓是較接近於上限或是下限刻度。這些單BIT的序列經由數位濾波器之後以頻率的降低換取解析度的提高便是最終轉換的結果。由於取樣頻率很高，這種方式的轉換不必使用取樣保值電路，又由於轉換解析度不高，僅1BIT，這種電路的製作良率高成本低。但由於轉換上是以高頻轉低頻，所以實際取樣頻率比操作取樣頻率小 ，限制了實際應用在聲頻範圍以內。圖8為超高取樣法ADC的電壓波形，其電壓基本上在0電壓上下漂動，每一個時脈，會因電壓高於或低於0而決定充放電斜率，充電斜率為$$ V_in + V_ref $$、放電斜率為$$ V_in - V_ref $$。放電時1Bit ADC為1，充電時1 Bit ADC值為0。輸入電壓愈高充電斜率愈陡0的數目愈少，放電斜率愈平，1的數目愈多。最終出現1之機率正比於輸入正電壓值。

![超高取樣法/ΣΔ法](./img/know2_img7.png)
圖7 超高取樣法/ΣΔ法


![超高取樣法ADC的電壓波形](./img/know2_img8.png)
圖8 超高取樣法ADC的電壓波形

### 資料擷取系統Data acquisition System
資料擷取系統包含擷取類比資料數位化進入電腦的記錄系統，以及將電腦資料送出類比化以驅動致動器的播放系統。

資料記錄器
圖3-8為資料記錄系統的功能方塊圖。感測器感測到的訊號通常極小，需要愈早放大愈好，因此會有一個前置放大器，在接近感測器的地方放大這個訊號，以提高其後訊號傳輸時的訊號雜訊比。到達ADC之前，為避免高頻雜訊在取樣中被疊影映到低頻與訊號混合，需要以反疊影濾波器，即低通濾波器濾除高頻雜訊之後再執行ADC轉換為數位訊號。由微控制器讀取，並直接傳回PC人機或累積暫存在記憶中，等積存足夠筆數後，再由PC人機讀回PC，執行資料的存檔，繪圖或後處理。若取樣的頻率高過於通訊速率時，則我們需要額外提供一個可以暫存大量資料的記憶卡SDC，把取樣資料存檔，並於事後傳給PC人機。

![資料記錄系統功能方塊圖](./img/know2_img9.png)
圖9 資料記錄系統功能方塊圖

ASA-單板電腦用的ATMega128微控器內部包含有ADC等資料擷取電路。此外本課程也提供了簡單的前處理電路，可組成以下簡易資料記錄系統。

![簡易資料記錄系統功能方塊圖](./img/know2_img10.png)
圖10 簡易資料記錄系統功能方塊圖