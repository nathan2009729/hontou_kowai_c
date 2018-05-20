來介紹指標變數最後的變數，「double pointer(雙重指標)」吧。用講的很簡單，就是放著指標變數的位址的指標變數; 而放著指標變數(b)的位址的指標變數(a)，(b)自己又是一個雙重指標，就把(a)叫做「triple pointer(三重指標)」。宣告也很簡單，就是增加*號而已。

```cpp
char array[4] = {0, 1, 2, 3};
char *ptr = array; //pointer
char **ptr_double = &ptr;//double pointer
```
用講的不好懂就根據剛剛的程式碼上示意圖吧。

![Imgur](http://i.imgur.com/L0SuoYS.png)

上面就是根據程式碼所畫的指標跟雙重指標的示意圖。假設char陣列**array[]**，它的開頭位址是3。指標變數ptr，則是儲存陣列**array[]**的開頭位址3; 雙重指標變數ptr_double，則是儲存指標變數ptr的開頭位址2。

如果寫*ptr_double的話，則是間接參照一次，輸出的值會是「位址3」。而只寫ptr的話，就是「位址3」。所以，寫*ptr_double的值跟寫ptr是一樣的。

如果寫**ptr_double的話，則是間接參照兩次，輸出的值會是「0」。像**ptr_double這樣的變數，有雙重指標的稱呼，不過也有人稱為「指標的指標」。如果說成指標的指標，就很容易理解是保存著指標位址的變數了。執行下面的範例程式就很容易理解了，試試看吧。

**範例程式:**
*ptr_double.c*
```cpp
#include <stdio.h>

int main(){

	char array[4] = {'A', 'B', 'C', 'D'};
	char *ptr = array;
	char **ptr_double = &ptr;

	printf("array %p\n", array);
	printf("array %s\n", array);
	printf("array[0] %c\n", array[0]);
	printf("array[1] %c\n", array[1]);
	printf("array[2] %c\n", array[2]);

	printf("ptr %p\n", ptr);
	printf("&ptr %p\n", &ptr);
	printf("ptr %s\n", ptr);
	printf("*ptr %c\n", *ptr);
	printf("*(ptr + 1) %c\n", *(ptr + 1));
	printf("*(ptr + 2) %c\n", *(ptr + 2));

	printf("ptr_double %p\n", ptr_double);
	printf("*ptr_double %p\n", *ptr_double);
	printf("&ptr_double %p\n", &ptr_double);
	printf("*ptr_double %s\n", *ptr_double);
	printf("**ptr_double %c\n", **ptr_double);
	printf("*(*ptr_double + 1) %c\n", *(*ptr_double + 1));
	printf("*(*ptr_double + 2) %c\n", *(*ptr_double + 2));

	return 0;
}
```
**執行結果:**
```
array 0x7ffef7035370
array ABCD�
array[0] A
array[1] B
array[2] C
ptr 0x7ffef7035370
&ptr 0x7ffef7035360
ptr ABCD�
*ptr A
*(ptr + 1) B
*(ptr + 2) C
ptr_double 0x7ffef7035360
*ptr_double 0x7ffef7035370
&ptr_double 0x7ffef7035368
*ptr_double ABCD�
**ptr_double A
*(*ptr_double + 1) B
*(*ptr_double + 2) C
```
array, ptr, *ptr_double各自都顯示了相同的位址，0x7ffef7035370。所以在用**printf()**的%s時array, ptr, *ptr_double都會顯示ABCD�。而把array[0]用%c表示時，array[0], *ptr, **ptr_double也都表示的一樣。

指標的指標，會在實做多維陣列、還有從函數傳遞指標時會使用。雖然沒有現在使用的必要，不過在影像處理還有動態記憶體確保時是必要的。為了加深理解，試試把範例程式改造成三重指標間接參照吧。