**define**是可以把接收的參數置換的命令，也就是說，可以用它做出類似函數的東西，這被稱為「macro函數」。

### macro函數的寫法

比如說，想寫出跟function.c的**sum()**一樣的macro函數，就要寫成下面這樣:

```cpp
#define SUM(a, b) a + b
```
先來看看範例程式的執行吧。

**source code**
*define_4.c*
```cpp
#include <stdio.h>

#define SUM(a, b) a + b
#define SUB(a, b) a - b
#define MUL(a, b) a * b
#define DIV(a, b) a / b

int main(){

	int num_1;
	int num_2;
	int answer;

	num_1 = 1;
	num_2 = 2;

	answer = SUM(num_1, num_2);
	printf("answer = %d\n", answer);

	answer = SUB(num_1, num_2);
	printf("answer = %d\n", answer);

	answer = MUL(num_1, num_2);
	printf("answer = %d\n", answer);

	answer = DIV(num_1, num_2);
	printf("answer = %d\n", answer);

	return 0;
}
```
**執行結果:**
```
answer = 3
answer = -1
answer = 2
answer = 0
```
跟預想一樣的結果吧。

### macro函數不是函數!

這個macro函數，有著「函數名(參數)」這樣的型態，怎麼看都是函數。但是，實際上函數跟macro函數是完全不同的東西。要說的話，因為macro函數跟其它我們看過的**define**一樣，不過是單純的文字置換而已。

我們把define_4.c的範例程式編譯時gcc加上一些option觀察一下吧。

`gcc -E define_4.c > define_4.txt`

可以看到原本程式碼中的
```cpp
	answer = SUM(num_1, num_2);
	printf("answer = %d\n", answer);

	answer = SUB(num_1, num_2);
	printf("answer = %d\n", answer);

	answer = MUL(num_1, num_2);
	printf("answer = %d\n", answer);

	answer = DIV(num_1, num_2);
	printf("answer = %d\n", answer);

```
在define_4.txt中的最後，變成了下面這樣子:
```cpp
answer = num_1 + num_2;
 printf("answer = %d\n", answer);

 answer = num_1 - num_2;
 printf("answer = %d\n", answer);

 answer = num_1 * num_2;
 printf("answer = %d\n", answer);

 answer = num_1 / num_2;
 printf("answer = %d\n", answer);

 return 0;
```
可以發現**SUM(num_1, num_2);**的行蹤完全消失，變成了**num_1 + num_2;**，其他式子也是如此。

**恐怖實驗: macro函數的陷阱**

因為macro函數的便利，在C語言裡常常被利用。雖然之前的**SUM()**不怎麼實用所以不太可能會為它去寫macro函數; 不過因為是文字置換，不需要在意data type，所以我們會把兩個變數a, b的交換寫成像下面的macro函數:

```cpp
#define SWAP(a, b) a^=b; b^=a; a^=b;
```

但是，macro函數是有陷阱的。首先來執行看看可以正確動作的的範例程式吧。

**source code:**
*define_5.c*
```cpp
#include <stdio.h>

#define SWAP(a, b) a^=b; b^=a; a^=b;

int main(){

	int num_1;
	int num_2;

	num_1 = 1;
	num_2 = 2;

	printf("num_1 = %d, num_2 = %d\n", num_1, num_2);
	SWAP(num_1, num_2);
	printf("num_1 = %d, num_2 = %d\n", num_1, num_2);

	return 0;
}
```

**執行結果**
```
num_1 = 1, num_2 = 2
num_1 = 2, num_2 = 1
```
變數**num_1**跟變數**num_2**確實交換了。接下來，我們使用**if**，使得只有在**num_1**等於0的時候進行交換，先故意不用block吧，就像下面:
```cpp
	printf("num_1 = %d, num_2 = %d\n", num_1, num_2);
	if(num_1 == 0)
    	SWAP(num_1, num_2);
	printf("num_1 = %d, num_2 = %d\n", num_1, num_2);
```
**執行結果**
```
num_1 = 1, num_2 = 2
num_1 = 2, num_2 = 3
```
執行結果很奇怪吧。原本範例程式中的範例程式變數**num_1**是1才對，macro函數**SWAP()**應該沒有實行，而且還置換了奇怪的數字。我們用`gcc -E`來調查原因吧。輸入:

`gcc -E define_5.c > define_5.txt`

在define_5.txt中，可以發現**SWAP()**的函數被置換變成下面這樣子:

```cpp
printf("num_1 = %d, num_2 = %d\n", num_1, num_2);
if(num_1 == 0)
    num_1^=num_2; num_2^=num_1; num_1^=num_2;;
printf("num_1 = %d, num_2 = %d\n", num_1, num_2);
```
因為沒有{}，所以if的分歧處理就只有num_1^=num_2這一句; 要用{}解釋上面的code的話，就是下面這樣:

```cpp
printf("num_1 = %d, num_2 = %d\n", num_1, num_2);
if(num_1 == 0){
	num_1^=num_2;
}
    num_2^=num_1; 
    num_1^=num_2;;
printf("num_1 = %d, num_2 = %d\n", num_1, num_2);
```
所以才會出現奇怪的結果。原本在**if**的下面block的話就沒問題了，共同作業的人也不一定會在**if**加上block，而且就算弄錯也不會出現警告error，我們希望可以讓**SWAP()**函數本身可以應對不加block的問題。利用置換文字的特性，寫法如下:

```cpp
#define SWAP(a, b) {a^=b; b^=a; a^=b;}
```

這樣寫的話，就可以依照我們的想法執行了。
```cpp
/*if不加block*/
printf("num_1 = %d, num_2 = %d\n", num_1, num_2);
if(num_1 == 0)
	{a^=b; b^=a; a^=b;};
printf("num_1 = %d, num_2 = %d\n", num_1, num_2);

/*if加block*/
printf("num_1 = %d, num_2 = %d\n", num_1, num_2);
if(num_1 == 0){
	{a^=b; b^=a; a^=b;};
}
printf("num_1 = %d, num_2 = %d\n", num_1, num_2);
```
順代一提，macro函數也可以寫出分歧這種複雜的處理。在這情況下，可能會因為使用的制御文而造成compile error，可以使用像下列的不重複的**do while**寫法。

```cpp
#define SWAP(a, b) do{ \
					// 這裡可以寫if \
					(a)^=(b); \
                    (b)^=(a); \
                    (a)^=(b); \
                    }while(0)
```
這裡會寫成(a)還有(b)這樣把變數用括號括起來，是為了不要發生優先順位的問題。\是代表換行後**define**依然繼續的意思。還有，同常我們呼叫完函數後一定會加個;不過while(0)後面是不需要加的。

這些不是現在立刻要用到的知識，不過記起來也沒什麼損失。