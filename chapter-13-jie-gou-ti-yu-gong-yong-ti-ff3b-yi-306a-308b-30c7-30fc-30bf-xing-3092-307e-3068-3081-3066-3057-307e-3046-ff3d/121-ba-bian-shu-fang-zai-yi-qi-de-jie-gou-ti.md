### 何謂結構

要進行複雜的處理的話，要傳給函數的參數就增加了。在範例程式中，函數的參數只有一個兩個是還好，但是參數的數量會越增加越容易搞錯，會連哪個參數是代表什麼資料都不太清楚。

比如說現在有一個計算座標的函數如下，這函數是接受x座標y座標，經過一些計算後返把結果當返回值。

```cpp
void func(double x, double y, double *ans_x, double *ans_y){
	//以x跟y做計算
}
```
像這種程度的話還是允許範圍吧。

那麼，接下來考慮看看保存*住所錄*資料的函數吧。住所錄一般會處理以下項目:

* 管理號碼
* 姓
* 名
* 姓(假名)
* 名(假名)
* 郵件號碼
* 住所1
* 住所2
* 電話號碼
* 傳真號碼
* 手機號碼
* 附註

如果要紀錄企業相關的住所錄的話，項目會變得更多; 總之就以上這些，考慮把這些項目保存成一個資料的函數吧。如下:

```cpp
int save_to_file(int no, char *last_name, char *first_name, char *last_name_furigana, char *first_name_furigana, int post_code, char *address_1, char *address_2, char *phone_no, char *fax_no, char *cellular_no, char *remarks){

	//保存file失敗 return (-1)
    //保存file成功 return (0)
}
```
這個函數的參數就太多了，變得搞不清什麼參數是代表什麼。就算不是要傳給函數，光是在程式內處理這些資料，就要像下面這樣保存各式各樣的陣列，非常的不好讀。來看看下面的宣告，假設想保存1024個住所錄，住所錄裡的每一種資料都可以容納256字。

```cpp
#define MAX_CHARACTER (256)
#define MAX_DATA (1024)

int no[MAX_DATA];
char last_name[MAX_DATA][MAX_CHARACTER];
char first_name[MAX_DATA][MAX_CHARACTER];
char last_name_furigana[MAX_DATA][MAX_CHARACTER];
char first_name_furigana[MAX_DATA][MAX_CHARACTER];
int post_code[MAX_DATA];
char address_1[MAX_DATA][MAX_CHARACTER];
char address_2[MAX_DATA][MAX_CHARACTER];
char phone_no[MAX_DATA][MAX_CHARACTER];
char fax_no[MAX_DATA][MAX_CHARACTER];
char cellular_no[MAX_DATA][MAX_CHARACTER];
char remarks[MAX_DATA][MAX_CHARACTER];
```
像這些為了描述一件事而衍生的資料，把它們定義成一個data type會比較輕鬆。能作到這件事的，就是**結構體**。要宣告結構體，首先要定義它要處理怎樣的資料(data type)，要用到時再去實際宣告使用這個結構體的變數。定義它要處理怎樣的資料的方式如下:

```cpp
struct 結構體tag名稱 {member變數,...};
```
實際宣告變數的方式:
```cpp
struct 結構體tag名稱 變數名;
```
比方說剛剛提到的計算座標，就可以像下面這樣寫:
```cpp
struct tag_coord {
	double x;
    double y;
};//definition

struct tag_coord coord;//the variable use tag_coord structure
```
住所錄的例子用結構來寫就像下面這樣:

```cpp
#define MAX_CHARACTER (256)
#define MAX_DATA (1024)

struct tag_address{
	int no;
    char last_name[MAX_CHARACTER];
	char first_name[MAX_CHARACTER];
	char last_name_furigana[MAX_CHARACTER];
	char first_name_furigana[MAX_CHARACTER];
	int post_code;
	char address_1[MAX_CHARACTER];
	char address_2[MAX_CHARACTER];
	char phone_no[MAX_CHARACTER];
	char fax_no[MAX_CHARACTER];
	char cellular_no[MAX_CHARACTER];
	char remarks[MAX_CHARACTER];
};

struct tag_address address[MAX_DATA];
```
看起來就比較清爽了。

那麼，這個結構體的資料要怎麼使用呢? 答案是使用**.**運算元。先來看看範例程式吧。

**source code**
*struct.c*
```cpp
#include <stdio.h>

struct tag_coord{
	double x;
	double y;
};

int main(){

	struct tag_coord coord;
	coord.x = 1.00;
	coord.y = 2.00;

	printf("coord.x = %f\n", coord.x);
	printf("coord.y = %f\n", coord.y);

	return 0;
}
```
**執行結果:**
```
coord.x = 1.000000
coord.y = 2.000000
```
因為是簡單的記述，所以不會有什麼迷惑的地方吧。如果我們把結構體宣告成陣列，比如說**struct tag_coord coord[8];**，那麼也可以存取**coord[0].x**。這個結構體裡的變數，稱之為**成員**。

### 結構體跟函數的關係

雖然可以很簡單的利用結構體，但是要注意結構體在函數間的傳遞比較不一樣。傳遞普通的變數，是把caller中的值複製到callee，如下圖:

```cpp
int sum(int x, int y){
	//the x,y here are cpoied form main()
}
int main(){
	int ans;
    
    ans = sum(1,2);
}
```
傳遞指標的話，callee的變數會指向caller中變數的位址，如下圖:
```cpp
int sum(int *x, int *y){
	//the x,y here are the address of x,y in main()
}
int main(){
	int x,y;
	int ans;
    
    ans = sum(&x,&y);
}
```
如果是陣列的話，一定是傳遞位址，也就是傳遞指標。
```cpp
int print_message(char *msg){
	/*the msg here is the first address of meaasge[] defined
      in main()*/
}
int main(){
	char message[] = "Message!";
    print_message(message);
}
```
第十二章的「指標與陣列」中，說過因為陣列太大所以只能傳遞指標; 那麼如果結構體的成員很多會如何呢? 其實，跟普通變數一樣，兼具直接複製或是只傳指標兩種。和陣列只能傳遞指標是大不相同的。趕快來試試範例程式吧。

**source code**
*struct_2.c*
```cpp
#include <stdio.h>

struct tag_coord{
	double x;
	double y;
};

void print_coordinates(struct tag_coord);

void print_coordinates(struct tag_coord c){

	printf("c.x = %f\n", c.x);
	printf("c.y = %f\n", c.y);

	c.x = 12345.00;
	c.y = 54321.00;
}

int main(){

	struct tag_coord coord;

	coord.x = 1.00;
	coord.y = 2.00;

	print_coordinates(coord);


	printf("coord.x = %f\n", coord.x);
	printf("coord.y = %f\n", coord.y);

	return 0;
}
```
**執行結果:**
```
c.x = 1.000000
c.y = 2.000000
coord.x = 1.000000
coord.y = 2.000000
```
利用print_coordinates函數把值表示在畫面上後，再用別的數值代入結構體的成員變數，但是用**main()**再度表示的時候，值卻沒有改變。這就確定了，結構體在傳給函數時是用複製的方式。

但是，結構體在返回值是有些特殊的。陣列不管在傳遞或是返回，都只能是指標的形式。如果希望一個函數返回陣列，就要像**char *function(void)**這樣去宣告，讓它返回一個指標。不能宣告像是**char []function(void)**這樣的東西: 不能宣告一個可以複製陣列出來當作返回值的函數，也沒有這樣的機能。

所以第七章的auto-var.c才會出現奇怪的結果: 這是因為被呼叫的函數，它的區域變數被破壞了。

但是，結構體是可以直接返回實體的。來實驗看看吧。
**source code**
*struct_3.c*
```cpp
#include <stdio.h>

const char *string_literal = "hello, world";

struct tag_string{
	char one_string[16];
};

//prototype: pointer ver.
struct tag_string *get_stringP(void);
//prototype: 
struct tag_string get_stringR(void);

//function body: pointer ver.
struct tag_string *get_stringP(void){

	struct tag_string a;
	int i;

	for(i = 0; i < sizeof(a.one_string) - 1 &&  
		string_literal[i] != 0; i++){
		a.one_string[i] = string_literal[i];
	}
	a.one_string[i] = '\0';

	return (&a);
}

//function body
struct tag_string get_stringR(void){

	struct tag_string a;
	int i;

	for(i = 0; i < sizeof(a.one_string) - 1 && 
		string_literal[i] != '\0'; i++ ){
		a.one_string[i] = string_literal[i];
	}
	a.one_string[i] = '\0';

	return a;
}

int main(){

	struct tag_string *a;
	struct tag_string b;

	a = get_stringP();
	b = get_stringR();

	printf("pointer %s\n", a->one_string);
	printf("real    %s\n", b.one_string);

	return 0;
}
```
**執行結果:**
```
pointer (null)
real    hello, world
```
**a->one_string**是指標為了參照構造體內的成員的運算元(跟寫(\*a).one_string是一樣的)，下一節會詳細說明。指標的版本在compile時就出現警告了，這是因為**get_stringP()**返回了自身定義的變數的位址，但是在函數執行結束時值就被破壞了，所以程式執行時不會出現結果; 但是實體版就不會有這問題，這是因為我們把結構體本身複製過去。

### 結構體跟指標的關係

結構體也是一種使用記憶體的變數，所以也可以取得它的位址放進指標變數裡，再把指標傳給函數。只是，如果透過指標使用結構體的話，存取成員變數的方法就改變了。結構體可以利用.運算元來存取成員變數，如果是用結構體的指標變數來間接存取的時候是用->運算元。

**source code**
*struct_4.c*
```cpp
#include <stdio.h>

struct tag_coord{
	double x;
	double y;
};

void print_coordinates(struct tag_coord*);

void print_coordinates(struct tag_coord *c){

	printf("c->x = %f\n", c->x);
	printf("c->y = %f\n", c->y);

	c->x = 12345.00;
	c->y = 54321.00;
}

int main(){

	struct tag_coord coord;

	coord.x = 1.00;
	coord.y = 2.00;

	print_coordinates(&coord);


	printf("coord.x = %f\n", coord.x);
	printf("coord.y = %f\n", coord.y);

	return 0;
}
```
**執行結果:**
```
c->x = 1.000000
c->y = 2.000000
coord.x = 12345.000000
coord.y = 54321.000000
```
這個範例程式也是利用**print_coordinates()**函數來把值替換。這一次所傳遞的是指標，所以返回**main()**的值的確是改變了。

這個->就像*(double_array + 1)等價於double_array[1]一樣，c->x等價於(*c).x。可以改造一下範例程式，看看c->x改成(*c).x結果是不是相同。

### 結構體跟陣列的關係

就算以陣列的方式宣告了結構體，可以存取像是**coord[0].x**之類的東西，但還是要注意函數間的傳遞。要注意是因為，結構體可以像普通變數一樣複製值給函數處理，也可以用指標傳過去，但是陣列就只能用指標了。

這個時候，當然無法複製值，再加上callee可以變更值，所以可能會對原來構造產生影響，這是需要注意的。

來實驗看看範例程式吧。
**source code**
*struct_5.c*
```cpp
#include <stdio.h>

struct tag_coord{
	double x;
	double y;
};

void print_coordinates(struct tag_coord [], int);

void print_coordinates(struct tag_coord c[], int num){

	int i;

	for(i = 0; i < num; i++){

		printf("c[%d]->x = %f\n", i, c[i].x);
		printf("c[%d]->y = %f\n", i, c[i].y);

		c[i].x = 12345.00;
		c[i].y = 54321.00;
	}
}

int main(){

	int i;
	struct tag_coord coord[4];

	coord[0].x = 1.00;
	coord[0].y = 2.00;
	coord[1].x = 3.00;
	coord[1].y = 4.00;
	coord[2].x = 5.00;
	coord[2].y = 6.00;
	coord[3].x = 7.00;
	coord[3].y = 8.00;

	print_coordinates(coord, 4);

	for(i = 0; i < 4; i++){
		printf("coord[%d]->x = %f\n", i, coord[i].x);
		printf("coord[%d]->y = %f\n", i, coord[i].y);
	}

	return 0;
}
```
**執行結果:**
```
c[0]->x = 1.000000
c[0]->y = 2.000000
c[1]->x = 3.000000
c[1]->y = 4.000000
c[2]->x = 5.000000
c[2]->y = 6.000000
c[3]->x = 7.000000
c[3]->y = 8.000000
coord[0]->x = 12345.000000
coord[0]->y = 54321.000000
coord[1]->x = 12345.000000
coord[1]->y = 54321.000000
coord[2]->x = 12345.000000
coord[2]->y = 54321.000000
coord[3]->x = 12345.000000
coord[3]->y = 54321.000000
```
因為傳遞的是指標，所以在**main()**函數表示的時候，值也好好的被替換了。在**print_coordinates()**的函數裡面因為使用了**[]**，所以不是用**->**運算元，要注意一下。

雖然扯到了指標變得有些囉唆，但是這是一定要知道的事情，只能習慣並記住了。多做幾次實驗來習慣吧。

### 要注意alignment的理由

關於使用結構體的方法已經詳細的說明了，大意上就是把許多變數包成一個而已所以也不是很難。存取成員變數的方法有好幾種，也是要習慣; 而這一節是要介紹處理結構體需要注意的地方。

因為結構體是把複數的變數當作一個來用，當然它所需要的記憶體就會比較大。目前為止所使用的**struct tag_coord**結構體，裡面有2個**double**型的成員變數，那它自然就是2個**double**變數的大小。

想知道變數的大小，就利用**sizeof**運算元吧。先用範例程式做實證吧。

**source code**
*struct_6.c*
```cpp
#include <stdio.h>

struct tag_coord{
	double x;
	double y;
};

int main(){

	double num;
	double num_array[4];
	struct tag_coord coord;
	struct tag_coord coord_array[4];

	printf("size of num is %zu\n", sizeof num);
	printf("size of num_array is %zu\n", sizeof num_array);
	printf("size of coord is %zu\n", sizeof coord);
	printf("size of cooord_array is %zu\n", sizeof coord_array);

	return 0;
}
```
**執行結果:**
```
size of num is 8
size of num_array is 32
size of coord is 16
size of cooord_array is 64
```
不管是32還是64位元的電腦，在大部份的場合**double**都是8 bytes。所以執行的結果**num**是8 bytes，**num_array[]**因為是4個元素的**double**陣列所以是32 bytes。

**struct tag_coord**這個結構體，因為成員變數是2個**double**，所以是16 bytes，**coord_array[]**因為是4個元素的**struct tag_coord**型態的陣列，所以是64 bytes。

目前為止都是跟預想一樣吧。那麼，像下面的話結構體的大小是多少呢?

```cpp
struct tag_question{
	char character;
	double number;
};
```
來實際執行看看範例程式吧。
**source code**
*struct_7.c*
```cpp
#include <stdio.h>

int main(){
	struct tag_question{
		char character;
		double number;
	};

	printf("size of question is %zu\n", sizeof(struct tag_question));

	return 0;
}
```
**執行結果:**
```
size of question is 16
```
竟然被表示成16了(有些人的電腦可能是12)。因為是1個**char**型還有1個**double**型，所以很多人會猜9吧，那為什麼是16呢?

通常CPU從記憶體讀出資料的時候，並不是一次只讀1 byte，而是一口氣讀出4 bytes(32 bit電腦)或是8 bytes(64 bit電腦)。

(注意，以上的說明只限制在byte machine: 最小單位是1 byte的系統。雖說普通的電腦跟工作站幾乎都是byte machine，但是世界上也有別種機器的。還有，雖然本書以32 bit電腦跟64 bit電腦來總括了所有電腦，但是根據製作者，也會有其他種機器，總之，就是為了方便說明。)

目前為止範例程式出現的**struct tag_coord**變數，在記憶體上會是下面的圖:

![Imgur](http://i.imgur.com/pDfBg9I.png)

如果是一次讀取8 bytes的電腦，要讀取下一個變數，就是讀一次剛剛好的距離。那如果是**struct tag_question**的話，它的圖示像下面這樣:

![Imgur](http://i.imgur.com/57ACBE2.png)

如果真的像上面這個圖去配置的話，那麼一次就讀8 bytes的電腦想讀取成員變數會很困擾，因為0x7fff68372be9這個位址並不存在於一次就讀8 bytes的電腦裡。會說不存在是因為這個位址除以8會有餘數。

如果無論怎樣都想讀取0x7fff68372be9的話，就要從0x7fff68372be8跳8 bytes到0x7fff68372bf0後，再從0x7fff68372be9~0x7fff68372bf0之間去讀取0x7fff68372be9。這樣的話要存取兩次，效率不好。在製作CPU的時候，本來就不會讓CPU去做這麼沒效率的存取方式，硬是要CPU這麼去做的話會造成錯誤使程式異常中止。

摁了避免這種事，compiler進行了「alignment(對齊)」的動作，這是為了不讓CPU做沒效率的動作而在結構體的成員變數間塞了一些東西。這個**struct tag_question**，它在記憶體中其實長的像下圖這樣:

![Imgur](http://i.imgur.com/Mnw6RnY.png)

最左的一小格才是**character**，這樣的配置才可以讓存取的效率較好。

之前的陣列，一開始的開頭位址就已經alignment好了，所以連續的配置同一data type的資料本來就不會有問題，但是結構體可以含有許多不同大小的成員變數，所以需要這樣的處理。

還有，alignment是以幾個bytes為單位是看CPU的，上面的例子是以8 bytes為單位，但不是每台電腦都這樣，要注意。

雖然這次把**char**跟**double**包在一個結構體無論如何都需要16 bytes，如果有這個alignment的知識外加把成員變數排列的功夫，可以大幅的節約記憶體。

舉個例子，如果把剛剛的
```cpp
	struct tag_question{
		char character;
		double number;
	};
```
換成
```cpp
	struct tag_question{
		char c1;
		double d1;
        char c2;
		double d2;
        char c3;
		double d3;
        char c4;
		double d4;
        char c5;
		double d5;
        char c6;
		double d6;
        char c7;
		double d7;
        char c8;
		double d8;
	};
```
的話，可以發現size是128，因為16*8=128(32bit機器應該是96)。但是**char**跟**double**各八個，應該要是1*8+8*8=72才對，需要的記憶體是大幅增加了。

如果這種結構體只有一個那還不是很在意，但是有100000的話就會多佔去5MB的記憶體空間了。但是，宣告的位置排一下，就可以改善很多了。我們可以改成如下:
```cpp
	struct tag_question{
    	double d1;
        double d2;
        double d3;
        double d4;
        double d5;
        double d6;
        double d7;
        double d8;
		char c1;
		char c2;
        char c3;
        char c4;
        char c5;
        char c6;
        char c7;
        char c8;
	};
```
這樣應該就會是72了! 這樣宣告的話就不用每一次為了alignment而塞入填充，記憶體可以有效率的存取。

C語言有關聯到硬體跟OS，像這種CPU造成的結果是完全不掩飾的。所以會有很難的印象，很多不是因為C語言、而是CPU等等硬體的原因。也可以說C語言是對這很誠實的語言吧。