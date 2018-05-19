這一章的最後，來說明變數的種類(**storage class**)吧。

### 靜態變數就是安靜的一直在那裡

目前為止說明的變數都是存在stack裡面，這種叫做**local變數**或是**自動變數**。確保在stack上，在那個函數的處理結束後，變數本身也會消失的就是local變數。那麼，來實驗一下範例程式吧。因為利用到還沒詳細說明的函數的宣言與利用，程式的動作請參照source code中的註解。

```cpp
#include <stdio.h>

char* func(){
	char one_string[14] = "hello, world\n";
	printf("from func: %s",one_string);
	return (one_string);
	/*return the first address of one_string to caller*/
}

int main(){
	
	printf("from main: %s", func());
	
	return 0;
}
```

在第六章只有一點點說明的函數登場，雖然有點難，但應該猜得到結果吧。**main()**函數與**func()**函數這兩個地方呼叫了可以讓畫面顯示文字的**printf()**，所以畫面會出現兩行字串: 

* 第一個執行的是**func()**中的**printf()**，會出現
```
	from func: hello, world
```
* 第二個執行的是把**func()**中宣告的**one_string**交給caller **main()**函數
```
	from main: hello, world
```
但是實際執行看看，會變成
```
	from func: hello, world
	from main: (null)
```
在compile的時候也有發出警告
```
auto-var.c: In function ‘func’:
auto-var.c:6:9: warning: function returns address of local variable [-Wreturn-local-addr]
  return (one_string);
         ^
```
為什麼會發生跟預期不同的結果呢?

**func()**中宣告的**one_string**陣列是確保在stack上的，在**func()**處理結束後，**one_string**的內容就被破壞了。

那麼，要如何像我們所期待的，先是**func()**中的**printf()**執行，再來是**main()**中的**printf()**執行呢？

要做到的話，就需要做一個不同的宣告，這宣告可以把變數確保在跟stack不一樣的地方。

函數中，如果把變數宣告成**靜態變數**的話，這變數就會被確保在跟stack不一樣的地方。靜態變數要像以下這樣宣告:
```cpp
static data_type 變數名;
static data_type 變數名 = 初始值;
```
現在把auto-var.c的`char one_string[14] = "hello, world\n";`改成`static char one_string[14] = "hello, world\n";`，執行起來就變成期待的輸出:
```cpp
from func: hello, world
from main: hello, world
```
這個靜態變數，有可以一直保存值的性質。這是怎樣的一回事呢? 來執行個範例程式實驗一下吧。
**source code**
*auto-var2.c*

```cpp
#include <stdio.h>

void func(){
	int count =0;

	count = count + 1;

	printf("count = %d\n", count);

	return;
}

int main(){
	func();
	func();
	func();

	return 0;
}
```
**執行結果**
```
count = 1
count = 1
count = 1
```
原本我們預想，**func()**被**main()**呼叫3次，那麼值應該是1,2,3這樣顯示，但實際上都顯示1。再重新看一次程式，可以知道在**func()**裡面的變數宣告部份，因為`int count =0;`這一句造成每呼叫一次**func()**就把count初始化一次。如果我們把這句改成`static int count =0;`執行結果就會變成
```
count = 1
count = 2
count = 3
```
也就是說，把**count**宣告成靜態變數就可以得到預想的結果。像這樣利用靜態變數的話，就可以保存「狀態」，非常便利。請記得這個特徵。
### 全域變數一直都在某個地方存在著

接下來說明**全域變數**。全域變數又被稱為「global變數」，程式的任何地方都可以寫入讀取的變數。全域變數跟一般的local變數的宣告是一樣的，但宣告的位置不同。local變數是在函數中(例如**main()**裡面的開頭)宣告，全域變數是在不是函數的地方宣告。什麼是"不是函數的地方"呢?先來看看範例程式吧。

**source code**
*global.c*

```cpp
#include <stdio.h>

int global_number;

void func_1(){
	printf("from func_1: %d\n", global_number);

	return;
}

void func_2(){
	printf("from func_2: %d\n", global_number);

	return;
}

void func_3(){
	printf("from func_3: %d\n", global_number);

	return;
}

int main(){
	global_number = 1;
	func_1();
	
	global_number = 10;
	func_2();

	global_number = 100;
	func_3();

	return 0;
}
```
雖然是有點長的程式，但內容很簡單。**main()**函數中把值帶入**global_number**中再呼叫**func_數字()**這函數把**global_number**裡面的值表示出來。

**執行結果**
```
from func_1: 1
from func_2: 10
from func_3: 100
```
如何呢? 這應該是預想內的結果吧。全域變數可以簡單的讓複數的函數去處理它的值，是非常便利的構造。但是因為每個函數都可以去修改它的值，濫用的話程式會不好讀，也常常成為bug的原因。

在程式的全體都需要共有的設定資料之類的最低限度才會利用全域變數，其他的值的處理就把值交給函數吧。會在第十章詳細說明(值渡)

### extern 可以reference別的檔案有的全域變數

好不容易了解了全域變數的使用方法，source code把全域變數交給複數檔案的利用方法也記起來吧。

一般來說，source code把變數交給複數檔案的話，一個 source code生成一個object code，最後一起link成為一個執行檔。這個時候，一個一個打gcc的命令是很麻煩的，這才是Makefile發揮真正價值的時候。

趕快來build範例程式吧。

**source code**
*extern_sub.c*
```cpp
int global_number = 99;
```
*extern_main.c*
```cpp
#include <stdio.h>

int main(){
	return 0;
}
```

**Makefile**
*Makefile.extern*
```makefile
PROGRAM   = extern
OBJS 	 = extern_sub.o extern_main.o
SRCS	  = $(OBJS: %.o=%.c)
CC		= gcc
CFLAGS	= -g -Wall
LDFLAGS   =
$(PROGRAM):$(OBJS)
	$(CC) $(CFLAGS) $() -o $(PROGRAM) $(OBJS) $(LDLIBS)
```
輸入指令`make -f Makefile.extern`後，會出現以下訊息
```
gcc -g -Wall   -c -o extern_sub.o extern_sub.c
gcc -g -Wall   -c -o extern_main.o extern_main.c
gcc -g -Wall  -o extern extern_sub.o extern_main.o 
```
可以發現跟至今為止不同出現了三行，第一行第二行分別是生成extern_sub.c跟extern_main.c的object file，第三行是link，生出了名為extern的執行檔。

**extern**這個範例程式，在extern_main.c中的**main()**函數裡什處理都沒寫，所以執行的話什麼都不會發生。機會難得，我們試試看把extern_sub.c宣告的**global_number**的內容用**printf()**函數顯示出來吧。現在把extern_main.c改成
```cpp
#include <stdio.h>

int main(){

	printf("global_number = %d\n", global_number);
	
	return 0;
}
```
但是在makefile時會出現
```
gcc -g -Wall   -c -o extern_main.o extern_main.c
extern_main.c: In function ‘main’:
extern_main.c:5:33: error: ‘global_number’ undeclared (first use in this function)
  printf("global_number = %d\n", global_number);
                                 ^
extern_main.c:5:33: note: each undeclared identifier is reported only once for each function it appears in
<builtin>: recipe for target 'extern_main.o' failed
make: *** [extern_main.o] Error 1
```
這個error是在說**global_number**這個變數沒被宣告。要說為什麼的話，因為雖然我們有在extern_sub.c宣告並初始化，但要用到這變數的extern_main.c卻沒宣告。

在這種情況下，我們使用**extern**來解決。extern是想要用到其他檔案宣告的變數或函數時，「等等link的時候，有實體在喔」這樣指定的keyword。extern是像以下這樣宣告的:
```cpp
extern data_type 變數名;
```
所以我們在extern_main.c的**main()**函數之前，加上`extern int global_number;`即可順利通過compile，在螢幕上顯示出`global_number = 99`。

使用extern的話，即使source code被分成數個檔案，都可以把該變數當成全域變數使用。在寫大到某種程度的大程式的話，這是必須的東西，請記起來。

### scope是指看到的範圍

全域變數的全域，指的是**scope**。scope是指可以reference到特定名字的變數的範圍。全域變數就是指可以從程式的任何地方reference到，所以我們說全域變數的scope是全域。而這個scope，local變數跟其他的全域變數是有區別的; 稱之為「block」。雖然以前的C語言做不到，但是在C99，可以在程式的任何地方宣告變數。

```cpp
int main(){

	int i;
    i = 0;
    
    printf("i=%d\n",i);
    
    char c;
    c = 'A';
    
    printf("c=%c\n",c);
    
    return (0);
    
    
}
```
像這樣的程式，**main()**函數內**int i**的下個句子就可以使用變數**i**。但是，這時還無法使用變數**c**，要在**char c**的下個句子開始，才能開始使用變數**c**。

舉個例子，如果把**char c**用block圍起來，變數**c**就只在block中有效，在block之外是無法使用的。所謂block，是把一些句子用一個東西圍起來處理的單位，C語言是用**{}**圍起來。在這情況下，變數的scope只在block內，出了block就無法使用。比如說像下面:
```cpp
int main(){

	int i;
    i = 0;
    
    printf("i=%d\n",i);
    {
    	char c;
    	c = 'A';
    }
    printf("c=%c\n",c);
    
    return (0);
    
    
}
```
通常我們不會把{}用在把變數的scope給變窄，而是用在「函數」的分別上; 不過還是作為知識記起來吧。

### 加上const的話就無法更改

**const**是把初始化的值變得無法更動的指定子。這是用在我們想把它當作常數，不想讓它被程式改變時的情況利用。**const**指定的變數，在初始化以外的變更都會出現編譯錯誤。實驗看看吧。

```cpp
#include <stdio.h>

int main(){
	const char character = 'A';
	const int number = 1;

	printf("character is = %c\n", character);
	printf("number is = %d\n", number);

	character = 'B'; //compile error
	number = 2; //compile error
	
	return (0);
}
```
可以發現編譯時出現以下錯誤訊息:
```
const.c: In function ‘main’:
const.c:10:12: error: assignment of read-only variable ‘character’
  character = 'B'; //compile error
            ^
const.c:11:9: error: assignment of read-only variable ‘number’
  number = 2; //compile error
         ^
```
這些訊息代表「read-only(讀出專用)」的error。順帶一提，"hello, world"這種字串常數，被當作有加上const的變數處理。