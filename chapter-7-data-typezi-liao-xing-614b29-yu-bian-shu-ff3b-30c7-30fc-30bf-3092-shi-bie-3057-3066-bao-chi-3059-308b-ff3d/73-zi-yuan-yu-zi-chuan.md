說明到這裡，應該能理解數值放入變數裡的方法了。但是關於文字被放入**char**來表示這點，來不是很清楚吧。

#### C語言如何表示文字

一般的alphabet，就像之前說明的「data type就是處理記憶範圍要多大」，用ascii code來表現。

*ascii code表:*

![ascii code表](http://irp.nain-t.net/lib/exe/fetch.php/030codage:iso_latin_1.png)

這個圖是把ascii code表示出來的表。縱軸(00,10,20,...,F0)是16進位上第二位，橫軸(00,01,02,...0F)是第一位，表示了00 ~ FF共256種值，在十進位，00 ~ FF表示了0~255。

從這個表找出字母A的話，它是在縱軸的40, 橫軸的01，所以是16進位的41。以十位數來講，就是16*4+1=65。也就是說，字母A以數字65表示。接下來，就算說明也說不清楚，就實際寫寫範例程式吧。

**source code:**
*character.c*
```cpp
#include <stdio.h>

int main(){
	int one_alphabet;
	int one_number;

	one_alphabet = 65;
	one_number = 48;

	printf("one_alphabet is %c\n",one_alphabet);
	printf("one_number is %c\n",one_number);

	return 0;
}
```
**執行結果:**
```
one_alphabet is A
one_number is 0
```
在**printf()**指定%c的話可以把數字作為文字表示。因為如此，65與48這兩個數值被表示成文字A和0。我們應用這個，來執行一個畫出ASCII表的程式。而這程式會用到第九章才會說明的「while」跟「if」，會比至今為止的程式還難，不果應該不知不覺間可以理解。

**source code:**
*ascii.c*
```cpp
#include <stdio.h>

int main(){
	int character = 0;

	while(character < 256){ //>
		printf("%c ", character);
		character = character + 1;
		if((character % 16) == 0){
			printf("\n");
		}
	}
	return 0;
}
```

**執行結果:**
```
        	 
 
  
   
                
  ! " # $ % & ' ( ) * + , - . / 
0 1 2 3 4 5 6 7 8 9 : ; < = > ? 
@ A B C D E F G H I J K L M N O 
P Q R S T U V W X Y Z [ \ ] ^ _ 
` a b c d e f g h i j k l m n o 
p q r s t u v w x y z { | } ~  
� � � � � � � � � � � � � � � � 
� � � � � � � � � � � � � � � � 
� � � � � � � � � � � � � � � � 
� � � � � � � � � � � � � � � � 
� � � � � � � � � � � � � � � � 
� � � � � � � � � � � � � � � � 
� � � � � � � � � � � � � � � � 
� � � � � � � � � � � � � � � � 

```

也可以試試把`character = character + 1;`變成`character = character + 2;`或是`printf("%c ", character);`改成`printf("%d ", character);`，又或是把`int character = 0;`改成`int character = 32;`把`while(character < 256){//>`改成`while(character < 128){//>`看看會發生什麼事，總之多做是程式進步的秘訣。

經過了以上這兩個程式，對C語言如何處理文字應該可以理解。但是，實際的程式常常處理的是像「hello, world」這種字串，而不是像剛剛的一個字一個字來。那字串要如何處理呢？

### 如何處理字串

嚴格來說，C語言不存在處理「字串」的data type。直覺好的人，會想在第五章的printf("hello, world")中用""括起來的是不是就是在處理字串? 可以實驗一下，把character.c的`one_alphabet = 65;`把它改成`one_alphabet = "hello, world";`會發現出現警告; 即使執行也跑不出東西。

想一下就知道，**char**型的變數只有1byte大小，然後想表示一個字就需要1byte，但是「hello, world」有12個字。也就是說需要12byte以上的記憶體，**char**型的變數本來就放不下。

在這裡，C語言為了表現字串，利用了文字的「陣列」。關於陣列，會在第十二章「pointer與陣列」詳細解釋，想像一下同樣data type的變數排成一直線。

![Imgur](http://i.imgur.com/iXTqFDu.png)

陣列就像以下這樣宣告:

`data_type 變數名[元素個數]`

比如說，為了放入14個bytes，就需要宣告有14個元素的char陣列如下:

`char one_string[14];`

C語言對於被這樣宣告的變數會當成字串來處理。但是，要處理字串的話，陣列的最後就需要放一個「\0」的特別的值。「\0」雖然以值來說就是0，它是為了明確表示文字的尾端的。因為不直觀，所以來實行一下sample program吧。

**source code**
*string.c*

```cpp
#include <stdio.h>

int main(){
	char one_string[14];

	one_string[0] = 104;
	one_string[1] = 101;
	one_string[2] = 108;
	one_string[3] = 108;
	one_string[4] = 111;
	one_string[5] = 44;
	one_string[6] = 32;
	one_string[7] = 119;
	one_string[8] = 111;
	one_string[9] = 114;
	one_string[10] = 108;
	one_string[11] = 100;
	one_string[12] = 10;
	one_string[13] = 0;

	printf("%s", one_string);
	return 0;
}
```

**執行結果:**
```
hello, world
```
可以好好的表示「hello, world」，但是處理字串的話要給陣列代入數字太麻煩了。所以C言語對於用`''`括起來的文字會把它當數字來看。所以利用這個特性，可以寫的更好懂一點。

```cpp
	one_string[0] = 'h';
	one_string[1] = 'e';
	one_string[2] = 'l';
	one_string[3] = 'l';
	one_string[4] = 'o';
	one_string[5] = ',';
	one_string[6] = ' ';
	one_string[7] = 'w';
	one_string[8] = 'o';
	one_string[9] = 'r';
	one_string[10] = 'l';
	one_string[11] = 'd';
	one_string[12] = '\n';
	one_string[13] = '\0';
```

這樣執行也可以得到一樣的結果。比起用數字指定文字，這樣會比較好懂; 但是這也給人一種冗長的感覺。其實，就像之前的"hello, world"這種被""括起來的東西可以表示字串。如果在初始化時把這指定給**char**型的陣列，就不用一個一個代入文字跟數字。在初始化時給陣列指定字串就像下面這樣宣告:

`char 變數名[元素個數] = "字串";`

如果是「hello, world」的話，就像下面這樣宣告:

`char one_string[14] = "hello, world\n";`

確認一下，把剛剛的範例程式換成這個寫法，是不是會出現一樣的結果。

### 不可思議的字串常數

現在已經理解用**char one_string[14] = "hello, world\n";**這個寫法，可以讓陣列容納字串。那麼，把陣列的宣告跟字串的assign分開來會怎樣呢? 也就是說，把剛剛的

`char one_string[14] = "hello, world\n";`

換成

`char one_string[14];
 one_string = "hello, world\n";`

如果這樣改的話，在編譯時會出現錯誤。這是為什麼呢?

首先，C語言把`""`括起來的東西當作「字串常數」來處理，在程式執行中一直把它存在有記憶體的位置。變數可以常常被讀取或放入新的值，但常數基本上無法被替換，只能被讀取。只在程式裡寫"hello, world\n"的話，對記憶體來說就像下面這圖一樣:

![Imgur](http://i.imgur.com/PU9RhqU.png)

想像有點困難，首先來介紹範例程式吧。
**source code:**
*string_const_test.c*
```cpp
#include <stdio.h>

int main(){
	"hello, world\n";
	return 0;
}
```
compile的話，應該會出現以下error吧:
`string_const_test.c: In function ‘main’:
string_const_test.c:4:2: warning: statement with no effect [-Wunused-value]
  "hello, world\n";
  ^
`
statement with no effect意思是沒有意義的句子。但是如果換成

`char one_string[14] = "hello, world\n";`

記憶體就會變成下圖的狀態:

![Imgur](http://i.imgur.com/fOIzyPa.png)

也就是說，記憶體上「hello, world」會出現在兩個地方。C語言的話，會在陣列初始化時把常數領域的字串照順序帶入。寫`char one_string[14] = "hello, world\n";`的時候，compiler自動把文字帶入**one_string**的各元素。

但是，陣列一旦被宣告，寫程式的人就需要負責任把值代入這個陣列。所以剛剛

`char one_string[14];
 one_string = "hello, world\n";`

的寫法會出錯。雖然很難理解，但這是C語言的構造所以沒辦法。

當需要把字串放入已宣告的陣列的話，要嘛像string.c一樣一開始就代入，要嘛就要用到**strcpy()**或**memcpy()**這種複製記憶體內容的函數。**strcpy()**在第十四章「使用字元與字串」會說明。

在第五章範例程式，並沒有給**printf()**函數一個陣列，而是突然給它一個字串常數: `printf("hello, world\n");`，那為何**printf()**函數可以接受字串常數也可以接受陣列呢?

C語言的話，普通的變數(**char**, **int**之類)，就直接處理變數裡的值了。但是像陣列或字串這樣大的情報，只能處理放著情報的記憶體範圍的第一個address。為了說明例子，我們把剛剛的圖加上address，從10開始。

*即使是同樣的字串也是放在不同場所使用*
![Imgur](http://i.imgur.com/rpNT1Ih.png)

直接對**printf()**指定字串常數"hello, world\n"的話，**printf()**會從把address從10號到\0所在的23號之間的字串顯示出來。如果指定陣列**one_string**的話，**printf()**會從把address從80號到\0所在的93號之間的字串顯示出來。這裡是沒有字串type這個data type的C語言非常難的部份。如果不是很理解的話，總之就一直讀到第12章的pointer與陣列後，再回來複習這裡。

**恐怖實驗: 沒有'\0'的話會怎樣?**

目前為止的說明，應該理解了C語言為了處理字串的陣列，最後一格要是'\0'。那沒有'\0'的話會怎樣? 實驗看看吧。把string.c中的`one_string[13] = 0;`給註解掉(就是變成`//one_string[13] = 0;`)來compile看看。

筆者手邊的電腦顯示出來是正常的，但有些人可能會換行出現?之類的。這是因為處理字串的函數(以此例來說是**printf()**)，會把到'\0'為止的東西視為字串，如果沒有'\0'的話直到找到為止都繼續處理。?的例子，就是因為在?這個文字後發現'\0'才停止印出。但是運氣不好一直找不到'\0'的話，可能就會一直讀到不可以讀的記憶位置。這樣的話，程式就會異常中止。

請意識到C語言在處理字串的時候終端一定要有個0。

**恐怖實驗: 超過元素數目的話會怎樣?**

我們至今學習到C語言用char的陣列來利用字串型態。而剛剛宣告陣列時，我們宣告元素個數是14，如果弄錯了會怎樣呢? 我們可以把剛剛string.c的14換成4來看看。筆者手邊的環境，執行的時候會出現
```
*** stack smashing detected ***: ./string terminated
已經終止 (core dumped)
```
有些人的電腦則是會出現Segmentation fault這種錯誤。這是因為程式把值放進沒有許可的記憶體範圍，OS強制終止會出現的錯誤。把值放進沒有許可的記憶體範圍的話，stack會被破壞，程式會異常中止，所以OS才會強制終止。

可怕的是，即使寫這種錯誤的程式，C語言的compiler完全不會給警告，所以一直到執行前都很難發現這種錯誤。而且，有時就算是這種超出範圍的錯誤程式，運氣好的話依然可以正常執行，但如果問題發現的晚的話之會會發生大問題。

### 字串總結

因為C語言的字串處理很難懂，所以重新整理一次吧。

* 沒有處理字串的data type
* 以char的陣列來代替處理字串
* 當作字串處理的char陣列，最後一定要擺一個'\0'
* 函數來使用時，是從字串的第一個元素的address開始用
* 被""括起來的字串，和陣列的變數是存在記憶體的不同位置