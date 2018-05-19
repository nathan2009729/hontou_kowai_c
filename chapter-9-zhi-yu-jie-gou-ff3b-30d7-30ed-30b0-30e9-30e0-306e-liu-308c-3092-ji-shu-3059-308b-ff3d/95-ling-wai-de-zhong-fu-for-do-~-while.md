學習了**if**跟**while**後，應該就可以實做各種各樣的演算法了。作為例子，我們介紹了求質數跟求圓周率的範例程式。看了這些範例，應該可以知道while寫法就是下面的模式:

```cpp
跟條件有關的變數的初始化
while(條件){
	增加變數的值
}
```
上面這個部份以求質數來說寫成下面這樣:
```cpp
i = 1;
while(i <= end){ //> 調查1~end之間的數字
	i = i + 1;
}
```
雖然不是說必要，大部分的重複處理都是這種型態。而C語言有準備了可以清楚的表達這種記述方式的「for」。

### for

**for**基本上跟while一樣，不過for可以把「重複前的處理(對應上面「跟條件有關的變數的初始化」)」「重複的條件」「在重複的過程中做的處理(對應上面「增加變數的值」)」，這三個東西寫成一行，範例如下:

```cpp
for(重複前的處理; 重複的條件; 在重複的過程中做的處理){
}
```
以下面這例子來說:
```cpp
i = 1;
while(i <= end){ //> 調查1~end之間的數字
	i = i + 1;
}
```
可以寫成
```cpp
for(i = 1; i <= end; i = i + 1)
```
//>
怎麼樣呢? 比起while來更加簡潔吧。for()中的**i = i + 1**，是在block裡面的事都處理完才執行的，之後才會判定中間的重複條件。

把prime_2.c用for重寫執行一遍，看看實際上該怎麼做吧。
**source code**
*prime_3.c*
```cpp
#include <stdio.h>

int main(){
	
	int i,j,end;
	int flag_not_prime; /*flag_not_prime = 0 is prime, 1 is not prime*/

	end = 100; //find prime until 100


	for(i = 1; i <= end; i = i + 1){ //>find prime from 1~end
		flag_not_prime = 0; 
		/*check whether i is prime, if i%j==0, i is not prime. 
		  Otherwise, i is prime. j is 2~i-1*/  
		for(j = 2;j < i; j = j + 1){//>
			if(i % j == 0){
				flag_not_prime = 1;
				break;
			}
		}

		if(i == 1){
			//i = i + 1;
			continue;
		}

		if(flag_not_prime == 0){
			printf("%d\n", i); //
		}
	}
	return 0;
}
```
執行結果跟prime_2.c是一樣的，不過程式變得簡潔許多了。尤其是當時的while版本在**continue**那裡還一定要寫**i = i + 1**，for在重複時會自動執行**i = i + 1**。

### counter (loop counter)

使用**for**的話，雖然可以不用在block中寫**i = i + 1**，使得程式清爽許多，但是利用第八章介紹過的increament運算元(**++**)可以更簡潔。因為**i = i + 1**是把**i**加1的處理，所以可以用**i++**代替，如下例

```cpp
for(i = 1; i <= end; i = i + 1){ //>
```
變成
```cpp
for(i = 1; i <= end; i++){ //>
```
實際的程式，幾乎沒有寫**i = i + 1**的，都是寫**i++**，原因是重複處理的情況都是按照順序一個一個處理。像這個數重複回數的變數總稱為counter或loop counter。因為是C語言常用的文法，請記起來。也可以把while.c的範例程式用for來改寫看看。

### 至少會重複一次的do~while

**while**在開始重複之前，如果沒有符合條件的話就不會開始重複。比如說下面的程式碼:
```cpp
while(cnt < 10){//>
}
```
如果在執行到while之前，cnt就已經大於等於10的話，那就根本不會執行{}裡的東西。但是在某些場合還是需要執行的，這就是do while的用處。**do while**就是一定會執行一次{}內的處理，第一次處理完後才會檢查while內的條件，寫法如下:

```cpp
do{
	//處理
}while(條件);
```
雖然同樣是重複，跟while還有for沒什麼區別，但是不管怎樣的條件都一定會執行一次block內的東西，在寫**對話方面**的處理是很方便的。來實驗看看單純的對話處理的範例程式吧，內容是擲骰子程式。

**source code**
*dice.c*
```cpp
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

int main(){
	int c;

	srand(time(NULL));

	do{
		printf("%d\n", rand() % 6 + 1);
		//get a random number between 1 to 6
		c = getchar();
		//get a character from keyboard
	}while(c != 'q');//if input is q, quit

	return 0;
}
```
**執行結果:**
```
6
 
2

1

4

1

6

2
q
```
執行這個程式的話，一執行就會先出現一個介於1~6之間的數字，之後如果輸入q以外的鍵(比如Enter)就會繼續出現下一個數字，直到按下q再按Enter程式才結束。雖然這個程式不需要用**do while**也可以寫，但是用**while**來寫的話，**getchar()**就需要寫兩次，source code不好看，改法如下:
```cpp
	do{
		printf("%d\n", rand() % 6 + 1);
		c = getchar();
	}while(c != 'q');
```
變成
```cpp
	c = getchar();
	while(c != 'q'){
		printf("%d\n", rand() % 6 + 1);
		c = getchar();
	}
```
雖然**do while**的使用頻率比**while**還有**for**低很多，但是為了在適當時候能夠做出最佳選擇，還是把**do while**記起來。