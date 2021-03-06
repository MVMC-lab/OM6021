## 範例程式3
### 以M128與MATLAB送收結構資料
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

可參考專案連結 https://github.com/mickey9910326/OM6021examples
可搭配影片 https://youtu.be/RS2tvxid5B0

### ASA單板電腦端成程式
``` c
/**
 * @file exp1_2.c
 * @author LiYu
 * @date 2017.11.26
 * @brief Example of sending struct data by using HMI function.
 *
 * Sending struct data by using HMI function From M128. Can catch
 * the struct data by use ASA_HMI_Data_Agent or Matlab. Matlab
 * example is "exp1_2.m" in "matlab" folder.
 */

#include "ASA_Lib.h"
#include <string.h>

#define HMI_TYPE_I8   0
#define HMI_TYPE_I16  1
#define HMI_TYPE_I32  2
#define HMI_TYPE_I64  3
#define HMI_TYPE_UI8  4
#define HMI_TYPE_UI16 5
#define HMI_TYPE_UI32 6
#define HMI_TYPE_UI64 7
#define HMI_TYPE_F32  8
#define HMI_TYPE_F64  9

typedef struct St {
    uint8_t ui8[10];
    int32_t i32[10];
    float   f32[5];
} st_t;

int main() {
    ASA_M128_set();
    char s[20];

    st_t data = {
        {1, 2, 3,  4,  5,  6,   7,   8,   9,   10},
        {2, 4, 8, 16, 32, 64, 128, 256, 512, 1024},
        {1.0, 2.5, 4.0, 5.5, 6.0}
    };
    char bytes = sizeof(st_t);

    printf("M128 Start -------------!\n");
    printf("Plz say something to M128\n");
    scanf("%s", s);

    M128_HMI_Form_put("ui8x10,i32x10,f32x5", bytes, &data);

    M128_HMI_Form_get("ui8x10,i32x10,f32x5", bytes, &data);
    data.ui8[0] += 1;
    data.i32[0] += 1;
    data.f32[0] += 1.0;

    M128_HMI_Form_put("ui8x10,i32x10,f32x5", bytes, &data);

    return 0;
}

```

### MATLAB端程式
``` matlab
%%
% @file exp1_2.m
% @author LiYu
% @date 2017.11.26
% @brief Example of getting array data by using HMI function.
%
% Can run with "exp1_2.c".

%%
% 清除之前變數
clc; clear;

% 開啟串列埠
port_num = 8;
port = REMO_open(port_num);

% 取得字串
s1 = fscanf(port,'%s');
s2 = fscanf(port,'%s');

% 發送字串
fprintf(port, 'Hello');
res = fscanf(port,'%s');
% ASA_M128 會把 scanf 接收到的字串重新發送一遍，故此會接收到 'Hello'

% 接收結構資料
Bytes = 1*10 + 4*10 + 4*5;
[data,FormatString,error] = REMO_FORM_get(port,Bytes);
data

% 改變資料
data.type1(1) = data.type1(1) + 1;

% 發送結構資料
REMO_FORM_put(port,FormatString,Bytes,data);

% 接收結構資料
[data_re,FormatString,error] = REMO_FORM_get(port,Bytes);
data_re

% 關閉串列埠
REMO_close(port);

```
