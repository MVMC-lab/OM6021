
### M128_HMI_put
- 呼叫介面：  
  ` char M128_HMI_put(char Bytes, char Type, void *Data_p); `
- 簡介：  
  呼叫函式可以將存放於指標Data_p所指記憶體內指定位元組數Bytes的資料透過UART0送向人機。  
  如果整批資料BYTES數超過封包容量上限32BYTES時，可將整批資料分割成每32BYTE一包，分成多次呼叫傳送。利用拆包及重組功能，能讓通訊干擾造成之通訊損失減少。
- 參數：
 - Bytes  : 欲送出資料總位元大小，上限為32。
 - Type   : 欲送出之資料型態編號。
 - Data_p : 第一筆資料位址。  
- 回傳：  
 - 0 : 成功  
 - 1 : 錯誤：BYTES數不合理。  

範例1 - 發送float矩陣：  
``` c
char res;
float f32_data[5] = { 1.5, -1, 0, 1, -2.5 };
// one flat is 4 bytes, here are 5 float
// bytes is 4*5 = 20
res = M128_HMI_put(4*5, 4, f32_data); // send f32  array data
printf("res = %d\n", res);
```
範例2 - 發送uint8矩陣：
``` c
char res;
unsigned char data[5] = { 0, 1, 2, 3, 4 };
char bytes = sizeof(data); // bytes is 1*5 = 5
res = M128_HMI_put(bytes, 8 , data);
printf("res = %d\n", res);
```

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

### M128_HMI_get
- 呼叫介面：  
  ` char M128_HMI_get(char Bytes, void *Data_p); `
- 簡介：  
  呼叫函式可以透過UART0由人機接收指定位元組數Bytes的資料，並存入指標*Data_p所指記憶體內。  
  如果整批資料BYTES數超過封包容量上限32BYTES時，可將整批資料分割成每32BYTE一包，分成多次呼叫傳送。  
- 變數：
 - Bytes  : 欲接收資料總位元大小，上限為32。  
 - Data_p : 第一筆資料位址。  
- 回傳：  
 - 0 : 成功  
 - 1 : 錯誤：BYTES數不合理。  
 - 2 : 錯誤：送收BYTES數不吻合。  


 範例 - 接收float矩陣：
 ``` c
 char res;
 float data[5];
 // one flat is 4 bytes, here are 5 float
 // bytes is 4*5 = 20
 res = M128_HMI_get(4*5, data); // get f32  array data
 printf("res = %d\n", res);
 for (char i = 0; i < 5; i++) {
     printf("%d - %f\n", i, data[i]);
 }
 ```

#### Type 資料型態編號對應表

| 編號 | 資料型態 | 說明 | avr-gcc 中型態 |
| :-- | :-- | :-- | :-- |
|  0  | int8    | 8  bit 整數型態   | int8_t , char |
|  1  | int16   | 16 bit 整數型態   | int16_t , int |
|  2  | int32   | 32 bit 整數型態   | int32_t , long int|
|  3  | int64   | 64 bit 整數型態   | int64_t |
|  4  | uint8   | 8  bit 正整數型態 | int8_t , unsigned char |
|  5  | uint16  | 16 bit 正整數型態 | int16_t , unsigned int |
|  6  | uint32  | 32 bit 正整數型態 | int32_t , unsigned long int |
|  7  | uint64  | 64 bit 正整數型態 | int63_t |
|  8  | float32 | 32 bit 浮點數型態 | float |
|  9  | float64 | 64 bit 浮點數型態 | 無 |

** (NOTE:AVR-GCC中double與float同為單精度浮點數、為32bit) **

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

### M128_HMI_Form_put()
- 呼叫介面：  
  ` char M128_HMI_Form_put(char *FormatString, char Bytes, void *Data_p) `
- 簡介：  
  呼叫函式可以將存放於指標Data_p所指記憶體內，以FormatString指定不同形態及筆數組成之結構內容，透過UART0送向人機。
- 變數：
 - FormatString : 由資料代碼及筆數所組成之字串，其中每一欄位，代表資料型態，由Type 資料型態對應表中的代碼表示，後接英文字母"x"及一個數字代表筆數。各欄間以","隔開。
 - Bytes ：  欲送出資料總位元大小。
 - Data_p ： 資料位址。  
- 回傳：
 - 0 : 成功
 - 1 : 錯誤

範例 - 發送一個結構：
先進行結構定義：
``` c
typedef struct St {
    uint8_t ui8[10];
    int32_t i32[10];
    float   f32[5];
} st_t;
```
再宣告變數，並發送。
``` c
st_t data = {
    {1, 2, 3,  4,  5,  6,   7,   8,   9,   10},
    {2, 4, 8, 16, 32, 64, 128, 256, 512, 1024},
    {1.0, 2.5, 4.0, 5.5, 6.0}
};
char bytes = sizeof(st_t);
M128_HMI_Form_put("ui8x10,i32x10,f32x5", bytes, &data);
```

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

### M128_HMI_Form_get()
- 呼叫介面：  
  ` char M128_HMI_Form_get( char *FormatString, char Bytes, void *Data_p) `
- 簡介：  
  呼叫函式可接收以FormatString指定不同形態及筆數組成之結構。
- 變數：
 - FormatString : 由資料代碼及筆數所組成之字串，其中每一欄位，代表資料型態，由Type 資料型態對應表中的代碼表示，後接英文字母"x"及一個數字代表筆數。各欄間以","隔開。
 - Bytes : 欲接收資料總位元大小。
 - Data_p : 資料位址。  
- 回傳：
 - 0 : 成功
 - 1 : 錯誤  

範例 - 接收一個結構：
先進行結構定義：
``` c
typedef struct St {
    uint8_t ui8[10];
    int32_t i32[10];
    float   f32[5];
} st_t;
```
再宣告變數，並接收。
``` c
st_t data;
char bytes = sizeof(st_t);
M128_HMI_Form_get("ui8x10,i32x10,f32x5", bytes, &data);
```

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
