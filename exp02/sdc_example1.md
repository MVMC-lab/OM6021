## SDC00 範例程式1
### 範例：藉由檔名寫入指定檔案的方法(覆寫模式)
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

可參考專案連結 https://github.com/mickey9910326/OM6021examples

### ASA單板電腦端 程式碼
``` c
/**
 * @file exp2_sdc_1.c
 * @author Cheng-Wei
 * @date 2018.03.05
 * @brief Example of open file with overlap mode, and write data
 *
 */
 void main(void) {

 	ASA_M128_set();

 	/* 建立set函數所需之參數變數 */
 	unsigned char ASA_ID = 4, Mask = 0xFF, Shift = 0, Setting = 0xFF;

 	// 宣告 與SDC00交換資料的資料陣列緩衝區
 	uint8_t swap_buffer[64];
 	// 初始化資料陣列緩衝區記憶體
 	memset(swap_buffer, 0, sizeof(swap_buffer));

 	char check = 0; // 模組通訊狀態旗號

 	// 以覆寫模式建立檔案，檔案名為 "testa.txt”

 	/*** 開啟檔案 ***/
 	// 設定檔案名稱
 	ASA_SDC00_put(ASA_ID, 64, 8, "testa");
 	// 設定檔案副檔名
 	ASA_SDC00_put(ASA_ID, 72, 3, "txt");

 	// 使用覆寫模式開檔，送出0x05至LSbyte 200
 	Setting = 0x05;
 	// 寫入旗標組合
 	check = ASA_SDC00_set(ASA_ID, 200, Mask, Shift, Setting);
 	if (check != 0) {  // 檢查回傳值做後續處理
 		// 開檔失敗，回傳錯誤資訊(錯誤碼請查閱規格書)
 		printf("Debug point 1, error code <%d>\n", check);
 		return;
 	}
 	/*** 開啟檔案完成 ***/


 	printf("[Start] Write data to file with overlap(or new file) mode !!\n");
 	for (int i = 1; i<50; i++) {
 	// 寫入旗標組合
 		sprintf(swap_buffer, "There is a string from M128 %d\r\n", i);

 		// 計算swap_buffer[]中的字串大小
 		int sizeof_string = 64;
 		for (int i = 0; i<sizeof(swap_buffer); i++) {
 			if (swap_buffer[i] == '\0') {
 				sizeof_string = i;
 				break;
 			}
 		}

 		// 寫入資料至指定檔案
 		ASA_SDC00_put(ASA_ID, 0, sizeof_string, swap_buffer);
 	}
 	printf("[Finish] Write data to file with overlap mode!!\n");

 	/*** 關閉檔案 ***/
 	check = ASA_SDC00_set(ASA_ID, 200, Mask, Shift, 0x00);
 	if (check != 0) {  // 檢查回傳值做後續處理
 		printf("Debug point 2, error code <%d>\n", check);
 		return;
 	}
 	/*** 關閉檔案完成 ***/

 	return;
 }

```
