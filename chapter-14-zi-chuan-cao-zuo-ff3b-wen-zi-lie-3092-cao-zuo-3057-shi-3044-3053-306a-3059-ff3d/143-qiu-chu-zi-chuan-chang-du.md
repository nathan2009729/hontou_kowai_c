記住了字串的複製之後，接下來介紹調查字串長度的函數。可以調查字串長度(就是char陣列的長度)的程式，在第十章的string_length.c介紹過。其實這程式也被實裝在標準library中，幾乎所有的環境都可以使用，就是STRingLENgth的縮寫，**strlen()**函數。宣告方式如下:

```cpp
size_t strlen(const char *對象字串)
```
指定想調查的字串的指標後，會返回用**size_t**(**printf()中以%zu表示**)為data type的字串長度。以前的範例程式因為是自己寫的所以機能比較簡單，趕快來試試範例程式吧。**strlen()**的prototype在string.h中，所以要include它。

**source code**
*strlen_test.c*
```cpp
#include <stdio.h>
#include <string.h>

int main(){

	char one_string[16] = "hello, world\n";
	size_t length;

	length = strlen(one_string);

	printf("length is %zu\n", length);

	return 0;
}
```
**執行結果:**
```
length is 13
```
跟string_length.c的結果是一樣的吧。如果參數填**NULL**的話，因為它不是以'\0'為結尾的**char**陣列(也就是說，**NULL**不是字串)，所以會異常中止，要注意。