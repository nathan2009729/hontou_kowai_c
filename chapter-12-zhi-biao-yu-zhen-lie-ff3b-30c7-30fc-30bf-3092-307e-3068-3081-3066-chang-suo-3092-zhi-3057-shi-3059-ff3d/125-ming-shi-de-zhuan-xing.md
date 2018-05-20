目前為止所說的都是偷偷轉型，不過也可以明確的指定要轉成什麼型，這種明示的轉型稱為**cast**。cast很簡單，只要在變數左側()裡寫入想轉的型態就可以。用法如下:

```cpp
(想轉的data_type)變數名;
```
這裡來實驗一下吧。

**source code**
*cast.c*
```cpp
#include <stdio.h>

int main(){

	int num_1 = 1000;
	char num_2 = num_1;
	short num_3 = num_1;

	printf("int -> char %d\n", num_2);
	printf("int -> short %d\n", num_3);

	num_3 = (char) num_1;

	printf("int -> (char) short %d\n", num_3);

	return 0;
}
```
**執行結果:**
```
int -> char -24
int -> short 1000
int -> (char) short -24
```

首先把**int**轉型成**char**之後，變成了-24。接下來轉型成**short**，因為short足夠表示1000所以就直接表示1000。第三個，在代入short前有(char)，所以先轉成char，變成了-24，再轉成short，依然是-24。

這個cast，如果是合理的變換的話是什麼都可以轉換的。先不管結果正不正確，指標變數的內部也是「數值」，也可以變換成整數。

這裡也來實驗看看吧，addr.c範例程式的指標的值用**printf()**來表示的話需要用%p這個變換指定，所以我們做如下變更來執行看看。

```cpp
printf("address of number_1 is %p\n", &number_1);
printf("address of number_2 is %p\n", &number_2);
printf("address of character is %p\n", &character);
```
改成
```cpp
printf("address of number_1 is %llx\n", 
(unsigned long long)&number_1);
printf("address of number_2 is %llx\n", 
(unsigned long long)&number_2);
printf("address of character is %llx\n", 
(unsigned long long)&character);
```
**執行結果:**
```cpp
address of number_1 is 0x7ffef53b0070
address of number_2 is 0x7ffef53b006c
address of character is 0x7ffef53b006b
number_1 is 0.100000
number_2 is 1
character is a
address of number_1 is 7ffef53b0070
address of number_2 is 7ffef53b006c
address of character is 7ffef53b006b
```
除了前面沒有0x之外，應該是一樣的數字。既然指標變數可以轉換成普通的整數，當然也可以轉成別的data type的指標變數。

```cpp
(data_type *)變數名;
```
之前說明過，指標變數在運算後會自動決定要前進多少位址。比如說char*每加1就前進1 byte，int*則是4 bytes。如果把一個指標變數換成別的data type的指標變數，則會改變前進多少。比如說可以讓int像char一樣1 byte的讀取。

因為是有趣的實驗，趕快來實驗範例程式看看吧。

**source code**
*ptr_cast.c*
```cpp
#include <stdio.h>

int main(){

	int i; //a counter that avoid exceed the memory
	unsigned char *ptr; 

	int num_1 = 2147483647;
	long long num_2 = 9223372036854775807;
	double num_3 = 1.797693e+308;

	printf("num_1 = %d\n", num_1);
	//printf("num_1 = 0x%X\n", num_1);
	ptr = (unsigned char *)&num_1;
	for(i = 0; i<(int)sizeof(num_1); i++){//>
		printf("0x%X ", *ptr);
		ptr++; //same as printf("0x%X", ptr[i]);
	}
	printf("\n");


	printf("num_2 = %lld\n", num_2);
	//printf("num_2 = 0x%X\n", num_2);
	ptr = (unsigned char *)&num_2;
	for(i = 0; i<(int)sizeof(num_2); i++){//>
		printf("0x%X ", *ptr);
		ptr++;
	}
	printf("\n");


	printf("num_3 = %f\n", num_3);
	//printf("num_3 = 0x%X", num_3);
	ptr = (unsigned char *)&num_1;
	for(i = 0; i<(int)sizeof(num_3); i++){//>
		printf("0x%X ", *ptr);
		ptr++;
	}
	printf("\n");

	return 0;
}
```
**執行結果:**
```
num_1 = 2147483647
0xFF 0xFF 0xFF 0x7F 
num_2 = 9223372036854775807
0xFF 0xFF 0xFF 0xFF 0xFF 0xFF 0xFF 0x7F 
num_3 = 179769300000000004979913091153546431177385676945343486730197993498529636492108312404543796370045487218955201046137662191918513706549560771088224207924409275479864981823815660983343422176365744870072127934490865277449576937261468130920376085948653305075071243237207672347403131791038321491100101082182265602048.000000
0xFF 0xFF 0xFF 0x7F 0x4 0x0 0x0 0x0 
```
從上面的程式執行，可以知道2147483647在記憶體中是以0xFF 0xFF 0xFF 0x7F這樣並排的。同理，**long long**跟**double**在記憶體裡儲存的值也透過指標顯示出來。

對於2進位跟16進位熟悉的人，是不是有「阿咧?」的感覺呢? 2147483647在16進位應該是0x7FFFFFFF的，但顯示出來卻是0xFFFFFF7F，以十進位**insigned int**來說變成了4294967167這個超越int表示範圍的數。這是怎麼回事呢?

這是因為筆者(原書作者也是)的電腦是intel系CPU，它的「byte order」是屬於「little endian」。所謂byte order，就是一個需要多個byte來表示的資料，在記憶體上的byte的排列順序(order)。比如說某個資料是0x12 0x34 0x56 0x78好了，如果在記憶體中以0x78 0x56 0x34 0x12來排的話就是**little endian**，反之就這樣照著0x12 0x34 0x56 0x78來排的話就是**big endian**。

intel系CPU是屬於little endian，而SPARC跟PowerPC是屬於big endian。乍想之下，big endian這個直接照順序排的不是比較好懂嗎? 但基本上多byte的計算都是從最低位開始計算，跟人類在筆算的時候，也是從最低位開始計算是一樣的道理。所以，把最低位放在記憶體最前端的little endian，可以說是有道理的。

但是，世上有許多的電腦都是big endian，還有在網路上傳輸的資料也幾乎是用big endian排列。所以為了可以轉換，大部分的系統都準備了可以換byte order的函數。以本書所用的gcc來說，**ntohl()**函數跟**htonl()**函數是可以作到這個轉換的，有興趣的人可以調查一下。還有，byte order是在多byte的data type才有差別，所以和字串的陣列沒有關係(因為char陣列一個元素也就1 byte)。比如說宣告了一個char one_string[]="hello, world\n"好了，那麼記憶體裡也是不會儲存成「\0\ndlrow, olleh」的，而且這樣寫也存不進去。

執行這個程式以後，再看一次intworld.c這個範例程式應該可以理解更深。跟指標有關的東西很囉唆，推薦重複多學幾次。

**恐怖實驗:如果對指標強制轉型**

學到這裡也習慣指標，可能可以看compiler的manual來做各種挑戰了。但是，指標還有陷阱，來介紹一下。

如果使用cast的話，可以自由自在的存取記憶體。比如說，如果把整數進行cast變成unsigned char的指標變數的話，就可以讀取任意範圍的位址的內容。來實驗看看吧。

**source code**
*ptr_scan.c*
```cpp
#include <stdio.h>

int main(){

	int i;
	unsigned char *ptr;

	ptr = (unsigned char*)0x1000;

	for(i = 0; i < 0x1000; i++){//>
		printf("0x%02X ", *ptr);
		if(((i + 1) % 15) == 0 ){
			printf("\n");
		}
		ptr++;
	}
	printf("\n");

	return 0;
}
```
**執行結果:**
```
程式記憶體區段錯誤 (core dumped)
```
這個程式是把記憶體上位址0x1000~0x1100為止的內容用16進位表示出來。在執行的瞬間就異常中止了。現代的OS，像是Windows, Linux, Mac OS X之類的，都有著記憶體的保護機能，為了防止程式存取不應該存取的位址導致暴走，會強制中止程式。

而0x1000就是不能讀取的記憶體位址。想執行類似這樣的程式，就必須宣告一個變數，指定程式員可以自由處理的位址。比如說，把

```cpp
ptr = (unsigned char*)0x1000;
```
改成
```cpp
char buf[1024] = "This is text message.";
ptr = (unsigned char *) buf;
```
因為指標出現異常的值而又去存取那個值的內容的話，會在處理的途中強制中止，所以要注意。

到目前為止，其實就跟NULL的說明一樣。

還有一種記憶體位址，是可以讀取但不能寫入的。在第七章說明字串常數時就有說明過:

```cpp
char one_string[] = "hello, world\n";
```
這句意思是把陣列one_string[]用"hello, world\n"這個字串給初始化。講的詳細點，"hello, world\n"這個字串常數在compile跟link時，就配置位址在某處，程式執行時複製給陣列one_string[]。因為是陣列，所以就算在執行時被其他值給改動都不會有什麼事。

```cpp
char *one_string = "hello, world\n";
```
這句意思是把指標變數**one_string**用"hello, world\n"這個字串的*初始位址*給初始化，"hello, world\n"這個字串常數在compile跟link時，就配置位址在某處，程式執行時把開頭位址傳給**one_string**。

由於字串常數多數是被確保在寫入禁止的記憶體區域，所以***(one_string + 0) = 'H'**或是**one_string[0] = H**這種操作會使得程式異常中止。這裡也實驗看看吧。

**source code**
*ptr_const.c*
```cpp
#include <stdio.h>

int main(){

	char *one_string = "hello, world\n";

	one_string[0] = 'H';

	printf("%s", one_string);

	return 0;
}
```
**執行結果:**
```
程式記憶體區段錯誤 (core dumped)
```
原書作者用Mac OS X是Bus error (core dumped)，筆者是Linux，"程式記憶體區段錯誤"是Segmentation fault。

如果搞混陣列跟指標的話，就會犯下這種錯誤。要再度複習的話就把第七章跟本章重新讀過吧。