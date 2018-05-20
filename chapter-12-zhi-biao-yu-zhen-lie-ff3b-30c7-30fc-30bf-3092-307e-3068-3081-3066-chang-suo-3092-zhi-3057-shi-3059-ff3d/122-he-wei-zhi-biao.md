所謂指標，直譯的話就是「指示」。那麼，C語言指示什麼呢? 答案是「變數或函數所在的場所」。也就是說，考慮成記憶體的位址(address)就可以了。

### 求出變數所在的場所

首先，從求出變數的位址開始好了。想取得位址的話要用到**&**運算元，這個運算元和同樣使用**&**的運算元沒有關係，沒有什麼「因為是address所以用and阿...」(筆者注: 這裡是指在第八章原書作者為了解釋&的用法而舉的例子)這種想法，請不要誤解了。用法如下:

```cpp
data_type 變數名;
&變數名 //取得此變數位址
```
只是拿到位址的話一點用都沒有，來用**printf()**來表示吧。**printf()**有為了表示指標的值的變換指定子%p，趕快來實驗看看吧。

**source code**
*addr.c*
```cpp
#include <stdio.h>

int main(){

	double number_1;
	int number_2;
	char character;

	printf("address of number_1 is %p\n", &number_1);
	printf("address of number_2 is %p\n", &number_2);
	printf("address of character is %p\n", &character);
	
	number_1 = 0.1;
	number_2 = 1;
	character = 'a';

	printf("number_1 is %f\n", number_1);
	printf("number_2 is %d\n", number_2);
	printf("character is %c\n", character);

	printf("address of number_1 is %p\n", &number_1);
	printf("address of number_2 is %p\n", &number_2);
	printf("address of character is %p\n", &character);

	return 0;
}
```
**執行結果:**
```
address of number_1 is 0x7fffe8127ea0
address of number_2 is 0x7fffe8127e9c
address of character is 0x7fffe8127e9b
number_1 is 0.100000
number_2 is 1
character is a
address of number_1 is 0x7fffe8127ea0
address of number_2 is 0x7fffe8127e9c
address of character is 0x7fffe8127e9b
```
執行的結果是先表示變數的位址，再把值代入變數之後顯示代進去的值，之後再顯示一次位址。可以發現，即使代入了值，位址也不會改變。一開始屋子的名字(變數名)跟位址就已經決定了，把值代入變數相當於把一個值放進去屋子而已，位址當然不會變。

陣列也是變數的一種，也可以取得位址並表示之。用法:

```cpp
data_type 變數名[元素數量];
&變數名[index] //取得位址
```
這裡也用範例程式確認一下吧。

**source code**
*array_addr.c*
```cpp
#include <stdio.h>

int main(){

	double double_array[2];
	int int_array[2];
	char char_array[2];

	printf("address of double_array[0] is %p\n", &double_array[0]);
	printf("address of double_array[1] is %p\n", &double_array[1]);

	printf("address of int_array[0] is %p\n", &int_array[0]);
	printf("address of int_array[1] is %p\n", &int_array[1]);

	printf("address of char_array[0] is %p\n", &char_array[0]);
	printf("address of char_array[1] is %p\n", &char_array[1]);

	return 0;

}
```
**執行結果:**
```
address of double_array[0] is 0x7ffc01db2da0
address of double_array[1] is 0x7ffc01db2da8
address of int_array[0] is 0x7ffc01db2d90
address of int_array[1] is 0x7ffc01db2d94
address of char_array[0] is 0x7ffc01db2db0
address of char_array[1] is 0x7ffc01db2db1
```
執行結果可以看出，**double**的陣列是每8 bytes跳一格，**int**的陣列是每4 bytes，**char**的陣列是每1 bytes。到這裡大家應該都可以理解，不過，陣列是有特殊對待的，也有不用&就可以取得位址的方法。

比如說**&char_array[0]**，跟寫成**char_array**是一樣的意思。來實驗看看吧。

**source code**
*char_array.c*
```cpp
#include <stdio.h>

int main(){

	char one_string[] = "hello, world\n";

	printf(" one_string   : %p\n",one_string);
	printf("&one_string[0]: %p\n",&one_string[0]);
	printf("&one_string[1]: %p\n",&one_string[1]);
	printf("&one_string[2]: %p\n",&one_string[2]);
	printf("&one_string[3]: %p\n",&one_string[3]);
	printf("&one_string[4]: %p\n",&one_string[4]);
	printf("&one_string[5]: %p\n",&one_string[5]);
	printf("&one_string[6]: %p\n",&one_string[6]);
	printf("&one_string[7]: %p\n",&one_string[7]);
	printf("&one_string[8]: %p\n",&one_string[8]);
	printf("&one_string[9]: %p\n",&one_string[9]);
	printf("&one_string[10]: %p\n",&one_string[10]);
	printf("&one_string[11]: %p\n",&one_string[11]);
	printf("&one_string[12]: %p\n",&one_string[12]);
	printf("&one_string[13]: %p\n",&one_string[13]);
	printf("&one_string[14]: %p\n",&one_string[14]);

	return 0;
}
```
**執行結果:**
```
 one_string   : 0x7fff62ebec40
&one_string[0]: 0x7fff62ebec40
&one_string[1]: 0x7fff62ebec41
&one_string[2]: 0x7fff62ebec42
&one_string[3]: 0x7fff62ebec43
&one_string[4]: 0x7fff62ebec44
&one_string[5]: 0x7fff62ebec45
&one_string[6]: 0x7fff62ebec46
&one_string[7]: 0x7fff62ebec47
&one_string[8]: 0x7fff62ebec48
&one_string[9]: 0x7fff62ebec49
&one_string[10]: 0x7fff62ebec4a
&one_string[11]: 0x7fff62ebec4b
&one_string[12]: 0x7fff62ebec4c
&one_string[13]: 0x7fff62ebec4d
&one_string[14]: 0x7fff62ebec4e
```
怎麼樣呢? 可以看到寫**one_string**跟**&one_string[0]**的值都一樣吧。因為是char陣列，所以也可以看到index每多1個位址就加1，這裡的再度實驗，當作複習。

第七章的話，說過為了處理字串，char陣列的最後會放入\0，所以如果要用**printf()**來指定陣列的話，就只要寫**one_string**就可以。

```cpp
printf("%s", one_string);
```
因為對陣列來說，寫**one_string**跟**&one_string[0]**都等價於**one_string[]**這陣列的第一個元素的位址，所以可以說處理字串的函數(如上例的printf())所接受的是陣列第一個元素的位址。如下圖所示:

```cpp
char one_string[] = "hello, world\n";
```
![Imgur](http://i.imgur.com/rPBoByI.png)

握們把這個位址，稱作「陣列開頭位址」。再做個實驗看看吧。

**source code**
*char_array_2.c*
```cpp
#include <stdio.h>

int main(){

	char one_string[] = "hello, world\n";

	printf(" one_string   : %s\n",one_string);
	printf("&one_string[0]: %s\n",&one_string[0]);
	printf("&one_string[1]: %s\n",&one_string[1]);
	printf("&one_string[2]: %s\n",&one_string[2]);
	printf("&one_string[3]: %s\n",&one_string[3]);

	printf(".\n.\n.\n");
	return 0;
}
```
**執行結果:**
```
 one_string   : hello, world

&one_string[0]: hello, world

&one_string[1]: ello, world

&one_string[2]: llo, world

&one_string[3]: lo, world

.
.
.
```
我們只是把char_array.c中**printf()**的%p改成顯示字串的%s而已。因為**one_string**跟**&one_string[0]**都是同一個位址，所以顯示相同。而**&one_string[1]**則是從e才開始顯示字串，所以變成「ello, world\n」。所以接下來的**&one_string[2]**會是「llo, world\n」。

簡單來說，C語言中藥成為字串的條件就只是最後有\0而已，而函數根據我們給的開頭位址輸出也會改變。只是說每次都寫**&one_string[0]**很麻煩，寫**one_string**可以跟**&one_string[0]**一樣，有這想法就可以了。

就算是字串以外也一樣，**int**還有其他data type只要寫下「變數名」，就跟「&變數名[0]」都是指定相同的位址。雖然很囉唆，請好好的記起來。

### 指標變數

現在已經知道了如果只是要取得變數的位址，用&就可以了。還有，不管接受什麼data type的陣列的函數，都跟字串一樣: 不是接受整個字串或陣列，只是接收了陣列的開頭位址。

整理一下，就像下面這樣:

* 把&加在已被宣告的變數名前面，代表那個變數的所在地(address)。
Example:
```
int a = 1000;
寫a的話，代表著變數a所存著的int值1000
寫&a的話，代表著變數a在記憶體上的位址(address)
```
* 宣告成陣列的變數名，如果直接寫變數名的話，代表那個陣列的開頭(第一個)位址(address)
Example:
```
int a[4] = {1000, 2000, 3000, 4000};
寫a[0]的話，代表著陣列a第一個元素所存著的int值1000
寫a的話，代表著陣列a的第一個元素在記憶體上的位址(address)
```
* 宣告成陣列的變數名，如果加上index還有&的話，代表那個陣列的index的位址(address)
Example:
```
int a[4] = {1000, 2000, 3000, 4000};
寫&a[0]的話，代表著陣列a的第一個元素在記憶體上的位址(address)
```
* 像接受字串的函數一樣接受陣列的函數，如果給它已宣告的陣列的名字的話，就相當於指定了那個陣列的開頭位址
Example:
```
char a[] = "test";
printf("%s", a);
a代表陣列a的開頭位址(就是存放t的地方)
```

就像本章開頭說明的，指標是「指示」。目前為止，單單知道變數是在哪裡，用途也只知道把字串交給函數而已。那麼，這跟「指示」有怎樣的關聯呢?

為了回答這問題，先要理解所謂的「指標變數」。只是取得了變數所在地(位址)，是什麼都做不到的。指標應該是為了在程式上做什麼東西，才會把地址本身放入變數。看了addr.c這個範例程式就能明白，變數的位址是0x7fffe8127ea0,0x7fffe8127e9c之類的，怎麼看都是整數。話說回來，記憶體上的位址從0開始，32bit電腦的話到0xffffffff(32bit能表示的最大的數，換算成10進位是4294967295)都是記憶體位址的範圍。64bit電腦的話到0xffffffffffffffff(64bit能表示的最大的數)都是記憶體位址的範圍。

4294967295以KB,MB,GB來表示的話，是4G。幾乎所有的32bit系統的最大ram容量都是4G，原因就在這裡。

那麼，可能有人會覺得要記憶變數的位址的話，只要用**int**就可以了，但是不行; 有專用的**指標型**的變數，就用那個。說是**指標型**的變數，跟普通變數的宣告也差不多。用法如下:

```cpp
data_type *變數名;
```

就像上面這樣，只是在變數名的前面加上了*。趕快來改造一下addr.c範例程式，把取得的變數位址存起來吧。雖然想讓讀者自己考慮範例程式的改造，但是因為指標是難關，所以把全source code都寫上去了。

**source code**
*addr_2.c*
```cpp
#include <stdio.h>

int main(){

    double number_1;
    int number_2;
    char character;

	double *pnumber_1;
	int *pnumber_2;
	char *pcharacter;

    printf("address of number_1 is %p\n", &number_1);
    printf("address of number_2 is %p\n", &number_2);
    printf("address of character is %p\n", &character);
    
    pnumber_1 = &number_1;
	pnumber_2 = &number_2;
	pcharacter = &character;

    printf("pnumber_1 is %p\n", pnumber_1);
    printf("pnumber_2 is %p\n", pnumber_2);
    printf("pcharacter is %p\n", pcharacter);

	number_1 = 0.1;
    number_2 = 1;
    character = 'a';

    printf("number_1 is %f\n", number_1);
    printf("number_2 is %d\n", number_2);
    printf("character is %c\n", character);

    printf("address of number_1 is %p\n", &number_1);
    printf("address of number_2 is %p\n", &number_2);
    printf("address of character is %p\n", &character);

    printf("pnumber_1 is %p\n", pnumber_1);
    printf("pnumber_2 is %p\n", pnumber_2);
    printf("address of pnumber_2 is %p\n", &pnumber_2)
    printf("pcharacter is %p\n", pcharacter);

    return 0;
}
```
**執行結果:**
```
address of number_1 is 0x7ffe54470b28
address of number_2 is 0x7ffe54470b24
address of character is 0x7ffe54470b23
pnumber_1 is 0x7ffe54470b28
pnumber_2 is 0x7ffe54470b24
pcharacter is 0x7ffe54470b23
number_1 is 0.100000
number_2 is 1
character is a
address of number_1 is 0x7ffe54470b28
address of number_2 is 0x7ffe54470b24
address of character is 0x7ffe54470b23
pnumber_1 is 0x7ffe54470b28
pnumber_2 is 0x7ffe54470b24
address of pnumber_2 is 0x7ffe54470b30
pcharacter is 0x7ffe54470b23
```
**&number_1**所表示的位址代入了**pnumber_1**，所以**&number_1**跟**pnumber_1**的顯示是一樣的。這可以確認變數pnumber_1的確可以紀錄位址。

而變數**pnumber_1**, **pnumber_2**, **pcharacter**等等就是**指標變數**，就是單純的擺放**number_1**, **number_2**, **character**的位址而已，要如何活用呢?

比如說，變數**number_2**放了數字的「1」。如果想作加法，那麼寫**number_2 + 1**就可以了。變數**pnumber_2**，因為是指標變數，所以擺的是位址「0x7ffe54470b24」。因為「0x7ffe54470b24」指示的位址是變數**number_2**，在這位址中放了一個數字「1」。

那麼，想把變數**number_2**加上什麼數時，利用變數**pnumber_2**，寫成**pnumber_2 + 1**會如何呢?

這樣的話，不會是期待的結果，因為**pnumber_2 + 1**其實是0x7ffda246da64 + 1; 本來變數**pnumber_2**這個地方所存的「值」就是0x7ffda246da64，不是1。

因為是64bit的電腦來執行程式，執行結果位數太多了實在很囉唆，我們就以32bit的記憶體來畫示意圖吧(就是只保留上面寫的位址的後8位)。記憶體的示意圖的條件有下列:

* 變數**number_2**的值是1
* 變數**number_2**的addrsss是0x54470b24
* 指標變數**pnumber_2**的值是變數**number_2**的addrsss, 0x54470b24
* 指標變數**pnumber_2**的addrsss是0x54470b30

所以這個程式的記憶體示意圖如下:

![Imgur](http://i.imgur.com/thZeYyD.png)

0x54470b30是變數**pnumber_2**的記憶體位址，其中存著0x54470b24(由左往右，5是最左邊的0101，以此類推)。0x54470b24變數**number_2**的記憶體位址，其中存著1。這裡的圖只列出number_2跟pnumber_2的值，其他像是0x54470b28是number_1的地址，pnumber_1的內存值，上面的圖都沒寫。

因為還是很囉唆，再更簡化一下好了。

* 變數**number_2**的值是1
* 變數**number_2**的addrsss是2
* 指標變數**pnumber_2**的值是變數**number_2**的addrsss: 2。

那麼上面的圖可以變成下面這樣:

![Imgur](http://i.imgur.com/dw0QCa4.png)

看圖可以知道，變數**pnumber_2**的值是位址，並不是用通常的**int**進行運算。但是，如果加上一個記號的話，就可以利用**pnumber_2**指示的位址所放的值，那個記號就是*。如下圖:

![Imgur](http://i.imgur.com/8Hu1CDm.png)

如果寫***pnumber_2 + 1**的話，就可以用放在**pnumber_2**的address，2，利用放在這個位址(2)的值來做處理。可能很囉唆，這裡就執行範例程式弄清楚吧。

**source code**
*addr_3.c*
```cpp
#include <stdio.h>

int main(){

	int number_2;
	int *pnumber_2;

	number_2 = 1;
	pnumber_2 = &number_2;

	printf("address of number_2 is %p\n", pnumber_2);

	printf("number_2 is %d\n", *pnumber_2);

	return 0;
}
```
**執行結果:**
```
address of number_2 is 0x7fff8fb321bc
number_2 is 1
```
看過這個例子以後，總算是了解了「指標」就是「指示」的關聯性。如果目前為止都能理解的話，之後的就不是這麼難了。如果還是不能理解的話，就把範例程式的值變一變來實驗看看吧。

像這樣使用指標，利用指標所指的值，我們稱作「間接參照」，所以剛剛用的*記號又叫「間接參照運算元」。會這麼稱呼是因為，原本我們可以直接用**number_2**就好，但是我們卻用了**pnumber_2**這個指標變數來「間接的」參照。

### 指標與陣列

要學間接參照，首先先搞懂什麼是指標吧。其實，如本章標題「指標與陣列」所示，指標與陣列有很深的關係，可以使用指標的話也可以很方便的使用陣列。陣列和普通變數不同，變數名稱後會加**[]**。但是，要儲存陣列位址的指標變數，並不會因為是陣列而需要做什麼特別的事。趕快來把陣列位址存到指標變數吧。

**source code**
*array_addr_2.c*
```cpp
#include <stdio.h>

int main(){

	int number_array[4] = {1000, 2000, 3000, 4000};
	int *pnumber;

	pnumber = number_array; //same as pnumber = &number_array[0]
	printf("*pnumber is %d\n", *pnumber);

	pnumber = &number_array[1];
	printf("*pnumber is %d\n", *pnumber);

	pnumber = &number_array[2];
	printf("*pnumber is %d\n", *pnumber);

	pnumber = &number_array[3];
	printf("*pnumber is %d\n", *pnumber);

	return 0;
}
```
**執行結果:**
```
*pnumber is 1000
*pnumber is 2000
*pnumber is 3000
*pnumber is 4000
```
變成你預想的結果了嗎? 結果有四行，分別對應**number_array[0]**~**number_array[3]**的結果。要說為什麼指標變數用在普通變數跟用在陣列沒什麼分別的原因，是因為在記憶體上普通變數跟陣列的一個元素利用的記憶體大小都是一樣的。

理解了指標與陣列後，來介紹陣列便利的使用方法吧。我們把陣列那一節介紹的for_array.c這個範例程式，用指標重寫一遍。

**source code**
*white_ptr.c*
```cpp
#include <stdio.h>

int main(){

	int number_array[] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10, -1};
	//-1 is sentinel value

	int answer;
	int *pnumber;

	pnumber = number_array;
	answer = 0;

	while(*pnumber != -1){
		answer += *pnumber;
		pnumber ++;
	}

	printf("answer = %d\n", answer);

	return 0;
}
```
**執行結果:**
```
answer = 55
```
好好的把1~10加起來變成55了。這個程式應該要注意的是下面的地方

1.
```cpp
while(*pnumber != -1){
```
指標變數pnumber裡面的值是**number_array[]**的開頭位址。因為這裡有間接參照運算元(*)，所以一開始可以得到*pnumber可以得到「1」這個值; 因為不是-1，所以繼續處理while裡面的指令。

2.
```cpp
answer += *pnumber;
```
這裡的*pnumber一樣是1，所以把1加到**answer**上。

3.
```cpp
pnumber ++;
```
這句是什麼意思呢? 從字面上來看，就是把指標變數**pnumber**給\++。如果是普通的整數型變數的話\++就是+1了。但是，指標變數**pnumber**給\++的話，並不是單純的+1。因為**pnumber**是**int**的指標變數，所以**pnumber**所指的地方也是有**int**的大小(32bit電腦通常是4bytes)，也就是說，如果把位址只加上1的話，會指向奇怪的地方。這也是為何位址不能用普通的**int**來儲存的原因。

由於這個**pnumber ++**，在下次的重複處理時，***pnumber**就會指向「2」了。因為使用指標可以把處理寫的很簡潔，所以C語言很常用。

再舉一個例子，來考慮把字串代入別的陣列。
```cpp
char string_1[16] = "hello, world";
char string_2[16];
```
如果是普通變數的話，只要寫**string_2 = string_1**就可以了，但是在陣列的情況下，這種寫法會讓compiler噴出「error:incompatible types in assignment」這種錯誤。

因為**string_1**代表的是**string_1**的開頭位址，想代入陣列的話就一定要指定代入哪一個元素，像**string_2**這裡也要寫成**string_2[0]**等等。在這種情況下，同常就會像下面這樣做重複的處理。

```cpp
char string_1[16] = "hello, world";
char string_2[16];
int i;

for(i=0; i < sizeof(string_2)-1 && string_1[i]!= '\0';i++){//>
	string_2[i] = string_1[i];
}
string_2[i] = '\0';
```
來寫個範例程式確認一下吧。
**source code**
*string_cpy.c*
```cpp
#include <stdio.h>

int main(){

	char string_1[16] = "hello, world";
	char string_2[16] = "";
	int i;

	printf("string_2 is = %s\n", string_2);

	for(i = 0; i< sizeof(string_2) - 1 && string_1[i]!='\0'; i++){//>
		string_2[i] = string_1[i];
	}
	string_2[i] = '\0';

	printf("string_2 is = %s\n", string_2);

	return (0);
}
```
**執行結果:**
```
string_2 is = 
string_2 is = hello, world
```
在copy前什麼都沒表示，copy後好好的表示字串了。來用指標重寫一遍吧。

**source code**
*string_cpy_2.c*
```cpp
#include <stdio.h>

int main(){

	char string_1[16] = "hello, world";
	char string_2[16] = "";
	char *src, *dst;

	src = string_1;
	dst = string_2;

	printf("string_2 is = %s\n", string_2);

	while(*src != '\0'){
		*dst++ = *src++;
	}

	printf("string_2 is = %s\n", string_2);

	return (0);
}
```
**執行結果:**
```
string_2 is = 
string_2 is = hello, world
```

執行結果是一樣的吧。(這個指標版本因為不檢查error，所以被copy的領域(string_2)如果比string_1小會出現異常的執行結果)。

這種程度的程式感受不到使用指標的好處，不過如果處理的字串複雜一點，使用不需要記index的指標是比較便利的。

還有，如果陣列變大的話，用指標比較好處理。現階段範例程式的元素數量也就是4或16這種很小的，實用的程式的元素數量一般會指定到像是1024或8192之類的。比如說把**char**陣列的元素數量指定到8192的話，光是字母就有8192個，而要處理一行長的文字資料就需要確保很大的陣列，圖像的話就更大了(如果是更大的資料的話，會無法存在stack上，需要別的方法來確保記憶體)。

每當呼叫出一次函數，就要去copy這麼大的記憶體領域，速度會很慢。作為代替只交給陣列的開頭位址的話，就可以達成用最少情報量處理大量的資料，所以指標的概念很重要。好好的精通使用方法吧。

**恐怖實驗:注意交給sizeof運算元的東西**(筆者注:其實這實驗是在解釋陣列跟指標的不同)

我們剛剛毫不在意了用了**i &lt; sizeof(string_2)**這句，實際上這裡是有陷阱的。把剛剛的string_cpy.c做如下的修改:

就是把
```cpp
char string_2[16] = "";
```
改成

```cpp
char dst[16] = "";
char *string_2 = dst;
```

**執行結果:**

```
string_2 is = 
string_2 is = hello, 
```

可以發現顯示途中就被切斷了。要說原因的話，原本**i &lt; sizeof(string_2)**這句是針對「陣列」的。在原本的程式，**sizeof(string_2)**的結果是16。但是現在的**string_2**，是宣告成**char *string_2 = dst**的指標變數。指標變數，它的size就只會是可以容納記憶體位址的大小，以64bit的電腦來說，記憶體位址的大小就只會是64bit，也就是8 bytes。

也就是說，現在**sizeof(string_2)**的結果是8，所以只會複製7個字元外加'\0'而已，所以顯示出來是hello, 。

對C語言來說，不管是陣列變數或是指標變數，如果只寫變數名稱的話就是代表它的開頭位址。所以下面的兩種寫法:

```cpp
char one_string[16] = "hello, world";
printf("%s\n", one_string);

char *one_string = "hello, world";
printf("%s\n", one_string);
```
不管哪一種，交給**printf()**的都是裝著「hello, world」這個字串的記憶體的開頭，所以沒有問題。但是**sizeof**這個運算元卻是很嚴密的區分這個變數是怎麼被宣告的。所以下面的程式:

```cpp
char one_string[16] = "hello, world";
printf("%zu\n", sizeof(one_string));

char *one_string = "hello, world";
printf("%zu\n", sizeof(one_string));
```
會分別顯示16跟8(32bit電腦會是4)。這觀念一定要記起來。

---
(筆者注:兩者的差別如下圖)
```cpp
char one_string[16] = "hello, world";
printf("%zu\n", sizeof(one_string));
```

![Imgur](http://i.imgur.com/VwtAdSw.png)

```cpp
char *one_string = "hello, world";
printf("%zu\n", sizeof(one_string));
```
![Imgur](http://i.imgur.com/YJ6TNgZ.png)

下下一節「但是指標跟陣列還是不同的東西」，會進行更詳細的文字說明。

### 原本陣列的index就是位址的運算

我們已經學習了陣列、指標、間接參照，也理解了陣列與指標之間深厚的關係。這裡為了能正確的理解，會更詳細的說明有關陣列與指標的東西。

陣列是同樣的data type確保在連續的記憶體領域上，從array_addr.c這個程式的執行結果就可以知道，index每加1，double陣列的位址會一次跳8 bytes，int陣列的位址會一次跳4 bytes, char陣列的位址會一次跳1 bytes。如下圖:

**array_addr.c執行結果:**
```
address of double_array[0] is 0x7ffc01db2da0
address of double_array[1] is 0x7ffc01db2da8
address of int_array[0] is 0x7ffc01db2d90
address of int_array[1] is 0x7ffc01db2d94
address of char_array[0] is 0x7ffc01db2db0
address of char_array[1] is 0x7ffc01db2db1
```

我們也說明過指標變數的increament(++)了。因為這是很重要的事，所以用圖解吧。以bit為單位的記憶體示意圖太囉唆了，就簡單點用以byte(1byte = 8bit)為單位的圖吧。圖如下

**double_array**
![Imgur](http://i.imgur.com/JnXMLyO.png)

**int_array**
![Imgur](http://i.imgur.com/Yf1FL3l.png)

**char_array**
![Imgur](http://i.imgur.com/Vy9LSLS.png)

預想的系統中，**double**佔8 bytes，**int** 4 bytes，**char** 1 bytes。把index加1的話，**double**一次跳8 bytes，**int** 4 bytes，**char** 1 bytes。也就是說，index是根據data type的大小來決定要前進多少位址。

到這裡，是不是發現陣列的index加1跟指標加1是一樣的? 舉例來說，把**double_array**的開頭位址代入**ptr**這個指標變數好了，程式會寫成下面這樣:

```cpp
double *ptr = double_array;
```

這個時候，指標變數**ptr**會放入0x7ffc01db2da0這個位址，如果加上*來間接參照的話就可以得到**double_array[0]**的值。接下來把指標變數**ptr**加1的話，位址就會加8變成0x7ffc01db2da8這個值，當然，加上*來間接參照的話就可以得到**double_array[1]**的值。趕快實驗一下吧。

**source code**
*array_addr_3.c*
```cpp
#include <stdio.h>

int main(){

	double double_array[2] = {0.1, 0.2};
	double *ptr;

	ptr = double_array;//same as ptr = &double_array[0]
	printf("*ptr is %f\n", *ptr);
	printf("double_array[0] is %f\n", double_array[0]);

	ptr++;
	printf("*ptr is %f\n", *ptr);
	printf("double_array[1] is %f\n", double_array[1]);

	return 0;
}
```
**執行結果:**
```
*ptr is 0.100000
double_array[0] is 0.100000
*ptr is 0.200000
double_array[1] is 0.200000
```
這是預想的結果吧。這個程式是先+1再用**printf()**顯示的，我們來試試直接在**printf()**中才+1。

**source code**
*array_addr_4.c*
```cpp
#include <stdio.h>

int main(){

	double double_array[2] = {0.1, 0.2};
	double *ptr;

	ptr = double_array;//same as ptr = &double_array[0]

	printf("*(ptr + 0) is %f\n", *(ptr + 0));
	printf("*(ptr + 1) is %f\n", *(ptr + 1));
	printf("double_array[0] is %f\n", double_array[0]);
	printf("double_array[1] is %f\n", double_array[1]);

	return 0;
}
```
**執行結果:**
```
*(ptr + 0) is 0.100000
*(ptr + 1) is 0.200000
double_array[0] is 0.100000
double_array[1] is 0.200000
```

***(ptr + 0)**跟**double_array[0]**相同，***(ptr + 1)**跟**double_array[1]**相同。也就是說，想把指標當陣列使用的話，就是用***(ptr + n)**。這樣的話，即使是接收指標為參數的函數，也可以像陣列一樣使用，比較安心了。

但是，請想起一件事。

作為陣列宣告的變數**double_array[]**，在程式中寫成**double_array**時，是表示double_array的開頭位址。也就是說，只寫**double_array**其實是**位址記號**。而作為指標宣告的變數**ptr**，原本就是儲存位址，所以也是一種表示位址的記號。也就是說，寫**double_array**跟寫**ptr**，表示的東西是一樣的。

這樣的話，感覺也可以寫成**ptr[0]**或**ptr[1]**吧。對，這感覺是正確的，可以試試把剛剛的範例程式***(ptr + 0)**換成**ptr[0]**、***(ptr + 1)**換成**ptr[1]**試試。

反過來說，即使宣告成陣列，也可以像指標一樣用間接運算元。可以試試，把剛剛的範例程式**double_array[0]**換成***(double_array + 0)**，**double_array[1]**換成***(double_array + 1)**。

其實，**[]**這個記號，不過是計算位址並進行間接參照的東西而已。***(double_array + 1)**跟**double_array[1]**相同的話，即使把index跟變數名對調也是可以執行的，因為數字上來看，double_array + 1 = 1 + double_array的。所以，**double_array[1]**是可以寫成**1[double_array]**的。

原本應該寫成加法計算位址，卻為了好懂可以寫成**double_array[1]**，而**[]**就叫做「語法糖(syntax sugar)」。

### 但是指標跟陣列還是不同的東西

到目前為止應該理解了指標跟陣列之間密切的關係，但指標跟陣列是完全不一樣的概念。如果要立刻了解到兩者是不同的東西，可以執行看看下面的程式碼:

```cpp
double double_array[2] = {0.1,0.2};
double *ptr;

ptr = double_array;
double_array = ptr;
```

第三行是沒問題的，但第四行不行，會造成compile error。原因的話，是因為**double_array[]**不是為了代入位址的變數。來看看示意圖吧。

![Imgur](http://i.imgur.com/KlTRZ0A.png)

在程式中，不管是寫**double_array**或是**ptr**，值都是「位址2」。所以，不管是寫**double_array[0]**或是**ptr[0]**，所參照的值都是放在位址2的數字，0.1。

**ptr = double_array**這個式子，意思是把**double_array[]**的開頭位址，作為值代入**ptr**裡。**ptr**本身則是一個位於位址1的指標變數。也就是說，我們是把「位址2」代入(1)裡面。

但是，就算執行**double_array = ptr**這個式子，**double_array**本身在宣告時已經是在位址2了，所以這個變更無法實現。所以**double_array = ptr**不能用。

因為用同樣的寫法都可以存取到一樣的值，所以不小心就會搞混，請認識到指標跟陣列是不一樣的東西。