數學的函數，是根據所給予的值決定某個數字的對應表示式。就像這樣的感覺: f(x) = ax + b。像C語言這樣的程式語言，會根據系統的狀態決定返回值，不過也是類似的東西。C語言可以把某些機能做成函數，把這些函數組合來寫程式。

### 函數是接受parameter並返回處理結果

**函數**，是接受參數(parameter)，經過內部的定義做了各種各樣的處理後，把返回值(函數的執行結束後，向呼叫自己的函式傳回的值)作為結果傳回去。為了處理這些事，compiler需要知道這個函數需要接受怎樣的parameter，需要傳回什麼data type的返回值，為此，需要「prototype宣告」這個宣告。可以想成變數要使用之前要先宣告是一樣的。

### prototype宣告跟headfile(標頭檔)

目前為止用了很多次的**printf()**，但是並沒有經過prototype宣告。那是因為我們**include**的stdio.h裡，已經做了宣告了。利用系統標準的函數，在像stdio.h的「headfile」中已經做過prototype宣告了。反過來說，想使用函數的話就要include headfile。順便提一下，**printf()**的prototype宣告長成這個樣子:

```cpp
int printf(const char* __restrict, ...)
```

自己寫函數的時候，先做prototype宣告再定義具體的函數動作。這些東西實際寫個程式是最容易理解的，來實驗看看範例程式吧。

**source code**
*func.c*
```cpp
#include <stdio.h>

//prototype define
//function sum: 2 int parameter, return int
int sum(int ,int);

//function body
int sum(int a, int b){
	int return_value;

	return_value = a + b;

	return (return_value);
}

int main(){

	int num_1;
	int num_2;
	int answer;

	num_1 = 1;
	num_2 = 2;

	answer = sum(num_1, num_2); //use function

	printf("answer = %d\n", answer);

	return (0);

} 
```
**執行結果:**
```
answer = 3
```

這個例子，是利用了一個接受兩個參數，把那兩個值加起來的結果傳回去的函數**sum()**。**main()**函數裡把變數**num_1**的1跟變數**num_2**的2代入了**sum()**的變數**a**跟**b**，使得**main()**可以利用**sum()**。

在**main()**函數裡，則是把**sum()**傳回的值代入**answer**，再用**printf()**把結果顯示在螢幕上。把變數**num_1**跟變數**num_2**的值變一下，確認一下程式的動作吧。再來，為了確認**main()**函數跟**sum()**分別是不一樣的結構在動作，把變數的名字變一下吧。

### 即使是同樣的變數名，實體也是不一樣的

再來一次，為了清楚函數是不一樣的結構，來執行看看下一個範例程式吧。基本上跟剛剛沒什麼不同，就是來確認一下執行前跟執行後的值有沒有改變。

**source code**
*func.c*
```cpp
#include <stdio.h>

//prototype define
//function sum: 2 int parameter, return int
int sum(int ,int);

//function body
int sum(int a, int b){
	int return_value;

	return_value = a + b;

	return (return_value);
}

int main(){

	int num_1;
	int num_2;
	int answer;

	num_1 = 1;
	num_2 = 2;

	answer = sum(num_1, num_2);

	printf("answer = %d\n", answer);

	return (0);

}
```
**執行結果:**
```
before sub() a = 2
after sub() a = 2
answer = 1
```
可以發現同樣一個變數名稱**a**在**main()**跟**sub()**都有用到，但是除了裡面放入的值以外並無關聯性，**sub()**的變數值換成的也對**main()**的**a**沒有影響。

原因的話，就如第七章「變數確保在普通stack中」的說明，變數是在普通stack被確保的。每叫出一次函數(以這程式來說是sub(), 被稱為callee)，就會在之前叫出自己的函數(以這程式來說是main(), 被稱為caller)的address上堆積stack。如果callee執行結束，就會從stack回到caller。

callee跟caller所使用的變數，會是在不同的記憶體位置被儲存的。所以，即使名稱相同，在不同函數上換的值對另一個函數是完全沒影響的。第七章的圖配合這個範例程式的話，圖如下:

看圖就可以明白，圖中5的領域寫成別的東西，也不會影響2的領域。

**恐怖實驗:呼叫和宣言不一樣形式的函數**

知道了如何製作函數之後，來介紹C語言可怕的點。其實，C語言呼叫和宣言不一樣形式的函數也不會出現error。範例程式中，有好好的宣告prototype，而且在caller(以範例來說caller是main())之前宣告function body，所以沒有大問題。但是，在符合以下條件的時候，不會有error或警告，而且執行時也不會出現error。

* 沒有宣告prototype
* 沒有在compiler option加上可以讓警告標準變嚴格的-Wall
* 函數的定義在caller之後

在這些情況下，程式都可以動且不會有error。雖然沒有error，但是實際上程式在執行的時候，在特定的條件下會有奇怪的結果，也很難發現問題點。來實驗看看好懂的範例程式吧。

**source code**
*func_3.c*
```cpp
#include <stdio.h>

int main(){
	
	int num_1, num_2, answer;

	num_1 = 1;
	num_2 = 2;

	answer = sum(num_1, num_2);

	printf("answer = %d\n", answer);

	return (0);
}

//function body

int sum(double a, double b){

	int return_value;

	return_value = a + b;

	return (return_value);
}
```
編譯的時候，makefile的CFLAGS只要寫-g就可以，不用寫-Wall。也可以直接打`gcc -g func_3.c -o func_3`。在筆者的電腦上，即使不寫-Wall，compile的時候也會出現警告信息
```
func_3.c: In function ‘main’:
func_3.c:10:11: warning: implicit declaration of function ‘sum’ [-Wimplicit-function-declaration]
  answer = sum(num_1, num_2);
           ^
```
但是在原書作者的電腦上卻不會出現警告信息。

**執行結果:**
```
answer = 0
```
可以知道1 + 2的執行結果會是0。理由的話看就知道了，**sum()**函數要接受的參數是**double**的data type，但是**main()**裡面傳進去**sum()**函數的參數是兩個**int**。在第八章說明過，**float**跟**double**的內部構造跟整數型態是不同的。所以才會變成奇怪的結果。

因為我們沒有在程式的開頭宣告prototype，所以**main()**處理到**sum()**的時候是不知道**sum()**該接受怎樣的參數的; 所以，這時是不會出現警告error(原作者電腦)。

在link的階段時，compiler並不會考慮程式的意義，caller只是決定了函數的名稱，這時還不會出現error。

為了防止這種事，在呼叫那個函數的程式的前面一定要宣告prototype。以這例子來說，我們只要在main()之前加一句`int sum(double, double);`就可以通過編譯跑出正確答案。因為compiler一開始就知道**sum()**函數是接受兩個double，所以會偷偷的進行**型態變換**。

在原書作者的電腦上，就算是沒有宣告prototype的程式，都需要加上-Wall才會有警告出現。而這種錯誤就算是對C語言詳細的人也可能會犯。所以平時好好的宣告prototype，在編譯的命令選擇比較多警告的option，才不會在萬一的時候困擾。