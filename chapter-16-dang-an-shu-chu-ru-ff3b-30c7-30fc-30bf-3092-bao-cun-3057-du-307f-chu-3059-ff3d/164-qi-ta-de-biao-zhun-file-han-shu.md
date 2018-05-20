### 檢查error (feof, ferror)

前節的範例程式，利用到了**feof()**跟**ferror()**函數，這兩個是告訴我們file stream狀態的函數。如果已經讀取到檔案的最尾端，當然無法再繼續讀下去。如果**fread()**讀的數目比指定值還小的話，會返回0; 但是這是讀到尾端了呢，還是發生什麼error了呢? 像這種情況，就會利用到**feof()**函數。

```cpp
int feof(FILE *stream);
```
這個函數會在讀到尾端(End of file = EOF)的時候返回0之外的數值。而是否發生error，需要用**ferror()**函數來決定。

```cpp
int feof(FILE *stream);
```
這個函數會在發生error的時候返回0之外的數值。

如果要把這些狀態清除的話，就需要用到**clearerr()**函數。就算說是把狀態清除，也不會從錯誤中回復，請記住。

### 從檔案讀取一行 (fgets)

**fgets()**函數已經用過了，應該很熟悉吧。

```cpp
char *fgets(char *讀出目標, size_t size, FILE *stream);
```
目前為止stream都是指定**stdin**，只從鍵盤上讀取。如果對stream指定由**fopen()**函數得到的file stream的話，就可以從檔案讀出一行。返回值是讀出目標的指標，error或是一個字元都無法讀出的話就返回**NULL**。因為是很熟悉的函數了，趕快來應用吧。

**source code**
*fgets_test.c*
```cpp
#include <stdio.h>
#include <string.h>

int main(int argc, char *argv[]){

	char buf[8192];
	char *c;
	int line_no;
	FILE *fp;

	if(argc != 2){
		printf("usage: %s [filename]\n", argv[0]);
		return -1;
	}

	fp = fopen(argv[1], "r");
	if(fp == NULL){
		printf("Can't open file\n");
		return -1;
	}

	line_no = 0;
	while((fgets(buf, sizeof(buf), fp)) != NULL){
		c = strchr(buf, '\n');
		if(c != NULL){
			*c = '\0';
		}
		//
		printf("%04d: %s\n", line_no++, buf);
	}

	if(feof(fp)){
		printf("End of file\n");
	}
	else if(ferror(fp)){
		printf("Error\n");
	}

	fclose(fp);

	return 0;
}
```
**執行結果:**
```
$ ./fgets_test 
usage: ./fgets_test [filename]
$ ./fgets_test fgets_test.c
0000: #include <stdio.h>
0001: #include <string.h>
0002: 
0003: int main(int argc, char *argv[]){
0004: 
0005: 	char buf[8192];
0006: 	char *c;
0007: 	int line_no;
0008: 	FILE *fp;
0009: 
0010: 	if(argc != 2){
0011: 		printf("usage: %s [filename]\n", argv[0]);
0012: 		return -1;
0013: 	}
0014: 
0015: 	fp = fopen(argv[1], "r");
0016: 	if(fp == NULL){
0017: 		printf("Can't open file\n");
0018: 		return -1;
0019: 	}
0020: 
0021: 	line_no = 0;
0022: 	while((fgets(buf, sizeof(buf), fp)) != NULL){
0023: 		c = strchr(buf, '\n');
0024: 		if(c != NULL){
0025: 			*c = '\0';
0026: 		}
0027: 		//
0028: 		printf("%04d: %s\n", line_no++, buf);
0029: 	}
0030: 
0031: 	if(feof(fp)){
0032: 		printf("End of file\n");
0033: 	}
0034: 	else if(ferror(fp)){
0035: 		printf("Error\n");
0036: 	}
0037: 
0038: 	fclose(fp);
0039: 
0040: 	return 0;
0041: }
End of file
```
這是一個把文字檔的內容加上行數顯示在螢幕上的程式。之前都沒有說明過，**main()**的**argc**跟**argv[]**變數是在程式起動時，可以介壽使用者輸入的參數。**argc**是代表輸入參數的數量，**argv[]**是字串形式的參數。

**argv[]**在程式啟動時會先被傳入自己本身的程式名稱。所以如果什麼參數都沒指定的話，**argc**是1，**argv[0]**是"./fgets_test.c"。這次的範例程式因為一定要讀出某個檔案，所以**argc**不是2的話，我們就會印出"usage: ./fgets_test [filename]"出來當作是錯誤。

因為**fgets()**函數會讀到換行文字'\n'為止，所以用到了**strchr()**函數。

### 操作stream現在的位置 (ftell, fseek)

目前為止的範例，都是把整個檔案一次讀完。小的檔案還好，但是想一次讀完大的檔案就需要很大的記憶體，會很花時間。而且，也有可能在檔案的中途讀取資料。

**FILE**型的stream，可以好好的記住現在的檔案是在哪個位置，所以可以藉由操作這個位置來讀出寫入或是變更位置。

```cpp
long ftell(FILE *stream);
int fseek(FILE *stream, long 場所, int 哪裡開始);
```
**ftell()**函數可以告訴我們現在stream在哪個場所，返回值是**long**，如果發生error的話就返回-1。

**fseek()**可以變更位置到指定的場所。所謂的"哪裡開始"，我們是指定檔案的開頭叫**SEEK_SET**，現在場所(相對位置)是**SEEK_CUR**，檔案尾端是**SEEK_END**。如果成功的話返回0，否則-1。如果想回去檔案的開頭的話，就寫:

```cpp
fseek(fp, 0, SEEK_SET);
```
想回去檔案尾端的話，
```cpp
fseek(fp, 0, SEEK_END);
```
。
應用這些，我們可以寫出反著讀入fwrite_test.c所輸出的test.data的程式。

**source code**
*file_pos.c*
```cpp
#include <stdio.h>

#define FILE_NAME "test.dat"

int main(){

	int array[16] = {0};
	int i;
	size_t read_num;
	FILE *fp;

	fp = fopen(FILE_NAME, "r");
	if(fp == NULL){
		printf("Can't open file");
		return -1;
	}

	fseek(fp, -sizeof(int), SEEK_END);
	for(i = 0; i < 16; i++){//>
		read_num = fread(&array[i], sizeof(int), 1, fp);
		if(read_num != 1){
			if(feof(fp)){
				printf("End of file\n");
			}
			else if(ferror(fp)){
				printf("Error\n");
			}
			fclose(fp);
			return -1;
		}
		fseek(fp, -(sizeof(int) * 2), SEEK_CUR);
	}

	for(i = 0; i < 16; i++){//>
		printf("%d ", array[i]);
	}
	printf("\n");

	return 0;
}
```
**執行結果:**
```
16 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1
```
因為test.dat全部都是**int**的資料，所以是每次移動**sizeof(int)**，就可以簡單的表示逆順序。但是，文字檔的字串長度不先讀完是不知道的(因為有'\n'跟'\0')，所以表示逆順序很難。

### 附上格式輸出檔案 (fprintf)

標準file函數最後介紹的是**fprintf()**。可以把它想成**printf()**的檔案版本就行。除了第一個參數要指定stream以外，其他都跟**printf()**一樣。趕快來實驗看看吧。

**source code**
*fprintf_test.c*
```cpp
#include <stdio.h>
#include <string.h>

int main(int argc, char *argv[]){

	char buf[8192];
	char *c;
	int line_no;
	FILE *fp_read, *fp_write;

	if(argc != 2){
		printf("usage: %s [filename]\n", argv[0]);
		return -1;
	}

	fp_read = fopen(argv[1], "r");
	if(fp_read == NULL){
		printf("Can't open file\n");
		return -1;
	}

	fp_write = fopen("out.txt", "w");
	if(fp_write == NULL){
		printf("Can't create file\n");
		fclose(fp_read);
		return -1;
	}

	line_no = 0;
	while((fgets(buf, sizeof(buf), fp_read)) != NULL){
		c = strchr(buf, '\n');
		if(c != NULL){
			*c = '\0';
		}
		//
		if(fprintf(fp_write, "%04d: %s\n", line_no++, buf) < 0){
			if(ferror(fp_write)){
				printf("Error\n");
				fclose(fp_write);
				fclose(fp_read);
			}
		}
	}

	if(feof(fp_read)){
		printf("End of file\n");
	}
	else if(ferror(fp_read)){
		printf("Error\n");
	}

	fclose(fp_write);
	fclose(fp_read);

	return 0;
}
```
**執行結果:(顯示在out.txt中)**
```
0000: #include <stdio.h>
0001: #include <string.h>
0002: 
0003: int main(int argc, char *argv[]){
0004: 
0005: 	char buf[8192];
0006: 	char *c;
0007: 	int line_no;
0008: 	FILE *fp_read, *fp_write;
0009: 
0010: 	if(argc != 2){
0011: 		printf("usage: %s [filename]\n", argv[0]);
0012: 		return -1;
0013: 	}
0014: 
0015: 	fp_read = fopen(argv[1], "r");
0016: 	if(fp_read == NULL){
0017: 		printf("Can't open file\n");
0018: 		return -1;
0019: 	}
0020: 
0021: 	fp_write = fopen("out.txt", "w");
0022: 	if(fp_write == NULL){
0023: 		printf("Can't create file\n");
0024: 		fclose(fp_read);
0025: 		return -1;
0026: 	}
0027: 
0028: 	line_no = 0;
0029: 	while((fgets(buf, sizeof(buf), fp_read)) != NULL){
0030: 		c = strchr(buf, '\n');
0031: 		if(c != NULL){
0032: 			*c = '\0';
0033: 		}
0034: 		//
0035: 		if(fprintf(fp_write, "%04d: %s\n", line_no++, buf) < 0){
0036: 			if(ferror(fp_write)){
0037: 				printf("Error\n");
0038: 				fclose(fp_write);
0039: 				fclose(fp_read);
0040: 			}
0041: 		}
0042: 	}
0043: 
0044: 	if(feof(fp_read)){
0045: 		printf("End of file\n");
0046: 	}
0047: 	else if(ferror(fp_read)){
0048: 		printf("Error\n");
0049: 	}
0050: 
0051: 	fclose(fp_write);
0052: 	fclose(fp_read);
0053: 
0054: 	return 0;
0055: }
```
//>
好好的輸出了。