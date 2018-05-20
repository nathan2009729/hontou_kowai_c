我們介紹了幾個處理字串的函數，也有專門處理字母(文字)的函數，這裡也介紹一下。雖然是非常單一功能的函數，但是可能會在想不到的地方有用處也說不定。

### 文字種類判定(is系)

這裡要介紹的是可以判斷指定種類的函數群。雖然擅自稱為is系，不過真的就像**isalnum()**一樣以**is**開頭。首先來看看一覽表吧。

* isalnum(): 調查是否為英文字或數字
* isalpha(): 調查是否為字母
* isascii(): 調查是否為ASCII中的符號
* isblank(): 調查是否為空白
* iscntrl(): 調查是否為控制字元
* isdigit(): 調查是否為數字0~9
* isgraph(): 調查是否為可以表示的文字(不含空白)
* islower(): 調查是否為小寫
* isprint(): 調查是否為可以表示的文字(含空白)
* ispunct(): 調查是否為可以表示的文字(不含空白和英數字)
* isspace(): 調查是否為空白文字，也就是空白、'\f','\n','\r','\t','\v',
* isupper(): 調查是否為大寫
* isxdigit(): 調查是否為16進位數字

**is系**的函數全部，函數原型都是宣告在ctype.h裡面，所以使用時要include進ctype.h。全部的宣告都是像下面這樣:

```cpp
int is文字種類(int c);
```

雖然參數是**int**，但其實要給的是文字型的變數。返回值的話，符合條件的話不是0，不符合條件的話是0。由於C語言的條件式，0以外都當作真的，所以假設我們想在字母是大寫的時候做些處理，就寫**if(isupper(a))**就可以了。

那麼，這個要怎麼活用呢? 比如說，第七章我們利用了迴圈寫了ascii.c的範例程式，把所有可以8bit文字顯示了出來。但是，嚴格來說ASCII只能表現到7bit，後半都變成�，如下圖:

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
而且前半部都是空白，搞不清出它真的是空白還是因為沒辦法表示才會變成空白。這個時候，我們就來改造一下ascii.c這個範例程式，在**printf()**之前用**isprintf()**來調查看看。改造如下:
把
```cpp
	while(character < 256){//>
		printf("%c ", character);
		character = character + 1;
```
改成
```cpp
	while(character < 256){//>
    	if(isprint(character)){
        	printf("%c ",character);
        }
        else {
		printf(". ");
        }
		character = character + 1;
```
**執行結果:**
```
. . . . . . . . . . . . . . . . 
. . . . . . . . . . . . . . . . 
  ! " # $ % & ' ( ) * + , - . / 
0 1 2 3 4 5 6 7 8 9 : ; < = > ? 
@ A B C D E F G H I J K L M N O 
P Q R S T U V W X Y Z [ \ ] ^ _ 
` a b c d e f g h i j k l m n o 
p q r s t u v w x y z { | } ~ . 
. . . . . . . . . . . . . . . . 
. . . . . . . . . . . . . . . . 
. . . . . . . . . . . . . . . . 
. . . . . . . . . . . . . . . . 
. . . . . . . . . . . . . . . . 
. . . . . . . . . . . . . . . . 
. . . . . . . . . . . . . . . . 
. . . . . . . . . . . . . . . . 
```
可以發現輸出的行數變少了。其實，第七章的ascii.c，因為有換行文字等等所以會輸出多餘的行數。可以再把它改成下面這樣:

```cpp
	while(character < 256){//>
    	if(isprint(character)){
        	printf("%c ",character);
        }
        else if(isspace(character)){
			printf(" . ");
        }
        else{
        	printf("%02x ", character);
        }
		character = character + 1;
```
**執行結果:**
```
00 01 02 03 04 05 06 07 08  .  .  .  .  . 0e 0f 
10 11 12 13 14 15 16 17 18 19 1a 1b 1c 1d 1e 1f 
  ! " # $ % & ' ( ) * + , - . / 
0 1 2 3 4 5 6 7 8 9 : ; < = > ? 
@ A B C D E F G H I J K L M N O 
P Q R S T U V W X Y Z [ \ ] ^ _ 
` a b c d e f g h i j k l m n o 
p q r s t u v w x y z { | } ~ 7f 
80 81 82 83 84 85 86 87 88 89 8a 8b 8c 8d 8e 8f 
90 91 92 93 94 95 96 97 98 99 9a 9b 9c 9d 9e 9f 
a0 a1 a2 a3 a4 a5 a6 a7 a8 a9 aa ab ac ad ae af 
b0 b1 b2 b3 b4 b5 b6 b7 b8 b9 ba bb bc bd be bf 
c0 c1 c2 c3 c4 c5 c6 c7 c8 c9 ca cb cc cd ce cf 
d0 d1 d2 d3 d4 d5 d6 d7 d8 d9 da db dc dd de df 
e0 e1 e2 e3 e4 e5 e6 e7 e8 e9 ea eb ec ed ee ef 
f0 f1 f2 f3 f4 f5 f6 f7 f8 f9 fa fb fc fd fe ff 
```
雖然是餘談，**is文字種類()**這種函數，也有處理器是利用第11章介紹過的macro函數來實做。在這種情況下，如果使用像是**isupper(a++)**這種東西的話，**a**的值可能會在預想外。這是因為要判斷是不是大寫，就必須要參照兩次參數，因為用macro函數來實做**isupper()**的話就像下面這樣:

```cpp
#define isupper(a) ((a) >= 'A' && (a) <= 'Z')
```
//>
所以**isupper(a++)**會變成**(a++) >= 'A' && (a++) &lt;= 'Z'**這種東西，**a**會變成預想外的值。所以如果在使用**is文字種類()**函數出現奇怪的現象時，請考慮這種可能性。

### 變換文字種類(to系)

ctype.h除了**is**系以外也定義了其他的函數，是可以變換指定文字的**to系**，雖然可能用到機會不多就是了。而這種函數只有兩個:

* toupper(): 可以變成大寫的字就變成大寫
* tolower(): 可以變成小寫的字就變成小寫

宣告如下:

```cpp
int to文字種類(int c);
```
我們來寫個簡單的範例程式確認一下它的動作吧。

**source ocde**
*to_test.c*
```cpp
#include <stdio.h>
#include <ctype.h>

int main(){

	char hello[16] = "hello, world";
	char HELLO[16];

	char *src, *dst;

	src = hello;
	dst = HELLO;

	while(*src != '\0'){
		*dst = toupper(*src);
		src++;
		dst++;
	}
	*dst = '\0';

	printf("%s\n", HELLO);

	return 0;
}
```
(以下是筆者自己寫的陣列版本)
```cpp
#include <stdio.h>
#include <ctype.h>

int main(){

	char hello[16] = "hello, world";
	char HELLO[16];

    /*char *src, *dst;

	src = hello;
	dst = HELLO;

	while(*src != '\0'){
		*dst = toupper(*src);
		src++;
		dst++;
	}
	*dst = '\0';*/
	int i;
	for(i = 0; hello[i]!='\0';i++){
		HELLO[i] = toupper(hello[i]);
	}
	HELLO[i] = '\0';

	printf("%s\n", HELLO);

	return 0;
}

```
**執行結果:**
```
HELLO, WORLD
```
可以看到只有字母被轉成了大寫。雖然可能**is**系跟**to**系使用頻率都不高，但是知道的會比較方便，請自己再執行一遍吧。