### 字串的連接(strcat)

除了字串的複製以外，也常常有連接字串的需求。比如說，我們想為檔案名稱加上副檔名:

```cpp
char filename[256] = "strcat_test";
char ext[] = ".c"
```
我們想在filename[]的後面接上ext[]的字串。在目前為止的學習範圍內，想實現這項功能的話，就是先利用迴圈知道filename的'\0'在哪裡，再從那裡把ext[]的內容複寫上去。寫成程式的話就像下面這樣:

```cpp
char filename[256] = "strcat_test";
char ext[] = ".c"
char *ptr_1 = filename;
char *ptr_2 = ext;

while(*ptr_1 != '\0'){
	ptr_1++;
}

while(*ptr_2 != '\0'){
	*ptr_1 = *ptr_2;
    *ptr_1++;
    *ptr_2++;
}

*ptr_1 = '\0';
```
但是，每次都要寫這東西太麻煩了，所以有了這處理的標準函數，就是STRingcinCATenate的略稱，**strcat()**函數。宣告如下:

```cpp
char *strcat(char *連結目標(destination), char *連結對象(source));
```
返回值是destination的指標。跟**strcpy()**一樣，它的prototype是宣告在string.h，所以用到的時候也要include。連結目標跟連結對象比較難懂，來用上面的例子執行範例程式確認一下吧。

**source code**
*strcat_test.c*
```cpp
#include <stdio.h>
#include <string.h>

int main(){

	char filename[256] = "strcat_test";
	char ext[] = ".c";

	printf("filename is = %s\n", filename);

	strcat(filename, ext);

	printf("filename is = %s\n", filename);

	return 0;
}
```
**執行結果:**
```
filename is = strcat_test
filename is = strcat_test.c
```
這樣可以理解**strcat()**函數的動作了嗎? 這裡也有跟**strcpy()**一樣的**NULL**跟非字串傳入參數，會引起異常中止的問題。而且因為是往陣列追加字，所以超過原本大小的可能性比**strcpy()**更高。所以跟**strcpy()**一樣有一個指定大小的**strncat()**函數。

### 有長度限制的字串連結

可以指定連結目標的大小的**strncat()**函數，宣告如下:

```cpp
char *strcat(char *連結目標(destination), char *連結對象(source), 連結目標大小);
```
這個函數也跟**strncpy()**一樣，並不會自動加上'\0'，所以使用方法是需要點訣竅的。來看看範例程式吧。

**source code**
*strncat_test.c*
```cpp
#include <stdio.h>
#include <string.h>

int main(){

	char filename[256] = "strcat_test";
	char ext[] = ".c";

	printf("filename is = %s\n", filename);

	strncat(filename, ext, sizeof(filename) - strlen(filename) - 1);
	filename[sizeof(filename)] = '\0';

	printf("filename is = %s\n", filename);

	return 0;
}
```
使用上面程式的對策(**sizeof(filename) - strlen(filename) - 1**跟**filename[sizeof(filename)] = '\0';**)，就算是寫**char filename[256] = "";**或是**char filename[13] = "strcat_test";**或是**char filename[] = "";**都沒問題。可以改一下來實驗看看。