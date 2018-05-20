C語言的file(檔案)函數，prototype跟data type的定義都在stdio.h裡，大部分的函數名都是以f開頭。

第十章有說過，生出C語言的UNIX把許多輸出入裝置都抽象化為檔案。鍵盤或螢幕等等資料流動的裝置，也是當作檔案來處理。所以，我們也會把檔案稱作「stream(流)」。

這裡介紹的f*系函數，幾乎都可以用**stdin**,**stdout**,**stderr**等等「標準stream」來指定。

* clearerr: 清除stream的EOF(end-of-file)跟error指示
* fclose: 關閉stream
* feof: 測試EOF (file來檢查終端)
* ferror: 測試stream的error
* fflush: 把stream給flush
* fgetc: 從stream開始讀入下一個字元
* fgetpos: 變更stream的位置
* fgets: 從stream輸入文字
* fopen: 打開stream
* fprintf: 輸出對stream指定的顯示變換
* fputc: 對stream輸出一字元
* fputs: 對stream輸出一行
* fread: 從stream讀入
* fscanf: 從stream輸入
* fseek: 變更stream的位置
* fsetpos: 變更stream的位置
* ftell: 存取stream的檔案位置表示元
* fwrite: 對stream寫入
* getc: 輸入從stream來的一個字元
* putc: 往stream輸出一個字元
* rewind: stream的
* setbuf: stream的buffer設定
* setvbuf: stream的buffer詳細設定

**fgets()**目前已經使用過了，如果把它識別file stream的參數指定成**stdin**的話，就是從鍵盤輸入一行。這一章不會用**stdin**而是實際去開一個檔案。

### 打開檔案 (fopen)

**fopen()**這個函數，可以打開指定名稱的檔案，跟stream結合。

```cpp
FILE *fopen(const char *file path, const char *mode);
```
file path是用來指定file的名稱及位置的，像是"/home/mtaneda/test.txt"或"C:\\Usrs\\mtaneda\\test.txt"等等，根據OS的而有不同的指定方式。

mode，則是拿來指定file是為了做什麼而被開啟，它有如下的模式:

* r: 為了讀出而打開檔案，會從檔案的開頭開始讀取。
* r+: 為了讀出和寫入而打開檔案，會從檔案的開頭開始讀取。
* w: 為了寫入而打開檔案，在檔案已存在的前提下，會把原來的內容覆蓋掉; 如果檔案不存在就創建一個新的。
* w+: 為了讀出和寫入而打開檔案，在檔案已存在的前提下，會把原來的內容覆蓋掉; 如果檔案不存在就創建一個新的。
* a: 為了寫入到檔案的結尾而打開檔案，在檔案已存在的前提下，會從原本檔案的結尾寫入而不覆蓋原來的內容; 如果檔案不存在就創建一個新的。
* a+: 為了讀出和寫入而打開檔案，在檔案已存在的前提下，會從原本檔案的結尾寫入而不覆蓋原來的內容; 如果檔案不存在就創建一個新的。

返回值是指向**FILE**型的指標，所以不需要宣告**FILE**型變數的實體，只要宣告指標就可以了。關於這個**FILE**型，雖然許多實做是以結構體來實現，但是C語言的library並沒有明確的規定它是不是結構體。只要單純記得有一個data type是**FILE**型就可以了。

處理失敗的話會返回**NULL**。這個函數跟**malloc()**很像的是，它也是接受某個指標變數並佔有一塊記憶體，但是它的內部**malloc()**完全不同，所以不能把**fopen()**得到的指標給**free()**掉; 如果不需要再處理檔案的話，就用**fclose()**。

想對在當前目錄(程式所在的目錄)中存在的test.txt寫上新的內容時，應該要像下面這樣寫:

```cpp
FILE *fp;

fp = fopen("test.txt","w");
if(fp == NULL){
	printf("Can't create file.\n");
}
```
讀出檔案的話，應該要像下面這樣寫:
```cpp
FILE *fp;

fp = fopen("test.txt","r");
if(fp == NULL){
	printf("Can't open file.\n");
}
```
想對已存在的檔案增加內容的話，應該要像下面這樣寫:
```cpp
FILE *fp;

fp = fopen("test.txt","a");
if(fp == NULL){
	printf("Can't open file.\n");
}
```
### 關閉file (fclose)

**fclose()**函數可以把指定的stream給flush後關閉。
```cpp
FILE *fclose(FILE *stream);
```
返回值的話，如果正常處理完則是返回0，否則返回NULL。

### 從檔案讀出 (fread)

**fread()**函數可以從stream(file)上讀出資料。

```cpp
size_t fread(void *讀出目標, size_t 讀出單位的大小, size_t 讀出個數, FILE *stream);
```
讀出目標是指標指定的記憶體領域; 讀出單位是指我們要以多少bytes為單位去讀file; 讀出個數就是指定要讀幾個單位。讀出單位還有讀出個數跟**calloc()**中的記憶體的單位還有個數有異曲同工之妙。最後，再用**FILE**的指標變數指定是哪個stream。

因為返回值是讀出的個數，所以在全部讀出的時候，返回值跟參數指定的讀出個數是相同的。這個函數是無法區分到底是錯誤還是好好讀完file，所以想區分的話要跟別的函數**feof()**還有**ferror()**一起使用。

比如說，某檔案被寫入了16個**int**的資料。我們想把他們全部讀出的話，就像下面這樣寫:

```cpp
int array[16];
size_t read_num;

read_num = fread(array, sizeof(int), 16, fp);
if(read_num != 16){
	if(feof(fp)){
    	printf("End of file");
    }
    else if (ferror(fp)){
    	printf("Error\n");
    }
}
```
就像這個例子，我們把參數(16)跟返回值(**read_num**)來做比較確定有無錯誤，這是慣例。請記起來。

### 寫入檔案 (fwrite)

**fwrite()**函數，可以把stream上的資料寫入。

```cpp
size_t fwrite(void *想寫入的內容, size_t 寫入單位的大小, size_t 寫入個數, FILE *stream);
```
想寫入的內容是指標指定的記憶體領域; 讀出單位跟個數跟**fread()**相同; 因為返回值是寫入的個數，所以在全部寫入的時候，返回值跟參數指定的寫入個數是相同的。在發生錯誤的時候會返回0或是比指定的寫入個數還小的數字。

比如說，想向某檔案寫入16個**int**的資料，就像下面這樣寫:
```cpp
int array[16] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16};
size_t write_num;

write_num = fwrite(array, sizeof(int), 16, fp);
if(write_num != 16){
    printf("Error\n");
}
```