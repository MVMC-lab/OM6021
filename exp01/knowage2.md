## 背景知識 II Matlab
- - -
## Matlab 矩陣實驗室應用軟體
Matlab是一款老牌的分析設計及模擬軟體，由於大部份的動態模擬，數位訊號處理，控制演算，以及五花八門各個領域常用的演算法，絕大多部要電腦化時，都會化成矩陣運算方式。因此矩陣實驗室MATLAB，很早便因為長於矩陣運算了非常廣用。

### M script 腳本語言及Tool-Box工具箱
Matlab 除了提供許多方便的原生函式以外，它也提供了一個M script 腳本程式語言，這個腳本程式語言，具有一般常見程式語言的迴路指令，可以讓使用者依據自己的流程撰寫並控制Matlab照流程執行矩陣運算。腳本語言與一般語言不同在於它不用編譯器Compiler而是使用翻譯器Interpolater, 能夠直接將程式碼翻譯成可執行碼並立即執行。因為使用上能夠馬上反應，很容易除錯，因此在學界非常受歡迎。
更方便的是，它提供了類似可呼叫函式的語法，讓程式開發者，可以編寫腳本語言函式存為.m檔，並於日後由其它腳本函式，或程式片段呼叫使用。有了這個強力而方便的工具，便有不同領域的學者，將研發出來的演算法撰寫成.m檔函式庫，Matlab稱這些為ToolBox工具箱。例如數位訊號處理有DSP toolbox, 控制有Control toolbox。

### Matlab變數形態
Matlab基本變數形態為**雙精度浮點數double**。但是它也可以定義及處理具有其他變數形態的變數。但它只不過是一個腳本語言，沒有像C一樣有一套宣告，定義及初始的語法。
 - #### 如何產生一個特定形態的變數  
 它可以使用形態化函式來形態轉化一個舊變數之後存入一定新變數，使新的變數具有形態化函式的形態，同時這個新變數的值等於舊變數值。  

 例如：  
 ``` matlab
 array = [1,2,3,4,5]
 intArray = int8(array)
 ```
 此段程式即，把原本形態為Double的array矩陣數值，形態轉化為8位元整數int8後存入intArray。
 Matlab支援的形態，以及形態代函式如下表

| 形態   | 說明     |
| :----- | :------------- |
| double | Convert to double precision        |
| single | Convert to single precision        |
| int8   | Convert to 8-bit signed integer    |
| int16  | Convert to 16-bit signed integer   |
| int32  | Convert to 32-bit signed integer   |
| int64  | Convert to 64-bit signed integer   |
| uint8  | Convert to 8-bit unsigned integer  |
| uint16 | Convert to 16-bit unsigned integer |
| uint32 | Convert to 32-bit unsigned integer |
| uint64 | Convert to 64-bit unsigned integer |

另外有一個通用的指定形態轉化函式  
`B = cast(A,newclass)`  
把變數A 的數值，形態轉換為 newclass之後存到新變數B內。  
例如：上一個範例也可用以下程式碼達成相同效果  
`intArray = cast(array,'int8')`

 - #### 以不同形態來編碼同一串二進字串  

 當讀取單板電腦輸出入埠資料時，常會以二進制來處理，8位元常最常被用char或int8來處理。但有些時機，輸出入埠只管是正值，此時又會是unsigned Char或uint8。當一個埠被以int8形態讀進來，在正式運算前希望不動內部二進制內容，但將形態轉換為uint8。這時需要使用只轉形態不變原二進值的函式typecast()。
 例：  
 - 程式一： `typecast(uint8(255), 'int8')`  
 執行結果：
 ```
 ans =
      -1
 ```
 8位元正整數255的二進制碼，即8位元整數-1。  

 - 程式二： `typecast(int16(-1), 'uint16')`  
 執行結果：  
 ```
 ans =
      65535
 ```
16位元整數-1的二進制碼，即為16位元正整數65535。

- #### 取得一個變數的形態
在Matlab操作中,為了確認變數的形態，可以利用變數形態查詢函式。  

 例如：  
 程式：
 ``` matlab
 a = int8(5);         % 定義a為8位元整數，初始為5
 b = cast(a,'uint8'); % 取a值轉化為8位元正整數後存入b變數
 class(b)             % 詢問變數形態
 ```  
 執行結果：
 ```
 ans =
     'uint8'
 ```
 回應是8位元正整數形態

### SIMULINK
搭配Matlab的程式，有一個可以利用功能方塊圖連接方式，建立動態系統模擬模型，並模擬輸入波形，以計算並繪出輸出波形的軟體，稱為SIMULINK。這個軟體，讓訊號及系統可視化，對於研究者講解所開發的系統時非常具有說明性，但在開發程式時，仍以撰寫Matlab腳本語言程式較為直接。  

### Matlab to C 矩陣實驗室腳本語言轉C
Matlab雖然有翻譯器可以直接翻譯並執行.m程式碼，但是直譯器執行的速度仍遠比執行己完成編譯後的可程式碼慢許多。Matlab有C或C++碼產生器，可以將.m檔轉為C語言。轉換完成的程式，可以再經編譯，成為Matlab內可執行檔，副檔名為.mex，與Matlab原生可執行函式是相同的層級。  
若想要將在Matlab上己開發完成的演算法，轉移到其他電腦上執行，只要將其所產生的C放進其它電腦C言語開發IDE編譯即可得到在其他電腦上可執行的檔。
