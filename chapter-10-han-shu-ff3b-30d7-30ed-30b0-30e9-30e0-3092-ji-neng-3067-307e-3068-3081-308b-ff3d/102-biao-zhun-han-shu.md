理解了函數的基本，接下來說明標準函數吧。所謂標準函數，是不管OS用什麼，都可以使用的C語言的函數，主要是輸入/輸出還有字串的操作。

### 字串的顯示 (printf)

字串的顯示，就是用目前為止的範例中出現的**printf()**函數。%d或%c之類的顯示指定，可能一開始覺得困惑，但範例程式常常用也習慣了吧。

雖然**printf()**函數是顯示字串的函數，但就如上段所說，有許多的顯示指定。**printf()**的第一個參數是指定「format」。format是指定就照著原樣顯示的字串、第二個參數之後的變數、或值，要怎樣顯示。

第一個參數是"hello, world\n"的話，就會直接在畫面顯示hello, world[換行]。"character is = %c\n"的話，%c的部份就是把給予下一個參數的變數或值用文字表示; 這個%c就被稱為「變換指定」。

目前為止的範例程式，%d或%c之類的，感覺就像是%後面加個字母而已的變換指定罷了，其實它還可以指定各式各樣細微的地方。變換指定可以分別指定以下所示的項目。

```cpp
%[flag][最小寬度][.精度][長度修飾子]變換指定子
```

[]都是可寫可不寫的東西，按照順序來書名這些項目吧。實際上是有優先順位的，去讀讀compiler的manual再做實驗實際執行一下吧。

#### flag

| flag | 說明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|:----:|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|   #  | 把值轉成「別的形式」。比如說變換指定子是o的話，就會把值用八進位表示，而在那個值的前面會加上0。 原因的話，因為不加0的會分不清是十進位還是八進位。在電腦的世界裡，開頭是0的話代表它是一個八進位數字。 比如說012就是八進位的12，以十進位來說是10。如果只寫12的話很容易被誤認為是十進位的12。為了不需要程式設計師因為 這個數值是八進位特別寫成「"number = 0%o"」，所以有了這個#。只要寫成"number = #%o"，就會在值的前面加上0。也有其 他的flag，不過因為不常用，請自行參照compiler的手冊。 |
|   0  | 字的數量不到最小寬度的情況下，會一直補0直到到達最小寬度為止。比如說，%04d就是這個變換指定有0這個flag，還有4這個指定最小寬度。如果用%04d顯示數字10，因為10只有兩位數，還要再補2個0變成4位數，所以會顯示成0010。                                                                                                                                                                                                                                                                        |
| 空白 | 字的數量不到最小寬度的情況下，會一直補空白直到到達最小寬度為止。                                                                                                                                                                                                                                                                                                                                                                                                                      |
|   -  | 把要顯示的東西往左對齊(通常是往右)                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|   +  | 常用來表示符號。通常正數就是直接顯示，但是有+記號的話就會連+一起表示                                                                                                                                                                                                                                                                                                                                                                                                                  |

#### 最小寬度

跟flag中的0還有空白一起使用。

| 變換指定 | 參數 | 表示 |
|:--------:|------|:------|
|   %04d   | 10   | 0010 |
|   % 4d   | 10   |   10 |
|    %d    | 10   | 10   |

#### 精度

寫在.的右邊，可以指定顯示的最少位數跟最大字串長。根據數值(%d)或字串(%s)的不同而有不同的表現。

| 變換指定 | 參數           | 表示  |
|:--------:|----------------|-------|
|   %.4d   | 900            | 0900  |
|   %.4d   | 1000           | 1000  |
|   %.5s   | "hello, world" | hello |

#### 長度修飾子

| 長度修飾子 | 說明                         |
|------------|------------------------------|
| hh         | 參數的data type是char        |
| h          | 參數的data type是short       |
| l          | 參數的data type是long        |
| ll         | 參數的data type是long long   |
| j          | 參數的data type是intmax_t    |
| z          | 參數的data type是size_t      |
| t          | 參數的data type是ptrdiff_t   |
| L          | 參數的data type是long double |

雖然有沒看過的data type，不過**size_t**明示了記憶體的大小所以常常使用。而**size_t**用**printf()**的%d顯示時會出現警告，在這情況下，就用%zu表示(u是下面才講的變換指定子)。另外，64bit的數字用%lld來表示(d是下面才講的變換指定子)。以上這兩個是第七章範例程式使用過的。

#### 變換指定子

| 變換指定子 | 說明                                                   |
|------------|--------------------------------------------------------|
| d          | 把參數的data加上符號並以10進位表示                     |
| i          | 把參數的data加上符號並以10進位表示                     |
| u          | 把參數的data不加符號並以10進位表示                     |
| o          | 把參數的data不加符號並以8進位表示                      |
| x          | 把參數的data不加符號並以16進位表示(英文字以小寫表示)   |
| X          | 把參數的data不加符號並以16進位表示(英文字以大寫表示)   |
| e          | 把參數的data以指數形式的浮動小數點表示(e以小寫表示)    |
| E          | 把參數的data以指數形式的浮動小數點表示(E以小寫表示)    |
| f          | 把參數的data以小數點形式的浮動小數點表示               |
| F          | 把參數的data以小數點形式的浮動小數點表示               |
| g          | 由系統決定是用e還是f比較適合                           |
| G          | 由系統決定是用E還是F比較適合                           |
| a          | 把參數的data以16進位的浮動小數點表示(英文字用小寫表示) |
| A          | 把參數的data以16進位的浮動小數點表示(英文字用大寫表示) |
| c          | 把參數的data以文字表示(參照ascii表)                    |
| s          | 把參數的data以字串表示                                 |
| p          | pointer的值                                            |
| n          |                                                        |
| %          | 印出%                                                  |

項目非常的多，多到會造成混亂，通常把dxfcs記起來就不會困擾了; 有必要的話再去參照compiler的manual。由於%被利用在變換指定的記號上，所以%是不會被印出來的，所以想在畫面上顯示%，需要兩個%連寫。範例如下:

```cpp
printf("tax = %f %%\n",tax);
```

這會顯示tax是多少%。

雖然已經用過許多次的**printf()**了，還是試試用不同的顯示指定來執行一個範例程式吧。

**source code**
*format.c*
```cpp
#include <stdio.h>

int main(){

	printf("%#o\n", 8);
	printf("%04d\n", 10);
	printf("% 4d\n", 10);
	printf("|%-4d|%-4d|\n|%4d|%4d|\n", 1, 2, 1, 2);

	printf("%+d\n", 10);
	printf("%+d\n", -10);

	printf("%.4d\n", 10);
	printf("%.5s\n", "hello, world\n");

	printf("%hhu\n", 255);
	printf("%hhu\n", 256);
	printf("%hhu\n", 257);

	printf("%e\n", 0.1);
	printf("%f\n", 0.1);

	printf("%%\n");

	return 0;
}
```
**執行結果:**
```
010
0010
  10
|1   |2   |
|   1|   2|
+10
-10
0010
hello
255
0
1
1.000000e-01
0.100000
%
```
有沒有感覺到**printf()**是一個能作到多采多姿表現的函數? 雖然沒必要知道全部，但可以做各種實驗來熟悉變換指定。

來一個附贈的情報，**printf()**函數可以作到「變換指定的變換」這種事。

比如說，format.c中的**printf("%04d\n", 10);**的4這個部份，可以作為被給予的參數。如果寫成**printf("%0\*d\n", 4, 10);**的話，意思就跟**printf("%04d\n", 10);**是一樣的。

應用這個技巧，**printf("|%*c|\n", 9, ' ');**可以用參數指定要有幾格空白。當然也可以用變數來當作參數，在只用文字顯示在畫面上的時候比較便利。

有關**printf()**函數的參數做了說明，而這個函數的返回值是什麼呢? **printf()**函數在顯示成功的狀態下會返回顯示的文字數。可能不是那麼好理解，就用範例程式說明一下吧。

**source code:**
*format_2.c*
```cpp
#include <stdio.h>

int main(){
	
	int return_val;
	
	return_val = printf("hello, world\n");
	printf("return_val is %d\n", return_val);

	//it's also ok
	printf("return_val is %d\n", printf("hello, world\n"));

	return 0; 
}
```
```
hello, world
return_val is 13
hello, world
return_val is 13
```
因為有換行，所以總共是13個字。如果發生錯誤的話，就會返回-1，不過**printf()**很少發生錯誤，所以不常看到。

#### printf()是奇怪的函數

這個**printf()**，其實是有點奇怪的函數。函數的話，應該在prototype宣告中宣告自己接收幾個參數，這些參數是哪些data type。但是把目前為止的範例程式重看一遍的話會發現**printf()**有時是一個參數、有時兩個、有時三個，應該可以注意到不同場合有不同數目的參數。

```cpp
printf("char_min = %d, char_max = %d\n", char_min, char_max);
第一個參數: "char_min = %d, char_max = %d\n"這個字串常數
第二個參數: 變數char_min
第三個參數: 變數char_max

printf("character is = %c\n", character);
第一個參數: "character is = %c\n"這個字串常數
第二個參數: 變數character

printf("\n");
第一個參數: "\n"這個字串常數
```

像這種參數數目根據不同情況變化的函數，我們稱為「可變參數函數」。在prototype的宣告中，有...的都是可變參數的函數。

```cpp
int printf(const char * __restrict, ...);
```
可變參數函數的寫法很難，本書在利用到的時候再介紹一下注意點。

**恐怖實驗: 搞錯printf()函數的參數數量**

搞錯printf()函數的參數數量會發生什麼事呢? 來執行看看下面的範例程式吧。這個範例程式雖然在字串中有四個參數，但是卻一個都沒指定。

**source code**
*format_3.c*
```cpp
#include <stdio.h>

int main(){
	printf("%s %s %s %s\n");

	return 0;
}
```
compile時會出現一些警告:

```
format_3.c: In function ‘main’:
format_3.c:4:9: warning: format ‘%s’ expects a matching ‘char *’ argument [-Wformat=]
  printf("%s %s %s %s\n");
         ^
format_3.c:4:9: warning: format ‘%s’ expects a matching ‘char *’ argument [-Wformat=]
format_3.c:4:9: warning: format ‘%s’ expects a matching ‘char *’ argument [-Wformat=]
format_3.c:4:9: warning: format ‘%s’ expects a matching ‘char *’ argument [-Wformat=]
```
**執行結果:**
```
�r��� �r��� (null) ��
```
雖然我們compile時有-Wall，在link時警告了我們參數不足，但還是沒有error的完成了link跟compile。原書作者執行程式的時候不但會跟筆者一樣印出莫名其妙的東西，還會顯示Segmentation fault (core dumped)強制結束執行。這是因為**printf()**參照了並沒有放入有效data的stack所造成的。

這就是C語言可怕的地方，即使是會造成致命結果的問題，文法上沒問題的話就只會給警告(如果是古老的compiler連警告都不給)。因為如此，常常寫的時候沒注意到，直到執行才發現有問題。字串特別容易有這錯誤，請注意。

### 字串的輸入

關於文字的輸入，第九章我們使用了**getchar()**函數。**getchar()**函數，是打1個字母跟Enter後，程式就會接收那個字母; 又或是什麼都不輸入直接按Enter後，程式就會把換行文字作為返回值接受。

非常的單純。那麼，要怎樣輸入字串才好呢?

#### 輸入字串 (scanf)

與**printf()**函數相反的**scanf()**函數，常常在入門書中介紹。**scanf()**函數，就如名稱中的f所示，是可以彈性的輸入各式各樣的寫法，是方便的函數，但是搞錯使用方法的話比**printf()**函數還危險。

比如說，使用**scanf()**函數來對**char one_string[14];**這個陣列進行輸入的話，用法如下:

```cpp
scanf("%s", one_string);
```
但是，這個方法在輸入字數超過陣列大小的情況下，程式會容易異常中止。關於迴避的方法，就要像下面這樣，在變換指定時就先寫入字串的最大值，其他應該考慮的點並不是初學者適合學的。

```cpp
scanf("%13s", one_string);
```
再來，**scanf()**函數跟**printf()**函數一樣可以使用變換指定，除了字串以外也可以輸入數值。但是，要使用的話，不徹底理解有C語言鬼門支撐的pointer的話是很難的，這裡先介紹**fgets()**函數的使用方法。

#### 安全輸入字串 (fgets, stream)

**fgets()**函數是從鍵盤輸入文字的函數，使用方法如下:

```cpp
fgets(char型的陣列名稱, 陣列大小, stdin);
```
說明一下陣列大小跟stdin這些沒見過的詞。第七章說明了C語言嚴格來說沒有表示字串的data type，而是利用文字的陣列來表示字串。所以我們需要給出可以容納這字串的陣列。

直覺上，文法應該像**變數名 = fgets()**這個樣子，不能這麼做的原因就是因為字串的data type是陣列。這原因在讀到第十二章的指標與陣列就會理解，總之先記起來需要把陣列作為參數就對了。

再來，陣列是任意元素排在一起的記憶體領域，所以需要指定大小(size)。最後的stdin，是表示「標準輸入」。所謂標準輸入，以一般的電腦來說就是鍵盤的輸入。

其實，**fgets()**函數是個從file(stream)讀入字串的函數。生出C語言的UNIX把許多的device(裝置)做抽象化，把device當作file。所以，指定了**stdin**這個file(stream)，就可以從鍵盤上讀取資料。

而被稱作「標準stream」的有**stdin**,**stdout**,**stderr**三種。**stdin**沒有特別的設定的話，就是從鍵盤上讀取字串，**stdout**跟**stderr**則是在consol上顯示字串。

雖然**printf()**函數可以用stdout來做文字的輸出，但是對file做文字輸出的**fprintf()**這個函數如果指定stream為stdout的話，就會跟**printf()**是一樣的結果。關於標準stream的詳細，請自行參照UNIX入門書，趕快來實驗字串的輸入吧。

**source code**
*input.c*
```cpp
#include <stdio.h>

int main(){

	char one_string [16];
	
	printf("input> ");

	fgets(one_string, 16, stdin);

	printf("Your input is %s", one_string);

	return (0);
}
```
**執行結果:**
```
input> hello, world
Your input is hello, world
```
輸入的文字被**fgets()**函數的下一行**printf()**函數顯示出來了。這次是用了**char**陣列16個元素，而字串則是如第七章所說最後有一個'\0'，輸入Enter時也輸入了'\n'。也就是說，這個範例程式實際上輸入了14個字，那輸入更長的字串會怎樣呢?

**執行結果:**
```
Your input is this is a very 
```
可以發現輸入被途中切斷了。如果不切斷的話，會有「buffer overflow」這個深刻的問題，不過這裡沒有這個問題。如果需要輸入更長的字串的話，就需要更多的陣列元素。

**恐怖實驗: 指定比實際的陣列更大的大小**

input.c這個範例程式，是把**char one_string [16]**交給**fgets()**，並在**fgets()**的參數寫16來利用這個陣列。那麼，**fgets()**的參數寫256會如何呢? 

input.c的**char one_string [16]**不變，**fgets()**改成**fgets(one_string, 256, stdin)**實驗看看吧。

**執行結果:**
```
input> hello, world
Your input is hello, world
```
```
input> this is a extremely very very long long string !!!
Your input is this is a extremely very very long long string !!!
*** stack smashing detected ***: ./input_modified terminated
已經終止 (core dumped)
```
可以知道雖然「hello, world」沒有問題，但是第二個輸入雖然完整顯示卻異常中止。這就是常造成安全問題的buffer overflow。實際上只確保了16個陣列元素，但是卻輸入48個字，多出來的字寫入了本來不應該寫的記憶體領域，造成了異常動作。
(圖)

這個問題可怕的地方在於，第一，compile時不會出現警告; 所以直到執行時error才發現它有問題。而且，因為環境、狀態等因素，error不是一定會發生的。在原書作者的電腦，即使輸入了「this is very long string !!!」這個稍微超出長度一點的字串是不會出現錯誤的(筆者電腦會)。

如果這個程式在通訊時進行字串的輸入，有惡意的人可能特地輸入一串非常長的字串來奪取電腦的控制權。雖然是非常危險所以需要注意的事，但是我們可能會type miss，或是變更了陣列的宣告卻忘記改函數裡的參數。

這種時候，就來利用第八章介紹過的**sizeof**運算元吧。**sizeof**運算元可以告訴我們變數的大小，所以不用寫**fgets(one_string, 16, stdin)**，可以寫成**fgets(one_string, sizeof(one_string), stdin)**，這樣即使type miss，至少也會compile error，而且在改變**one_string**的宣告時也會自動改變**fgets()**函數裡的值。

#### 把字串換成數值 (strtol)

介紹了輸入字串的方法，字串是**char**的陣列。假設我們需要輸入1234這種數字，對於變數來說還是像下面這樣的陣列:
(圖)

想進行計算的話，當然是可以處理整數的data type比較好，我們需要轉換的方法。而這種情況下，我們會用**strtol()**函數，用法如下:

```cpp
strtol(char的陣列名稱，最後文字的存放處，底數);
```

「最後文字的存放處」是在錯誤處理會用到的東西，不過沒學過指標會造成混亂，所以這次不用它，不用它的話，填上**NULL**就可以了。「底數」是可以指定我們輸入的陣列數值是8進位、10進位還是16進位。一般人輸入通常是10進位，所以這一格填10就可以。

想用**strtol**函數，就要include一個名為stdlib.h的head file。另外，向**strtol**函數傳送想轉換的字串後，轉換出來的數字會成為**long**型態的返回值。

#### 做簡易計算機

趕快來實驗看看範例程式吧，這程式也用到了第九章學到的東西，所以程式變得有些長，一個一個複習一定能理解的。

**source code**
*simple_cal.c*
```cpp
#include <stdio.h>
#include <stdlib.h>

int main(){

	char input_string[16];
	long a,b;
	long op;
	long answer;
	int c;

	do{

		printf("Input number> ");
		fgets(input_string, sizeof(input_string), stdin);
		a = strtol(input_string, NULL, 10);

		printf("Input number> ");
		fgets(input_string, sizeof(input_string), stdin);
		b = strtol(input_string, NULL, 10);

		printf("+---Operator menu---+\n");
		printf("|  1. +%*c|\n", 13, ' ');
		printf("|  2. -%*c|\n", 13, ' ');
		printf("|  3. *%*c|\n", 13, ' ');
		printf("|  4. /%*c|\n", 13, ' ');
		printf("+-------------------+\n");
		printf("Input operator number> ");
		fgets(input_string, sizeof(input_string), stdin);
		op = strtol(input_string, NULL, 10);

		switch(op){
			case 1:
				answer = a + b;
				break;
			case 2:
				answer = a - b;
				break;
			case 3:
				answer = a * b;
				break;
			case 4:
				if(b == 0){
					printf("divide by zero\n");
					continue;
				} else{
					answer = a / b;
				}
				break;
			default:
				printf("operator unknown\n");
				continue;
				//NOT REACHED
				break;
		}
		printf("answer is %ld\n", answer);

		printf("Input q and then Enter  to quit, Enter to continue\n");
		c = getchar();
	}while(c != 'q');

	return 0;
}
```
**執行結果:**
```
Input number> 1073741824
Input number> 536870912
+---Operator menu---+
|  1. +             |
|  2. -             |
|  3. *             |
|  4. /             |
+-------------------+
Input operator number> 4
answer is 2
Input q and then Enter  to quit, Enter to continue

Input number> 65535
Input number> 4
+---Operator menu---+
|  1. +             |
|  2. -             |
|  3. *             |
|  4. /             |
+-------------------+
Input operator number> 3
answer is 262140
Input q and then Enter  to quit, Enter to continue
q
```
這次的範例程式擴張了第九章的switch.c，做了一個簡易的對話式計算機。但是單單為了做兩個數的四則運算，就大張旗鼓的做了Operator menu; 以一個對話式程式來說很立派，可是沒什麼做的動力吧。

目前為止介紹的範例程式，因為沒有說明如何輸入字串或數值，所以一直都是直接把值寫死在source code中。比如說顯示質數的prime_3.c裡面，就寫了**end = 100**這個上限。如果利用**fgets()**跟**strtol()**的話，就可以把這些程式改造成對話式的東西，請一定要挑戰看看。

順代一提，simple_cal.c這個程式是有問題的，雖然以一個範例程式來說並不是需要太在意的問題就是了...。比如說，Input number> 輸入數字以外的東西不會出現error，而是會被當作0。還有，Input number> 輸入太長的字串則會有奇怪的動作。比如說我們輸入123456789012345678901234567890來看看會發生什麼

```
Input number> 123456789012345678901234567890
Input number> +---Operator menu---+
|  1. +             |
|  2. -             |
|  3. *             |
|  4. /             |
+-------------------+
Input operator number> operator unknown
Input number>
```
在原書作者的電腦上，則是還會出現answer = 4294967296這一行。總之，只輸入了一個數目程式就自己自動進行下去了。原因是我們輸入了陣列**input_string**塞不下的長字串，使得程式內部的記憶體殘留了那些放不下的字串。這樣的話，下一次的**fgets()**執行的時候會自動把那些殘留的字串當作輸入。

想防止的話，就需要「讀取捨棄」這個處理。像原書作者一樣出現4294967296這個結果的原因，就是「偶然」。因為我們沒有初始化變數**answer**，只不過**answer**裡面剛好裝了4294967296而已，大家的環境執行結果可能都不一樣。

致命的是，如果是BSD系的OS，如果在等待輸入的時候按了Ctrl+D的話，就會變成無窮迴圈。Ctrl+D的意思是不要再從**stdin**輸入東西了的意思，按了以後不管程式之後還有多少**fgets()**跟**getchar()**都不能再輸入了。

想對應這種情況，只是決定C語言的標準是不夠的，還需要OS的配合。像這樣，伴著輸入，程式要考慮的事很多，非常麻煩。為了對應以上這些情況，還需要一些本章以上的知識。作為參考把對應這些問題的版本寫在下面，學到後面之後再回頭來看這程式，應該會有「程式的錯誤處理很麻煩!」這樣的實感。

**source code**
*simple_cal_2.c*
```cpp
#include <stdio.h>
#include <stdlib.h>
#include <errno.h>
#include <limits.h>

int input_number_from_console(const char *prompt, const char *err_msg, long *number){
	char input_string[16];
	char *endptr;

	if(number == NULL){
		printf("Error.\n");
		return (-1);
	}

	printf("%s", prompt);

	if(fgets(input_string, sizeof(input_string), stdin) == NULL){
		//if no this, bsd will infinite loop
		fseek(stdin, 0, SEEK_END);
		printf("%s", err_msg);
		return (-1);
	}

	errno = 0;

	*number = strtol(input_string, &endptr, 10);
	if(errno != 0 || *endptr != '\n' || 
			(*number < LONG_MIN || LONG_MAX < *number)){
			fseek(stdin, 0, SEEK_END);
			printf("%s", err_msg);
			return (-1);
	}

	return 0;
}

int main(){

	long a,b;
	long op;
	long answer;
	int c;
	do{
		answer = 0;

		if(input_number_from_console("Input number> ",
					"\nInput error.\n",
					&a) == -1){
			continue;
		}

		if(input_number_from_console("Input number> ",
					"\nInput error.\n",
					&b) == -1){
			continue;
		}

		printf("+---Operator menu---+\n");
		printf("|  1. +%*c|\n", 13, ' ');
		printf("|  2. -%*c|\n", 13, ' ');
		printf("|  3. *%*c|\n", 13, ' ');
		printf("|  4. /%*c|\n", 13, ' ');
		printf("+-------------------+\n");

		if(input_number_from_console("Input number> ",
					"\nInput error.\n",
					&op) == -1){
			continue;
		}

		switch(op){
			case 1:
				answer = a + b;
				break;
			case 2:
				answer = a - b;
				break;
			case 3:
				answer = a * b;
				break;
			case 4:
				if(b == 0){
					printf("divide by zero\n");
					continue;
				} else{
					answer = a / b;
				}
				break;
			default:
				printf("operator unknown\n");
				continue;
				//NOT REACHED
				break;
		}
		printf("answer is %ld\n", answer);

		printf("Input q and then Enter  to quit, Enter to continue\n");
		c = getchar();
		if(c == EOF){
			// if no this, bsd will loop inifinitly
			fseek(stdin, 0, SEEK_END);
		}
	}while(c != 'q');

	return 0;
}
```
### 時間的取得 (time, ctime)

學會了輸出/入後，應該某種程度上可以寫出各種類型的程式了。如果可以處理時間的話，能寫的程式就更多了。從這一節開始要處理的data type又變多了，函數的使用方式就像實際的manual寫的一樣，先介紹它的prototype宣告。

首先，是可以從電腦內藏時鐘取得現在時間的**time()**函數。用法如下:

```cpp
time_t time(time_t *tloc);
```
執行**time()**函數後，它會用**time_t**這個data type把現在的時刻當作返回值。其實，也可以對參數傳入**time_t**變數的pointer，不過我們還沒學pointer，這次就得到作為函數返回值的時間就好了。

對於要求pointer的參數，如果什麼都不想傳給它的話，就用**NULL**這個表示什麼都沒有的值就可以了。**time_t**這個data type，大部分都是處理**long**型態的整數，不過也不一定是這樣，必要的時候就好好的宣告它是**time_t**。

**time()**函數跟**time_t**這個data type都被定義在time.h這個head file，所以要用的話要先`#include <time.h>`。

像以下這樣用的話，變數**now**就可以拿到現在的時刻。

```cpp
time_t now;
now = time(NULL);
```

讓我們來執行表示現在時刻的範例程式吧。

**source code**
*now.c*
```cpp
#include <stdio.h>
#include <time.h>

int main(){

	time_t now;

	now = time(NULL);

	printf("now %ld\n", now);

	return 0;
}
```
**執行結果:**
```
now 1477564133
```

雖然想說無事的表示了現在的時刻，但是卻輸出了看不懂的數字。這個數字是一般的UNIX的OS從1970/1/1開始經過的秒數。我們來算一下，1年是31536000秒，1477564133/31536000 = 46.8，今年是2016年，1970 + 46 = 2016，符合吧。

#### 計算程式執行時間的範例程式

雖然不是人一看就可以清楚的形式很遺憾，不過我們可以利用這個機能來計算程式的執行時間。我們挑一個要花比較多時間的程式- -第九章的pi.c，改造這個程式來讓我們知道這程式花多少時間。

**source code**
*pi_2.c*
```cpp
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

int main(){

	double x, y, insect_cnt;
	int i;
	time_t t1,t2;//new

	insect_cnt = 0.0;
	i = 0;

	srand(time(NULL));

	t1 = time(NULL); //new

	while(i < 100000000){ //>
		x = (double) rand() / RAND_MAX; //x get a random number of 0~1
		y = (double) rand() / RAND_MAX;
		if(x * x + y * y < 1.0){
			//>
			insect_cnt = insect_cnt + 1.0;
		}
		i = i + 1;
	}

	t2 = time(NULL); //new

	// insect_cnt / i = probability of x^2 + y^2 = 1.0 
	//*4 = 1 cycle
	printf("%f\n", insect_cnt / i * 4.0);
	
	printf("process time = %ld sec\n", t2 - t1);//new

	return 0;
}
```
**執行結果:**
```
3.142004
process time = 2 sec
```
以上結果是筆者i5-6xxx的cpu上的結果，另外，source code上有`//new`註解的句子是pi_2.c新增的。

要注意，UNIX系的OS，time_t就是從1970/1/1開始經過的秒數。但是這不是C語言的規定，在不同的OS上可能會無法顯示秒。

現在來談談**ctime()**函數吧。剛剛的執行時間計算的很開心，果然人是想要一看就知道的時間的。這種時候，就要用**ctime()**函數了。prototype如下:

```cpp
char *ctime(const time_t *clock);
```

想確實理解**ctime()**函數，就有知道pointer的必要，不過不學pointer就不能用這函數實在太悲傷了，就只介紹它的使用方法吧。來執行下面的範例程式吧。

**source code**
*now_2.c*
```cpp
#include <stdio.h>
#include <time.h>

int main(){

	time_t now;

	now = time(NULL);

	printf("now %s", ctime(&now));

	return 0;
}
```
**執行結果:**
```
now Thu Oct 27 19:06:18 2016
```
程式表示了月曆形式的時間情報。這個函數如果用在寫「multithread」這種程式的時候，是有些問題的。**ctime()**函數把作為字串的時間情報做處理，向caller返回處理過的月曆形式的時間情報，而函數內部利用的是靜態變數。

關於靜態變數，請參照第七章「靜態變數就是靜靜的一直在那」的說明。「multithread」這種東西，就是在同一程式(process)中同時進行複數的處理。在這時候，雖然沒有共用stack，但共有全域變數還有靜態變數。

如果在這複數的處理中同時呼叫**ctime()**函數的話，因為同時對一個靜態變數做寫入的話，可能會出現跟意圖不同的結果。如果想在multithread程式用到**ctime()**的話，可以用**ctime_r()**這個函數。multithread跟**ctime_r()**不是C語言的標準，超出了本書的範圍，如果以後要寫multithread程式的話，請想起這些事吧。

### 使用亂數 (rand, srand)

**亂數**是沒有規則性，出現機率相等的數字。比如說，擲骰子就是亂數，沒法從現在出現的點數預測下次出現的點數。亂數在第九章的dice.c有提過，但是沒有好好說明，就在這裡說明一下。

要使用亂數的話，就要使用**rand()**函數。prototype如下:

```cpp
int rand(void)
```

**void**就是不需要參數的意思，而電腦所產生的亂數，並不是真正的亂數，只是偽亂數而已。這是因為電腦只不過是依照程式指定的動作去做，換句話說，最終也只是依照某種規則產生數字罷了。

但是，如果可以簡單的預測的話就不叫亂數了，為了看起來很不規則，需要做一些初始化。而進行這個初始化的，是**srand()**函數。

```cpp
void srand(unsigned seed)
```
這個函數的返回值是**void**，表示這個函數並沒有返回值。以上這兩個函數，都定義在stdlib.h這個head file裡，pi.c跟dice.c都有include到。看pi.c還有dice.c就知道，我們把**time()**函數的結果當作**seed**，也就是**srand**的參數。原因的話，因為每個瞬間都是不會再出現的時間，所以可以看起來比較像亂數(其實如果調整電腦時間的話是可以再現同樣時間的，這裡說的是一般的情況)。所以我們通常如下這樣用:

```cpp
srand(time(NULL));
```
由**rand()**產生出來的亂數，會在0~**RAND_MAX**這個範圍之間，**RAND_MAX**通常是2147483647(根據系統而有不同)。知道了這些事，再重新看pi.c，應該就可以理解下面這一行:

```cpp
x = (double) rand() / RAND_MAX;
```
意思就是把**rand()**出來的數字(0~**RAND_MAX**之間)除以**RAND_MAX**。假設**rand()**的結果是3好了，那麼3/2147483647 = 0.00000000139698，是個0~1之間的亂數。另外，**(double)**被稱作「轉型(cast)」，因為**rand()**本來的結果是**int**型，我們想把它當成double型來處理。

那麼我們再來看dice.c的**rand()**的使用方式吧。

```cpp
rand() % 6 + 1;
```
這行是先求出**rand()**除以6出來的餘數，所以一定介於0~5之間。把這餘數再+ 1，就會得到1~6之間的亂數。亂數在數值計算還有遊戲中都很常使用，請記起來。

**恐怖實驗: 實際體驗偽亂數**

剛剛說明了**rand()**出來的數字是偽亂數。但是dice.c在執行時每次都會跑吃不同的結果，讓人感受不到是假的。所以我們來執行一個可以感覺到**rand()**是偽亂數的範例程式吧。source code跟dice.c幾乎一樣，但是**srand()**接收的參數不是**time()**的返回值，而是0這個數字。

**source code**
*pseudorandom*
```cpp
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

int main(){
	int c;

	srand(0);

	do{
		printf("%d\n", rand() % 6 + 1);
		//get a random number between 1 to 6
		c = getchar();
		//get a character from keyboard
	}while(c != 'q');//if input is q, quit

	return 0;
}
```
在筆者的電腦上，每一次執行都會是"2542625142323265115......"等亂數。這是因為srand()函數每次執行都交給rand()一樣的結果。也就是說，只要清楚是用什麼OS什麼compiler，就可以預測結果。所以在暗號處理等等需要用到亂數的時候，會使用更高性能產生亂數的函數，或者是利用硬體亂數產生器，它可以檢測周圍放射線來生成不能預測的亂數。

### 不能使用標準函數的情況

關於函數本身，我們學了輸出/入、時間、亂數等等標準函數的用法。目前登場過得**peintf()**,**fgets()**,**rand()**,**time()**等等都叫做「標準C library」，幾乎所有的c compiler都可以用，C99的規格也包含了這些。但是即使是滿足C99的環境，也有可能不能用這些東西; 這情況出現在利用C語言在「Freestanding environment」上開發的時候。所謂Freestanding environment，比如Linux kernel，以及一些在没有OS的裸板上的程式，總之就是不在OS控制下的環境。

假設我們要做加熱器的C語言控制程式吧。因為成本要很便宜，所以記憶體或外部記憶裝置(大部分的場合是ROM)，只有最小的容量。在這情況下，因為容量太少不足以裝個OS，所以會是Freestanding environment。相對來說，有OS的話就叫做「host environment」。在host environment底下可以使用C99對應的compiler，一定可以使用介紹的C標準library，但是Freestanding environment能使用的有限。C99可以利用且被規定的C標準library，只有以下幾種:

* float.h
* limits.h
* stdarg.h
* stddef.h
* iso646.h
* stdbool.h
* stdint.h

這些東西裡面只有定義data type跟常數，**printf()**跟**fgets()**都不包含在裡面。不過考慮到剛剛加熱器的例子，它沒有螢幕跟鍵盤，所以不實裝**printf()**跟**fgets()**也是沒辦法的吧 (如果加熱器有裝可以顯示幾個位數的液晶還有按鈕的話，可能也有實裝類似的函數，不過符合C99標準的**printf()**是沒有的)。

因為如此，如果一直有「標準C library是C99的一部分，什麼時候都可以用」的想法的話，可能會踢到鐵板。比如說，在秋葉原買到了「可以用C語言開發! 微電腦套件」的微電腦好了，這種微電腦，可能沒辦法用**printf()**。即使附了專用的液晶，也需要用到那台微電腦專屬的命令。

雖然標準函數確實是C語言的一部分，但是C語言的本質只有文法與制御文。就算是host environment，在現在GUI環境當道下，也是有專用的函數而不太能用標準C library。這個時候，就需要C語言+OS還有環境的知識。這就是C語言難的地方，像Java這種程式語言連同環境包在一起才稱作Java，只要記住一個環境就可以通用全部。但是，C語言不是這樣，記住了C語言的基本，接下來還要深入理解自己的開發環境才行。

(筆者注:不管哪個作業系統，安裝Java時會一起安裝可以讓Java運行的環境(JVM)，Java程式語言+JVM才是完整的Java，所以不管環境是什麼，寫Java都是一樣的)

### 使用現代風格的便利機能

要說Java還有LL跟C語言的不同之處，可以舉出C語言沒有container跟collection class這些點。collection class的概念是把共通的操作方法用抽象的data type來處理。雖然C語言有字串跟陣列等等，但是collection的字串跟陣列是比C語言的還要高機能。

比如說，Java的字串跟陣列的變數自身，就可以操作、參照變數的內部。像是要知道**one_string**這個字串(C語言是**char**陣列，Java則是有字串這個data type)的長度，Java只要執行**one_string.length()**就可以了; 而C語言要自己另外寫個函數來知道長度。因為C語言是**char**陣列，字串的最後一定會放入'\0'。也就是說，要知道字串長度的話會寫成像下面的範例程式一樣:

**source code**
*string_length.c*
```cpp
#include <stdio.h>

int main(){

	char one_string[16] = "hello, world\n";
	int length;

	length = 0;
	while(one_string[length] != 0){
		length = length + 1;
	}

	printf("lenght is %d\n", length);

	return 0;
}
```
**執行結果:**
```
length is 13
```

字串以外還有對data的許多操作，如果使用collection的話都可以輕易實現，但是沒有這種標準的C語言，許多操作要程式員自己寫程式達成，所以除了C語言文法以外，還需要知道演算法(algorithm)的知識，這也是C語言難的地方。請在了解本書的C語言基礎後，好好的學演算法。雖然很麻煩，但是這樣即使用C語言也可以寫出各式各樣的程式。

(筆者注:想計算字串長度的話，有string.h的strlen函數可用。)