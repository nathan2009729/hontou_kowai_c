### 變數是儲存用的
_ _ _

一開始也說明過了，電腦是把許許多多的值放在記憶體裡做情報處理。為了方便使用這些記憶體，C語言利用了變數這個概念。

電腦內部識別變數的方法是知道那個數值所在的address(位址)，但是拿來寫程式是太麻煩了: 每次儲存什麼值，都要指定記憶體的哪個地方來儲存什麼數值。

舉個例子，來考慮求出消費稅的計算。假設金額儲存在位址1000、稅率儲存在位址1004，那程式的內容就必須寫成「儲存在位址1000的值*儲存在位址1004的值」。不但很難理解，而且如果忘記是放在哪個位址，就會看不懂這成是在寫什麼。

在這裡，當我們把記憶體當作變數來使用的時候，我們會用一個固定的「名字」來區別。比如說金額取名成price, 稅率取名成tax的話，我們的程式就會寫成「price*tax」，比較好懂。

C語言的話，被宣告的記憶體位址可以作為變數利用。而變數的宣告就像以下的方式:

`data_type 變數名;`

比如說，在宣告某個儲存整數的變數number的時候，我們會寫成`int number;`，像這樣宣告的話，我們以後就可以使用叫做**number**的這個變數。如果有好幾個變數要宣告同一data type的話，我們用`,`來區隔: `int number_01, number_02;`。

我們來實行以下的範例程式吧。

**source code:**
*variable.c*
```c
#include<stdio.h>
int main(){
	int number;
    number = 12345;
    printf("number is %d\n", number);
    return (0);
}
```
**Makefile:**

第五章的時候，我們說明了gcc命令可以做出一連串compile的動作，但是這樣輸出的檔案名稱一定是a.out。還有，如果source code有複數個檔案的話會很麻煩，所以準備了Makefile並利用make命令。以下是Makefile的sample。

*Makefile.variable*
```makefile
PROGRAM   = variable
OBJS 	 = variable.o
SRCS	  = $(OBJS: %.o=%.c)
CC		= gcc
CFLAGS	= -g -Wall
LDFLAGS   =
$(PROGRAM):$(OBJS)
	$(CC) $(CFLAGS) $() -o $(PROGRAM) $(OBJS) $(LDLIBS)
```
Makefile有許多寫法，這裡用比較好懂的寫法。

>PROGRAM: 這裡寫的東西會成為執行檔的名字。
OBJS:要link的object file用空白分隔列舉出來。
SRCS:source code的檔案名，這裡的寫法意思是把OBJS填的名字的副檔名從.o改成.c(就是我們的source code的檔案名)
CC:c compiler的命令名
CFLAGS:指定compiler的option。這個例子用到了debug用的「-g」跟會發出警告的「-Wall」兩個option
LDFLAGS:這個例子沒有特別指定link特殊的library所以沒
$(PROGRAM): 記述程式的生成方法。程式是OBJS所指定的對象，程式的生成是tab後(最後一行)記述的。

順帶一提，Makefile會區分tab跟空白。如果最後一行前面($(CC)前面)不是用tab而是用空白的話會出現錯誤。

**build:**

第五章說明過，只是compile的話是生不出執行檔的，執行檔是經過compile->assemble->link一連串的作業才生出來的。而這一連串的作業，就稱為「build」。build是被make命令利用的，取名「makefile」或「Makefile」雖然也可以不用任何option就build成功，但是因為之後還要再放其他的source code，為了區別我們利用「-f」這個option來指定我們要build那一個file。以下是實行結果:
打`make -f Makefile.variable`，會出現

gcc -g -Wall   -c -o variable.o variable.c
gcc -g -Wall  -o variable variable.o 

這兩行。
`make`這個命令，遵循Makefile上寫的規則進行compile還有link。上述的例子，一開始經過preprocess, compile 還有assemble，生出variable.o，最後link生出variable這執行檔(windows是variable.exe)。

**執行結果:**

number is 12345

輸入`./variable`即可看到結果。`./`是指執行當前目錄下的執行檔，windows是不需要打這個的。

C語言準備了好幾種data type，整數的話**int**(integer的略稱)，文字的話**char**(character的略稱)，浮點數的話**float**(floating point的略稱)等等。

|      data type     |        說明       | 可表示的數值範圍                    |
|:------------------:|:-----------------:|:------------------------------------------------------:|
|        char        |    放1 byte整數   |                        -128~127                        |
|        short       |    放2 byte整數   |                    -32,768 ~ 32,767                    |
|         int        | 放2 or 4 byte整數 | -32,768 ~ 32,767 or -2,147,483,648 ~ 2,147,483,647     |
|        long        |    放4 byte整數   |             -2,147,483,648 ~ 2,147,483,647             |
|      long long     | 放8 byte整數(C99) | -9,223,372,036,854,775,808 ~ 9,223,372,036,854,775,807 |
|    unsigned char   |    放1 byte整數   |                         0 ~ 255                        |
|   unsigned short   |    放2 byte整數   |                        0 ~ 65535                       |
|    unsigned int    |    放4 byte整數   |                    0 ~ 4,294,967,295                   |
|    unsigned long   | 放4 byte整數      |      0 ~ 4,294,967,295(64 bit機器可放64bit正整數)      |
| unsigned long long | 放8 byte整數      |             0 ~ 18,446,744,073,709,551,615             |
|        float       | 放4 byte浮點數    |                    -3.4E38 ~ 3.4E38                    |
|       double       | 放8 byte浮點數    |                   -1.7E308 ~ 1.7E308                   |

C語言並沒有對data type的大小定義嚴密的規格，上面的表說到底就是一般環境的一個例子。比如說，以前的compiler的**int**幾乎都是16bit。

接下來執行看看可以表示這些data type的範例程式吧。

**source code:**
*limit.c*
```c
#include <stdio.h>

int main(){
    char char_min, char_max;
    short short_min, short_max;
    int int_min, int_max;
    long long long_long_min, long_long_max;
    unsigned char unsigned_char_min, unsigned_char_max;
    unsigned short unsigned_short_min, unsigned_short_max;
    unsigned int unsigned_int_min, unsigned_int_max;
    long long_min,long_max;
    unsigned long long unsigned_long_long_min, unsigned_long_long_max;
    float float_min, float_max;
    double double_min, double_max;
    
    char_min = 0x80;
    char_max = 0x7F;
    
    short_min = 0x800;
    short_max = 0x7FF;
    
    int_min = 0x80000000;
    int_max = 0x7FFFFFFF;
    
    long_long_min = 0x8000000000000000;
    long_long_max = 0x7FFFFFFFFFFFFFFF;
    
    unsigned_char_min = 0x00;
    unsigned_char_max = 0xFF;
    
    unsigned_short_min = 0x0000;
    unsigned_short_max = 0xFFFF;
    
    unsigned_int_min = 0x00000000;
    unsigned_int_max = 0xFFFFFFFF;
    
    long_min = 0x00000000;
    long_max = 0xFFFFFFFF;
    
    unsigned_long_long_min = 0x0000000000000000;
    unsigned_long_long_max = 0xFFFFFFFFFFFFFFFF;
    
    float_min = -3.402823e+38;
    float_max = 3.402823e+38;
    
    double_min = -1.797693e+308;
    double_max = 1.797693e+308;
    
    printf("char_min = %d, char_max = %d\n",char_min,char_max);
    printf("short_min = %d, short_max = %d\n",short_min,short_max);
    printf("int_min = %d, int_max = %d\n",int_min,int_max);
    printf("long_long_min = %lld, long_long_max = %lld\n",long_long_min,long_long_max);
    printf("unsigned_char_min = %u, unsigned_char_max = %u\n",unsigned_char_min,unsigned_char_max);
    printf("unsigned_short_min = %u, unsigned_short_max = %u\n",unsigned_short_min,unsigned_short_max);
    printf("unsigned_int_min = %u, unsigned_int_max = %u\n",unsigned_int_min,unsigned_int_max);
    printf("long_min = %ld, long_max = %ld\n",long_min,long_max);
    printf("unsigned_long_long_min = %llu, unsigned_long_long_max = %llu\n",unsigned_long_long_min,unsigned_long_long_max);
    printf("float_min = %f, float_max = %f\n",float_min,float_max);
    printf("double_min = %f, double_max = %f\n",double_min,double_max);
    return 0;
}

```

**執行結果:**
```
char_min = -128, char_max = 127
short_min = 2048, short_max = 2047
int_min = -2147483648, int_max = 2147483647
long_long_min = -9223372036854775808, long_long_max = 9223372036854775807
unsigned_char_min = 0, unsigned_char_max = 255
unsigned_short_min = 0, unsigned_short_max = 65535
unsigned_int_min = 0, unsigned_int_max = 4294967295
long_min = 0, long_max = 4294967295
unsigned_long_long_min = 0, unsigned_long_long_max = 18446744073709551615
float_min = -340282306073709652508363335590014353408.000000, float_max = 340282306073709652508363335590014353408.000000
double_min = -179769300000000004979913091153546431177385676945343486730197993498529636492108312404543796370045487218955201046137662191918513706549560771088224207924409275479864981823815660983343422176365744870072127934490865277449576937261468130920376085948653305075071243237207672347403131791038321491100101082182265602048.000000, double_max = 179769300000000004979913091153546431177385676945343486730197993498529636492108312404543796370045487218955201046137662191918513706549560771088224207924409275479864981823815660983343422176365744870072127934490865277449576937261468130920376085948653305075071243237207672347403131791038321491100101082182265602048.000000
```
**恐怖實驗:**
把上面程式的變數通通加1再編譯一次看看，可以發現編譯時出現以下警告:
```
limits_overflow.c: In function ‘main’:
limits_overflow.c:23:26: warning: integer overflow in expression [-Woverflow]
     int_max = 0x7FFFFFFF + 1;
                          ^
limits_overflow.c:26:40: warning: integer overflow in expression [-Woverflow]
     long_long_max = 0x7FFFFFFFFFFFFFFF + 1;
                                        ^
limits_overflow.c:29:25: warning: large integer implicitly truncated to unsigned type [-Woverflow]
     unsigned_char_max = 0xFF + 1;
                         ^
limits_overflow.c:32:26: warning: large integer implicitly truncated to unsigned type [-Woverflow]
     unsigned_short_max = 0xFFFF + 1;
                          ^
```

從這警告可以知道在**int**, **long long**, **unsigned char**, **unsigned short**都不是正確的值。實際執行後發現就算不在警告內的值都有問題。像是**char**, **short**, **unsigned int**, **long**, **float**, **double**的結果都有異常。比如說，**char_max**在還沒改程式前是127，+1以後應該是128，但是實際上表示成-128。

要說為何的話，**char**應該只能表示到127，我們卻把它+1。而且，C語言在這種overflow的場合下很多不會給警告，像這次警告的只有**int**, **long long**, **unsigned char**, **unsigned short**等4種type，但其他有問題的都沒警告。所以我們自己寫程式時要記得data type最多處理到哪個範圍。

### local變數沒有初始化

前節以前的實驗，宣告變數跟值的代入都是分別實行的(寫成兩行)。其實C語言也可以把這兩件事寫在一行。像下面這樣:

`data_type 變數名 = 初期值;`

比如說在variable.c中int的宣告以及初始化，可以寫成這樣:

`int int_min = 0x80000000, int_max = 0x7fffffff;`

**恐怖實驗:沒初始化的情況下垃圾data是哪裡來的?**

如果不初始化的話，那麼變數裡會放什值?用以下的範例程式跑跑看:

*no_init.c*
```cpp
#include <stdio.h>

int main(){
	char character;
	int number;

	printf("character is = %c\n", character);
	printf("number is = %d\n", number);

	return 0;
}
```
會發現出現的警告是
```
no_init.c:7:2: warning: ‘character’ is used uninitialized in this function [-Wuninitialized]
  printf("character is = %c\n", character);
  ^
no_init.c:8:2: warning: ‘number’ is used uninitialized in this function [-Wuninitialized]
  printf("number is = %d\n", number);

```
**執行結果:(ubuntu 16.04)**
```
character is = 
number is = 0
```
在各種linux發行版上的執行結果會不同，可知沒初期化的變數會放入什麼值是「不一定」的。如果把makefile中的CFLAGS = -g -Wall O2的話也會出現警告。利用沒初期化的變數寫出的程式，會不知道出現什麼奇怪的行為，要注意。

### 變數通常存在「stack」裡

為何沒初期化的變數的變數會有垃圾data呢?

這是因為變數通常是存在stack裡面的。

stack是電腦中非常重要的概念，最後輸入的data最先輸出，是stack的特徵，也被稱為一種data structure(資料結構)。具體的例子，可以想想看一張空桌子，有人往上面放書，而且書一定放在書的上面(第一本書除外)。可以知道，最後堆上去的書一定在最上面，這就是「最後輸入的data最先輸出」的概念。

實際的電腦，stack會確保在記憶體上，data 的位置則是利用一個叫stack pointer的特殊register來實現。

現代的電腦在函數的呼叫以及變數的確保都是利用stack來實現。要正確的說明很難，所以用了一個no_init.c的程式做了簡略的說明。

stack的動作

![stack的動作](http://p1.bqimg.com/567571/ac475881bbab5a1a.png)

概念上，把data往上堆的是stack，實際上是操作stack pointer的值創造出「空隙」來確保變數。如果變數不初始化只是確保空間的狀態下，它的值就是以前記憶體殘留的值，這就是「垃圾data」的真正身份。

變數在stack以外也有在一個叫「heap」的特殊記憶體領域被保存，知道宣告以後除了一部分的例外都會被存在stack就可以。

所謂一部分的例外，是指compiler在進行最佳化時，會特地把變數放在「register」上，而不是存取比較慢的記憶體上; register是CPU內部的高速記憶體。

還有，用**register**這個keyword來宣告變數的時候，是在指示compiler盡量把此變數存在register上。但說到底，只不過是「盡量」而已，並不是一定會存到register上。

像**register**這樣的keyword被稱為**storage class指定子**，就像下面這樣宣告:

`register int number;`