
### M128_HMI_put
- 呼叫介面：  
  ` char M128_HMI_put(char Bytes, char Type, void *Data_p); `
- 簡介：  
  呼叫函式可以將存放於指標*Data_p所指記憶體內指定位元組數Bytes 的資料透過UART0送向人機。  
  如果整批資料BYTES數超過封包容量上限32BYTES時，可將整批資料分割成每32BYTE一包，分成多次呼叫傳送。利用拆包及重組功能，能讓通訊干擾造成之通訊損失減少。
- 參數：
 - Bytes  : 欲送出資料總位元大小。
 - Type   : 欲送出之資料型態編號。
 - Data_p : 第一筆資料位址。  
- 回傳：
 - 0 : 成功
 - 1 : 錯誤：BYTES數不合理。

### M128_HMI_get
- 呼叫介面：  
  ` char M128_HMI_get(char Bytes, void *Data_p); `
- 簡介：  
  呼叫函式可以透過UART0由人機接收指定位元組數Bytes的資料，並存入指標*Data_p所指記憶體內。  
  如果整批資料BYTES數超過封包容量上限32BYTES時，可將整批資料分割成每32BYTE一包，分成多次呼叫傳送。  
- 變數：
 - Bytes  : 欲送出資料總位元大小。  
 - Type   : 欲送出之資料型態編號。  
 - Data_p : 第一筆資料位址。  
- 回傳：
 - 0 : 成功
 - 1 : 錯誤：BYTES數不合理。
 - 2 : 錯誤：送收BYTES數不吻合。

#### Type 資料型態編號對應表

| 編號 | 資料型態 | 說明 | avr-gcc 中型態 |
| :-- | :-- | :-- | :-- |
|  1  | int8    | 8  bit 整數型態   | int8_t , char |
|  2  | int16   | 16 bit 整數型態   | int16_t , int |
|  3  | int32   | 32 bit 整數型態   | int32_t , long int|
|  4  | int64   | 64 bit 整數型態   | int64_t |
|  5  | uint8   | 8  bit 正整數型態 | int8_t , unsigned char |
|  6  | uint16  | 16 bit 正整數型態 | int16_t , unsigned int |
|  7  | uint32  | 32 bit 正整數型態 | int32_t , unsigned long int |
|  8  | uint64  | 64 bit 正整數型態 | int63_t |
|  9  | float32 | 32 bit 浮點數型態 | float |
| 10  | float64 | 64 bit 浮點數型態 | 無 |


### M128_HMI_Form_put()
- 呼叫介面：  
  ` char M128_HMIForm_put(char *FormatString, char Bytes, void *Data_p) `
- 簡介：  
  呼叫函式可以將存放於指標*Data_p所指記憶體內FormatString指定不同形態及筆數組成之資料結構內容透過UART0送向人機。
- 變數：
 - FormatString : 由資料代碼及筆數所組成之字串，其中每一欄位，代表資料型態，由Type 資料型態對應表中的代碼表示，後接英文字母"x"及一個數字代表筆數。各欄間以","隔開。
 - Bytes ：  欲送出資料總位元大小。
 - Data_p ： 資料位址。  
- 回傳：
 - 0 : 成功
 - 1 : 錯誤

### M128_HMI_Form_get()
- 呼叫介面：  
  ` char M128_HMIForm_get( char *FormatString, char Bytes void *Data_p) `
- 簡介：  
  呼叫函式可以將存放於指標*Data_p所指記憶體內FormatString指定不同形態及筆數組成之資料結構內容透過UART0送向人機。
- 變數：
 - FormatString : 由資料代碼及筆數所組成之字串，其中每一欄位，代表資料型態，由Type 資料型態對應表中的代碼表示，後接英文字母"x"及一個數字代表筆數。各欄間以","隔開。
 - Bytes : 欲送出資料總位元大小。
 - Data_p : 資料位址。  
- 回傳：
 - 0 : 成功
 - 1 : 錯誤  

#### Type 資料型態代碼對應表  


 | 編號 | 資料型態 | 說明 | avr-gcc 中型態 |
 | :--  | :-- | :-- | :-- |
 |  i8  | int8    | 8  bit 整數型態   | int8_t , char |
 |  i16 | int16   | 16 bit 整數型態   | int16_t , int |
 |  i32 | int32   | 32 bit 整數型態   | int32_t , long int|
 |  i64 | int64   | 64 bit 整數型態   | int64_t |
 |  ui8 | uint8   | 8  bit 正整數型態 | int8_t , unsigned char |
 | ui16 | uint16  | 16 bit 正整數型態 | int16_t , unsigned int |
 | ui32 | uint32  | 32 bit 正整數型態 | int32_t , unsigned long int |
 | ui64 | uint64  | 64 bit 正整數型態 | int63_t |
 |  f32 | float32 | 32 bit 浮點數型態 | float |
 |  f64 | float64 | 64 bit 浮點數型態 | 無 |
