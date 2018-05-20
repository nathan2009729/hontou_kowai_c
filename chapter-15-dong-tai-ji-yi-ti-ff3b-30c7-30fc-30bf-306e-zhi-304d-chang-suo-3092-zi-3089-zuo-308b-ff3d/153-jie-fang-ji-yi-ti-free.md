至今為止的流程，這裡應該要趕快來試試範例程式了，不過還要做跟確保記憶體相對的解放記憶體，所以先說明這個。

記憶體的解放，是利用**free()**函數。

```cpp
void free(void *ptr);
```
要解放剛剛用**malloc()**確保的**one_string**，要像下面這樣寫:

```cpp
free(one_string);
```

這樣的話，就會把指標變數**one_string**指著的記憶體解放。但**one_string**只是放著指標的值而已，所以**one_string**本身並不會消滅。

那麼，來實驗看看吧。

**source code**
*memory.c*
```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main(){

	char *one_string;

	one_string = malloc(0xffffffffffffffff);
	if(one_string == NULL){
		printf("Memory allocation error!\n");
	}
	printf("one_string=%p\n", one_string);
	free(one_string);

	one_string = malloc(14);
	if(one_string == NULL){
		printf("Memory allocation error!\n");
		return -1;
	}
	printf("one_string=%p\n", one_string);
	
	strncpy(one_string, "hello, world\n", 14 - 1);
	one_string[14 - 1]= '\0';

	printf("%s", one_string);
	
	printf("one_string=%p\n", one_string);
	free(one_string);

	printf("one_string=%p\n", one_string);

	return 0;
}
```
**執行結果:**
```
Memory allocation error!
one_string=(nil)
one_string=0x7fc4bc000cd0
hello, world
one_string=0x7fc4bc000cd0
one_string=0x7fc4bc000cd0
```
這是有些難懂的實驗。最初把想確保的大小指定成0xffffffffffffffff故意失敗，幾乎所有的系統都會失敗吧。要注意，32bit的CPU請指定成0xffffffff。我們指定了超過1000萬TB的記憶體，所以一開始就不會成功，這是為了對應之後的**free()**函數萬一成功的情況。而雖然**malloc**失敗時會返回**NULL**，但是**free()**接受**NULL**為參數並不會怎樣，所以沒問題。

之後，我們確保了可以容納14字字串的14 bytes memory。之後利用了**strncpy()**函數複製了字串，再用**printf()**把字串表示在畫面上後，用**free()**解放。由於指標只是單純的值，所以記憶體解放前後**one_string**都不會變。

但是，因為**one_string**所指的地方在**free()**後已經不能用了，所以**free()**後不能再去碰**one_string**這個指標。不知道會發生什麼事的。

**恐怖實驗: 使用已經被解放的記憶體**

去碰被**free()**後的記憶體會發生什麼事呢? 我們來實驗看看在**free()**後，再度用**strncpy()**寫上去，程式改動如下:
把

```cpp
	free(one_string);

	printf("one_string=%p\n", one_string);
	return 0;
```
改成
```cpp
	free(one_string);

	printf("one_string=%p\n", one_string);
	strncpy(one_string, "hello, world\n", 14 - 1);
	one_string[14 - 1]= '\0';
	printf("%s", one_string);
	return 0;
```
**執行結果:**
```
Memory allocation error!
one_string=(nil)
one_string=0x7f9d24000cd0
hello, world
one_string=0x7f9d24000cd0
one_string=0x7f9d24000cd0
hello, world
```
什麼事都沒發生的正常執行了。其實**free()**並沒有清除被指定的記憶體領域，所以大部分情況下什麼都不會發生。那麼，我們再把程式改一下:

把
```cpp
	free(one_string);

	printf("one_string=%p\n", one_string);
	strncpy(one_string, "hello, world\n", 14 - 1);
	one_string[14 - 1]= '\0';
	printf("%s", one_string);
	return 0;
```
改成
```cpp
	free(one_string);

	printf("one_string=%p\n", one_string);
	strncpy(one_string, "hello, world\n", 14 - 1);
	one_string[14 - 1]= '\0';
    int *ptr = malloc(sizeof(int)); //new
    *ptr = 2147483647; //new
	printf("%s", one_string);
	return 0;
```
**執行結果:**
```
Memory allocation error!
one_string=(nil)
one_string=0x7fd73c000cd0
hello, world
one_string=0x7fd73c000cd0
one_string=0x7fd73c000cd0
���o, world
```
變成奇怪的結果了。這是因為我們把位址0x7fd73c000cd0解放，但又用**malloc(sizeof(int))**去存取它，直到這時我們才發現異常。如果在**free()**後不立刻用**malloc()**的話，是很難發現異常的，不過這跟系統有關就是了。所以會有因為時間過去而無法好好執行的這樣一個很難發現的bug存在。當然也可以再加個**free()**就好了，總之要好好考慮再寫程式。