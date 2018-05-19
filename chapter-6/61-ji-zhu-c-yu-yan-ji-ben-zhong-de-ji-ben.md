在進入C語言的構造前，先記住一些基本的東西。

### "hello, world"程式的分解
_ _ _
為了理解C語言成立的基本，我們把一個叫"hello, world"程式做詳細的說明。程式碼如下:
```c
#include <stdio.h> /*1*/
int main(int argc, char *argc[])/*2*/
{
	printf("hello, world\n");/*3*/
    return (0);/*4*/
}
```
這個程式可以在畫面上顯示「hello, world」，接下來讓我們一行一行解釋。
#### /\*1*/preprocessor

`#include <stdio.h>`

這行與其說是C語言，==不如說是preprocessor的命令==。為了之後所要用到的**printf()**這個標準函數，include 含有printf()定義的stdio.h這個檔案。

由於是對preprocessor的命令，所以雖然不會直接對程式作用，但在使用C語言的函數之前，一定要定義那函數的性質。stdio.h除了printf()以外還有許多常用的標準函數的定義，以後說明會出現的"**pointer**"在"沒有指向任何東西"的狀態下所表示的**NULL**這種常數之類的，也會包含在裡面。

就像第五章介紹的一樣，在不使用run time的場合還有沒其他別理由的狀態下，通常會include這個stdio.h，可以把它當作寫C語言程式一定要寫的東西記起來。

再次重複，**include**==並不是==C語言的文法，而是preprocessor的命令，作用是在那一行展開stdio.h的內容而已。

#### /\*2*/main函數
`int main(int argc, char *argc[])`
這行表示"main函數"的開始。

C語言是由許多的函數集合起來組成一個程式。在程式啟動的時候，最先執行的函數就是main函數(從第五章可以知道，實際上最先執行的是startup routine，再來才是main函數)。

宣告函數之後，有被**{}**括起來的部份，叫做**block**，在block的內部寫著想要函數進行的處理。

接下來讓我們稍微說明一下函數的宣告。

最初的**int**是函數的返回值的型態。返回值是在函數的執行結束的時候，交給呼叫自己的函數的值; 如果是**int**型，代表返回值是一個整數。而呼叫main函數的人是startup routine，main函數的返回值最後則是交給OS。OS在看到main函數的返回值後，可以知道程式是正常或異常結束。一般來說，main函數返回0的話是正常結束，返回1的話是異常結束。

**int**的旁邊，**main**，是函數的名字。雖然可以為函數取任何名字，但main函數的名字一定是**main**。

接下來括弧中的(**int argc, char *argc[]**)是parameter的宣告。

函數可以從呼叫自己的函數中，接受任意數字的數值; 這個時候，要接受怎樣的數值就是在這裡宣告。main函數因為有著接受從OS來的parameter的前提，所以parameter一定是這樣宣告。所謂的從OS來的parameter，假設我們想向瀏覽器給一個URL時，我們會打`iexplore http://www.shuwasystem.co.jp`，這個URL的部份就是parameter。

#### /\*3*/函數的呼叫
接下來，我們說明函數的處理。

`printf("hello, world\n");`

這行可以很快的想到就是把「hello, world」顯示在畫面上的處理。再說的仔細點，就是對**printf()**這個函數，交給它"hello, world"這個值。如果只是顯示在畫面上的話，**print**就好了，f是什麼呢？f是formatted的意思，可以指定各式各樣的寫法。最後的**\n**則是換行的意思。

表示整數: `printf("%d\n",放著整數的變數);`
表示浮點數: `printf("%f\n",放著浮點數的變數);`
把數值表示成文字: `printf("%c\n",放著數值的變數);`
表示文字陣列: `printf("%s\n",文字陣列);`

詳細的會在下一章說明，由於會對下一節的實驗有用，所以先介紹幾個代表性的寫法的指定方法。

#### /\*4*/返回值與函數
`return(0);`
這裡是這個函數被怎樣的數值呼叫時，指定傳回給呼叫者的東西。
以這個的例子來說，main函數的返回值是**int**，所以需要傳回整數。而這裡指定了0這個值，傳給了OS。

從剛才開始，就出現了好幾次"函數"這個詞。

提到函數，大部分人都會想到數學函數f(x)，C語言的函數比較簡單一點，可以想成「把一些處理合在一起變成一個手續」這樣的東西。"函數"這名字聽起來很可怕，但其實並不難，不擅長數學的人先讀以下例子來抓住感覺。

假設有個程式，它可以做從價格求出含稅價格的處理。那麼普通的程式會寫成下面這樣: 

`tax_inclusive_price = price + price * tax;`

這種程度的話，每次要寫的式子只有一行所以還沒問題，但如果定義一個"從價格求出含稅價格"的函數的話，可以在有些情況下寫出比較好懂的程式。像是這樣:

```cpp
int calculate_tax_inclusive_price (int price, float tax){
	return (price + price * tax);
}
tax_inclusive_price = calculate_tax_inclusive_price(price ,tax) ;
```
**price + price * tax**的部份，變成了**calculate_tax_inclusive_price (int price, float tax)**。要說這是怎麼一回事的話，就是把"含稅價格計算"這個處理變成了**calculate_tax_inclusive_price**這個手續。

像這樣，不用每次再做這些處理的時候都要把**price + price * tax**重寫一次，也因為把這處理取了一個容易理解的名字，讓程式本身變得好懂。

實際上這種只有一行、本身就很好懂的處理不需要把它變成函數，不過可以了解大概的意思吧。

C語言的話，像是「在畫面上顯示文字」這種基本的功能都是作為函數實裝; 還有「測量字串的長度」這種常常在用的處理也是附屬在標準的函數裡面。我們把以上這些稱作「標準函數」。

記住文法與某種程度的標準函數，可說是記住C語言的一個標準吧; 而標準函數中最常用的，應該就是「printf」了。總之，記住**printf**的用法，就可以顯示計算的結果。

### 句子與indent與comment
_ _ _
hello, world程式的解說，應該理解了一點C語言的文法，這裡再講的詳細點。

#### 句子
C語言程式的最小單位，是「句子」。像`printf("hello, world\n");` 還有
`return (0);`都是句子。句子的最後，以`;`做結尾。除了變數、函數跟常數以外不管有多少的空白跟換行在文法上都沒錯。像是
```cpp
printf (  

"hello, world\n"
       )
       ;
```
這樣寫也沒錯。
#### indent
既然基本上空白多少都可以，那為何hello, world程式的block中會有那些空白？像那樣規則的空白我們稱為**縮排(indent)**。縮排並不是文法必須，而是為了讓人好讀而特意做的事。

縮排的輸入會依每人的習慣不同，基本上就是block往內縮4~8格空白，或是可以記成[TAB]按一次吧。另外，C語言是用`;`來識別句子的，就算不換行也沒問題。比如像這樣:
```c
#include <stdio.h>
int main(int argc, char *argc[]){printf("hello, world\n");return (0);}
```
也是可以的。要注意，include並不是C語言的文法，而是對preprocessor的命令，所以不能把很多#include縮成一行。

但是很難看吧。基本上要記住，`;`就換行，也要做縮排。

#### comment
最後介紹comment(註解)。所謂comment，就是在程式中寫下人類語言的註釋。programming的話，最開始寫的本人是很清處理解的，因為短所以其他人也很好理解。但是隨著時間的經過，規模會變得越來越大，也會有很難的構造使得光讀program是無法理解它在做什麼的，所以一般會加上註解作為對策。
```c
#include <stdio.h>
/*
 * main函數
 * program的開始
 */
int main(int argc, char *argc[]){
	printf("hello, world\n"); /*顯示文字列*/
    return (0); //給OS 0 這個數值
}
```
註解會像上面例子這樣，在`/**/`之間，或是在`//`之後。註解對程式的動作完全沒影響，所以也可以用來無效化某部份程式碼，比如

`//printf("hello, world\n"); /*顯示文字列*/`

就可以讓printf無效。