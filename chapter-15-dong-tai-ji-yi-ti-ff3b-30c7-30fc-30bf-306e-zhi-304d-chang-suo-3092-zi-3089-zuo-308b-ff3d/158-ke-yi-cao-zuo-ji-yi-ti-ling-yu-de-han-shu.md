C語言的標準函數中有可以直接對特定記憶體領域操作的函數。比如說值的複製的話，並不是像**\*num_2 = *num_1;**這樣，利用變數名來進行值的複製，而是從**num_1**裡的記憶體位址開始，複製幾個byte到**num_2**所指的記憶體位址，這樣的感覺。

也很像對字串做**strncpy()**一樣。

### 填充記憶體領域 (memset)

**memset()**函數，可以把指定位址的記憶體領域給填入某個一定的值。宣告如下:

```cpp
void *memset(void *記憶體領域, int 一定的bytes, size_t 大小);
```
返回值是指向第一個參數所指定的記憶體領域的指標。**memset()**函數，是要對陣列填入特定的值所使用的。

如果以自動變數來宣告陣列的話，初始值會被指定為0。如果是用**calloc()**的話，雖然可以填0，但是沒辦法填-1。這種時候，就要用到**memset()**函數。跟**memset()**函數一樣，從此以後介紹的函數它們的prototype通通被宣告在string.h裡。

明明是記憶體卻是在字串的headfile? 雖然可能會這麼想，但是字串的操作跟記憶體的操作很像。不如說，字串操作就像是記憶體操作的特別版的感覺。趕快來用範例程式確認吧。

**source code**
*memset_test.c*
```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main(){

	int i;
	int *ptr;

	ptr = malloc(sizeof(int) * 8);

	for(i = 0;i < 8; i++){
		printf("%d ", ptr[i]);
	}
	printf("\n");

	memset(ptr, -1, sizeof(int) * 8);

	for(i = 0;i < 8; i++){
		printf("%d ", ptr[i]);
	}
	printf("\n");

	return 0;
}
```
**執行結果:**
```
0 0 0 0 0 0 0 0 
-1 -1 -1 -1 -1 -1 -1 -1 
```
可以發現被**malloc()**所確保的陣列，所有的元素都是0(**malloc()**所確保的記憶體不一定會初始化，所以不一定是0)。接下來我們用**memset()**全部填入-1，所以接下來迴圈中的**printf()**都印出了-1。

**恐怖實驗: 只能填充byte的memset()函數**

如果有可以指定位址的東西的話，**memset()**可以填入任何指定的值。但是，說到底必須要以「byte」為單位來填。

大部分的電腦都是以2的補數來處理負數，-1的話在電腦裡是0xffffffff或0xffffffffffffffff。這樣的話，如果利用開頭的oxff來重複填入8份，結果會是-1。

那麼，試一下把**memset(ptr, -1, sizeof(int) * 8);**改成**memset(ptr, 1, sizeof(int) * 8);**，再重新編譯執行一次程式看看。

**執行結果:**
```
0 0 0 0 0 0 0 0 
16843009 16843009 16843009 16843009 16843009 16843009 16843009 16843009 
```
變成奇怪的結果了。原因的話，因為**memset()**是每byte填進記憶體一次。1這個數字以1 byte表示就是0x01，int總共有4 bytes，所以01要填入4次，變成0x01010101，以十進位來說就是16843009。

如果所確保的記憶體是**double**或是自己定義的結構體或是內部表示比較複雜的data type的話，會出現更不知所然的結果。

用**memset()**來初始化變數的時候，就用0或-1這種可以預想的到內部表示的數字，data type也用**char**或**int**之類的。

還有，表示指標沒指向任何東西的**NULL**，在幾乎所有的電腦它的值都是0。在stdio.h或stddef.h等，就像下面這樣定義:

```cpp
#define NULL ((void *)0)
```
但是，可能有把-1當成**NULL**的電腦存在。這種電腦，下面的程式是無法預期執行的:

```cpp
struct tag_mydata{
	char *one_string;
    int *num_array;
};

struct tag_mydata mydata;

memset(&mydata, 0, sizeof(mydata));

if(mydata.one_string == NULL){
	//process
}
```
如果是把0當成**NULL**的電腦就可以沒問題的執行，但是，把-1當成**NULL**的電腦，if的條件是不會成立的。所以就算是麻煩，也要用**= NULL**來初始化指標變數跟指標型成員變數。
```cpp
struct tag_mydata{
	char *one_string;
    int *num_array;
};

struct tag_mydata mydata;

mydata.one_string = NULL;
mydata.num_array = NULL;

if(mydata.one_string == NULL){
	//process
}
```

### 複製記憶體領域(memcpy)

有可以把整塊記憶體的內容原封不動的複製到別塊記憶體領域的函數，**memcpy()**。可以把**memcpy()**想成就算是發現'\0'也會好好的把指定的份量給複製過去的**strncpy()**，會比較好懂。

```cpp
void *memcpy(void *複製目標, const void*複製來源, size_t 大小);
```
返回值是指向複製目標的指標。趕快來實驗範例程式吧。

**source code**
*memcpy_test.c*
```cpp
#include <stdio.h>
#include <string.h>

int main(){

	int a,b;
	double c,d;
	char string_1[256] = "test";
	char string_2[256];
	int array_1[4] = {1, 2, 3, 4};
	int array_2[4];

	a = 1000;
	c = 3.14;

	memcpy(&b, &a, sizeof(int)); //same as  b = a
	memcpy(&d, &c, sizeof(double)); //same as  d = c
	memcpy(string_2, string_1, sizeof(string_2));
	//sizeof(string_2) = sizeof(int) * 4
	memcpy(array_2, array_1, sizeof(array_2));

	printf("%d, %f, %s, %d %d %d %d\n",
			b, d, string_2, array_2[0],
			array_2[1], array_2[2], array_2[3]);

	return 0;
}
```
**執行結果:**
```
1000, 3.140000, test, 1 2 3 4
```
可以看得出來就是把變數**a**的內容複製到變數**b**，變數**c**的內容複製到變數**d**，字串**string_1**的內容複製到變數**string_2**，陣列**array_1**的內容複製到變數**array_2**。

**恐怖實驗: memcpy()一定會複製指定的量**

**memcpy()**一定會複製直到指定的量，不像**strncpy()**，看到了'\0'就會停止複製。我們把剛剛的範例程式改一下:

```cpp
	memcpy(&b, &a, sizeof(int)); //same as  b = a
```
改成
```cpp
	memcpy(&b, &a, 1024); //same as  b = a
```
**執行結果:**
```
程式記憶體區段錯誤 (core dumped)
```
這是因為接觸到了沒關係的記憶體領域，所以程式異常中止。最近的compiler可以檢查到明顯的overflow，但是關於**malloc()**函數等等被確保的動態記憶體，是不會發出警告的。請十分注意。

另外，**memcpy()**是無法對重疊的領域複製的。比如說下圖的情況:

![Imgur](http://i.imgur.com/1pmqbZl.png)

從**a**的部份複製4 bytes到**b**，會出現怎樣的結果是不知道的，因為這是未定義的動作，無法保證結果。如果想得到像下圖的結果，就要利用即使複製的來源跟目標重疊也沒關係的**memmove()**。使用方法跟**memcpy()**一樣，但是比較慢。

![Imgur](http://i.imgur.com/NuUlqpK.png)

### 比較記憶體領域(memcmp)

最後要介紹的函數，是比較記憶體內容的**memcmp()**函數。這個函數做的事幾乎跟**strncmp()**相同，只差在有沒有考慮'\0'。

```cpp
int memcmp(const void *領域1, const void *領域2, size_t 大小);
```
返回值跟**strcmp()**還有**strncmp()**是一樣的。只是，記憶體領域不太會考慮字典順序哪個比較大，通常是看結果是不是0(相等)。趕快來實驗一下吧。

**source code**
*memcmp_test.c*
```cpp
#include <stdio.h>
#include <string.h>

int main(){

	int a, b;
	double c, d;

	a = 1000;
	b = 1000;
	c = 3.14;
	d = 3.15;

	if (memcmp(&a, &b, sizeof(int)) == 0){
		printf("a and b is same\n");
	}

	if (memcmp(&c, &d, sizeof(double)) == 0){
		printf("c and d is same\n");
	}

	return 0;
}
```
**執行結果:**
```
a and b is same
```
因為變數**a**跟**b**的內容是一樣的，所以會顯示訊息，但變數**c**跟**d**的內容不同訊息就沒顯示了。跟**strcmp()**還有**strncmp()**比起來可能用到的機會比較少，不過還是記起來吧。

**恐怖實驗: memcmp()函數也可以比較結構體**

使用**memcmp()**函數的話，因為可以比較記憶體，所以除了一般的**int**之外，也可以比較結構體。趕快來實驗看看吧。

**source code**
*memcmp_test_2.c*
```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct tag_my_data{
	char character;
	double number;
};

int main(){

	struct tag_my_data *my_data_1;
	struct tag_my_data *my_data_2;
	
	my_data_1 = malloc(sizeof(struct tag_my_data));
	if(my_data_1 == NULL){
		printf("Memory allocation error!\n");
		return -1;
	}
	memset(my_data_1, -1, sizeof(struct tag_my_data));

	my_data_1 -> character = 'A';
	my_data_1 -> number = 3.14;

	
	my_data_2 = malloc(sizeof(struct tag_my_data));
	if(my_data_2 == NULL){
		printf("Memory allocation error!\n");
		free(my_data_1);
		return -1;
	}
	memset(my_data_2, 1, sizeof(struct tag_my_data));

	my_data_2 -> character = 'A';
	my_data_2 -> number = 3.14;

	if(memcmp(my_data_1, my_data_2, sizeof(struct tag_my_data)) == 0){
		printf("my_data_1 and my_data_2 is the same\n");
	}

	free(my_data_1);
	free(my_data_2);

	return 0;
}
```
執行後可以發現什麼都沒印出來，但我們想要的是「my_data_1 and my_data_2 is the same」，因為我們的確把**my_data_1**跟**my_data_2**都代入相同的值。

問題出在我們向**memset()**傳入的參數。

```cpp
memset(my_data_1, -1, sizeof(struct tag_my_data));
memset(my_data_2, 1, sizeof(struct tag_my_data));
```
我們分別把my_data_1跟my_data_2填滿了-1跟1。因為結構體會自動的進行alignment，所以會有一些.運算元跟->運算元指不到的記憶體間隙。我們用了**memset()**，以byte為單位填入了指定的值，所以雖然只看成員變數會一樣，但是以byte為單位做比較的**memcmp()**來看就不一樣，這是因為記憶體間隙的值不同。

如果向兩方**memset()**都傳入0，應該就可以看到
```
my_data_1 and my_data_2 is the same
```
不過一開始就不應該寫
```cpp
if(memcmp(my_data_1, my_data_2, sizeof(struct tag_my_data)) == 0)
```
而是寫
```cpp
if(my_data_1->character == my_data_2->character &&  my_data_1->number == my_data_2->number)
```
才對。