### 字串的複製 (strcpy)

我們有說明過字串，也就是**char**型陣列的初始化方法。

```cpp
char one_string[] = "hello, world";
```
跟其他變數一樣，我們可能會想把它代入到別的陣列。在第12章時，我們寫過string_cpy.c來說明字串的複製，其實在標準函數中是有字串複製的函數可以用的。幾乎所有的C compiler都可以用，試試看吧。

要用的函數是STRing CoPY的簡稱，**strcpy()**。它的宣告如下:

```cpp
char *strcpy(char *copy destination, char *copy source);
```
返回值是copy destination的指標。因為**strcpy()**的prototype是在string.h中，所以使用前要先include。趕快來試試範例程式吧。

**source code**
*strcpy_test.c*
```cpp
#include <stdio.h>
#include <string.h>

int main(){

	char string_1[16] = "hello, world";
	char string_2[16] = "";

	printf("string_2 is = %s\n", string_2);

	strcpy(string_2, string_1);

	printf("string_2 is = %s\n", string_2);

	return 0;
}
```
**執行結果:**
```
string_2 is = 
string_2 is = hello, world
```
不用特地為了字串的複製而去寫一個迴圈，比較簡單便利。

**恐怖實驗:有許多陷阱的strcpy()函數**

雖然**strcpy()**函數在許多場合使用，但它是個危險的函數。列舉如下:

* 參數填NULL的話，程式就會異常中止。這是因為函數內部不會檢查是NULL指標還是別的什麼。
* 因為不會檢查大小，所以可能超過陣列大小依然繼續複製。

**strcpy()**的參數的任何一方填入NULL的話，程式都會異常中止。來實驗看看吧。

```
string_2 is = 
程式記憶體區段錯誤 (core dumped)
```
或是把**char string_2[16] = "";**換成**char string_2[2] = "";**試試看:

```
string_2 is = 
string_2 is = hello, world
```
恩，不知道為什麼筆者的電腦依然正常執行。原書作者的執行結果是
```
string_2 is = 
Abort trap: 6
```
因為copy source不是字串，只是單純的**char**陣列，所以**strcpy()**是不會檢查參數中的指標大小的。最後，就是造成結尾沒有'\0'的異常中止。

所以要用的話，先確認字串不是**NULL**，以及copy destination的大小夠大才使用這個函數。如果copy destination的大小不是很大的話，就用等下介紹的**strncpy()**來複製陣列大小的份就可以了。

### 有長度限制的字串複製

**strcpy()**函數因為沒有指定copy destination的大小的參數，所以只能用在跟copy source大小相等或更大的場合。但是因為有些不便，所以有可以指定copy destination大小的**strncpy()**。它的宣告如下:

```cpp
char *strncpy(char *copy destination, char *copy source, copy destination的size);
```
返回值是copy destination的指標。這裡也實驗看看吧。

**source code**
*strncpy_test.c*
```cpp
#include <stdio.h>
#include <string.h>

int main(){

	char string_1[16] = "hello, world";
	char string_2[16] = "";

	printf("string_2 is = %s\n", string_2);

	strncpy(string_2, string_1, sizeof(string_2));

	printf("string_2 is = %s\n", string_2);

	return 0;
}
```
這次把copy destination的size指定為**sizeof(string_2)**。實際上是16這個數值。因為大小是足夠的，所以會跟strcpy_test.c的結果相同。那麼，把**char string_2[16] = "";**換成**char string_2[2] = "";**試試呢?
```
string_2 is = 
string_2 is = he
```
不知道為何，筆者的電腦是可以好好顯示複製兩個字後的結果。但是原書作者的執行結果是:
```
string_2 is = 
string_2 is = hehello, world
```
原書作者解釋這是因為**strncpy()**會複製所指定的字數，不會複製到'\0'，所以會變成字串最後沒有'\0'的情況。要寫對的話，要像下面這樣寫:

```cpp
    strncpy(string_2, string_1, sizeof(string_2)-1); 
    string_2[sizeof(string_2) - 1] = '\0';
```
這樣的話執行結果如下:
```
string_2 is = 
string_2 is = h
```
這樣就可以作到所期待的結果。因為很容易搞錯所以要注意。BSD系的OS，有改良這難用用法的**strlcpy()**函數，因為不是C語言的標準所以割愛。有興趣的話可以在compiler的manual上調查一下，或是在電腦上打**man strlcpy**查詢(只有BSD系的OS才可以)。