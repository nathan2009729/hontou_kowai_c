### 字串的切割(strtok)

最後要介紹的字串函數是**strtok()**，這是從字串切割出token(單字)的函數。所謂token，狹義來說就是構成句子的最小單位，可以想成自然語言的單字。

比如說，除了電子試算表軟體(excel之類的)跟住所錄軟體獨有的資料形式以外，還有一種可以把試算表軟體的儲存格(cell)用逗號分隔表示出來的檔案--CSV(Comma Separated Value)。

CSV檔案的例子

```
01101,"0600000","IKANIKEISAIGANAIBAAI","CHUO-KU SAPPORO-SHI","HOKKAIDO",0,0,0,0,0,0
01101,"0640941","ASAHIGAOKA","CHUO-KU SAPPORO-SHI","HOKKAIDO",0,0,1,0,0,0
01101,"0600041","ODORIHIGASHI","CHUO-KU SAPPORO-SHI","HOKKAIDO",0,0,1,0,0,0
```
(可以從日本郵局網站[下載](http://http://www.post.japanpost.jp/zipcode/download.html))

在被切割處理的場合時，01101或"0600000"被視作token。**strtok()**的函數宣告如下:

```cpp
char *strtok(const char *被切的字串，const char *想切割的字)
```

想切割的字是可以跟**strpbrk()**一樣，可以複數指定。返回值是找到token以後的指標，如果沒找到的話返回**NULL**。比較特別的點，就是在繼續處理被切的字串的情況下，第二次之後會用**NULL**代替，如下。

```cpp
token = strtok(one_string, ","); //第一次
token = strtok(NULL, ","); //第二次之後
```

因為有點難，所以用**strtok()**的函數來執行看看可以讓CSV比較好懂的範例程式。

**source code**
*strtok_test.c*
```cpp
#include <stdio.h>
#include <string.h>

int main(){

	char database[] = "064-0941,ASAHIGAOKA,CHUO-KU SAPPORO-SHI,"
					  "HOKKAIDO\n060-0041,ODORIHIGASHI,CHUO-KU "
					  "SAPPORO-SHI, HOKKAIDO\n060-0042,"
					  "ODORIHISHI(1-19-CHOME) ,CHUO-KU " 
					  "SAPPORO-SHI,HOKKAIDO";
	char *p1, *p2;
	char *token;

	p1 = database;

	do{
		//change '\n' into '\0' to take a line
		//p1 will look it as a string line
		p2 = strchr(p1, '\n');
		if(p2 == NULL){
			//the case that the end of database is not '\n'
			p2 = &database[strlen(database) - 1];
		}
		else{
			*p2 = '\0';
		}
		//show
		printf("%s\n", p1);

		//strtok()
		token = strtok(p1, ",");
		printf("code = %s\n", token);

		//
		token = strtok(NULL, ",");
		printf("addr2 = %s\n", token);
		//
		token = strtok(NULL, ",");
		printf("addr1 = %s\n", token);
		//
		token = strtok(NULL, ",");
		printf("prefecture = %s\n", token);

		printf("\n");
		p1 = p2 + 1;
	}while(*p1 != '\0');

	return 0;
}
```
**執行結果:**
```
064-0941,ASAHIGAOKA,CHUO-KU SAPPORO-SHI,HOKKAIDO
code = 064-0941
addr2 = ASAHIGAOKA
addr1 = CHUO-KU SAPPORO-SHI
prefecture = HOKKAIDO

060-0041,ODORIHIGASHI,CHUO-KU SAPPORO-SHI, HOKKAIDO
code = 060-0041
addr2 = ODORIHIGASHI
addr1 = CHUO-KU SAPPORO-SHI
prefecture =  HOKKAIDO

060-0042,ODORIHISHI(1-19-CHOME) ,CHUO-KU SAPPORO-SHI,HOKKAIDO
code = 060-0042
addr2 = ODORIHISHI(1-19-CHOME) 
addr1 = CHUO-KU SAPPORO-SHI
prefecture = HOKKAIDO

```

**strtok()**函數可以像**strpbrk()**函數一樣指定複數的字，所以如果寫成**token = strtok(NULL, "\n");**之類的就不需要**strchr()**去一行一行切，總之易懂優先。

執行範例程式之後就很明白**strtok()**的動作了吧。這個**strtok()**在讀入設定檔的時候是很有用的。

**恐怖實驗:strtok()函數會破壞原本的字串**

雖然**strtok()**函數很方便，但是是有陷阱的。首先，是會破壞原來的字串。來把範例程式的一些地方改掉吧。

把
```cpp
p1 = database;
do{
```
改成
```cpp
p1 = database;
printf("database = %s\n", database);
do{
```
再把
```cpp
	}while(*p1 != '\0');

	return 0;
```
改成
```cpp
	}while(*p1 != '\0');
	printf("database = %s\n", database);
	return 0;
```
**執行結果:**
```
database = 064-0941,ASAHIGAOKA,CHUO-KU SAPPORO-SHI,HOKKAIDO
060-0041,ODORIHIGASHI,CHUO-KU SAPPORO-SHI, HOKKAIDO
060-0042,ODORIHISHI(1-19-CHOME) ,CHUO-KU SAPPORO-SHI,HOKKAIDO
064-0941,ASAHIGAOKA,CHUO-KU SAPPORO-SHI,HOKKAIDO
code = 064-0941
addr2 = ASAHIGAOKA
addr1 = CHUO-KU SAPPORO-SHI
prefecture = HOKKAIDO

060-0041,ODORIHIGASHI,CHUO-KU SAPPORO-SHI, HOKKAIDO
code = 060-0041
addr2 = ODORIHIGASHI
addr1 = CHUO-KU SAPPORO-SHI
prefecture =  HOKKAIDO

060-0042,ODORIHISHI(1-19-CHOME) ,CHUO-KU SAPPORO-SHI,HOKKAIDO
code = 060-0042
addr2 = ODORIHISHI(1-19-CHOME) 
addr1 = CHUO-KU SAPPORO-SHI
prefecture = HOKKAIDO

database = 064-0941
```
我們在原本的輸出結果的前後加上了輸出原本的**database[]**的內容。可以發現之後的**database[]**只剩下064-0941這樣的字串。這是因為**strtok()**函數自己把**database[]**內容換掉了。具體來說，就像範例程式的**strchr()**一樣，放入了'\0'。

如果要使用**strtok()**函數的話，把必要的字串複製一份到別的陣列會比較好。

**恐怖實驗: 在**strtok()**函數裡使用字串常數的話**

還有一個要注意的點。這次的範例程式，我們把要傳遞給**strtok()**函數的字串宣告成陣列。我們試試把它宣告成對字串常數的指標，就是把
```cpp
char database[] = ...
```
改成
```cpp
char *database = ...
```
**執行結果:**
```
060-0041,ODORIHIGASHI,CHUO-KU SAPPORO-SHI, HOKKAIDO
060-0042,ODORIHISHI(1-19-CHOME) ,CHUO-KU SAPPORO-SHI,HOKKAIDO
程式記憶體區段錯誤 (core dumped)
```
這是因為**strtok()**函數把'\0'給寫進了**char *database**中。但是，**char *database**不是陣列，是放在讀出專用記憶體的字串常數，所以嘗試寫入的話就會引發錯誤。所以，我們只能用陣列宣告的方式。

還有一個要注意的是，**strtok()**函數的內部狀態。我們第一次用時要宣告是對什麼字串做切割，但是第二次以後不用，這是因為**strtok()**函數的內部利用了靜態變數記住現在切到哪裡了。

所以，如果字串要繼續被別人使用的話結果會變得奇怪。而且，利用靜態變數也意味著這函數在多執行緒程式上會有問題。C語言的標準範圍內是沒有平行環境下的同樣功能函數的，但是UNIX系OS有**strtok_r()**可以用。