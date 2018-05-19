雖然不像if用的這麼多，但還有另一個處理分歧很便利的**switch**。

### switch用法

if的話遇到複數的情況時，就像下面這樣使用:
```cpp
if(條件1){
	//處理
} else if (條件2){
	//處理
} else if (條件3){
	//處理
} else{
	//處理
}
```

如果每個條件所用到的變數都不同的話，那就沒辦法了，只能用上面這種寫法; 不過如果是因為同一個變數的不同值要做不同處理的話，有更簡潔的寫法，就是利用switch。寫法如下:

```cpp
switch (處理對象的變數) {
	//這裡的條件寫法不是if那種像句子的，而是寫「常數式」
    case 條件1:
		//處理
    case 條件2:
		//處理
    case 條件3:
		//處理
    default:
    	//處理
}
```
舉個例子，if_else_2.c中的分歧，是像下面這麼寫的:

```cpp
if(a == 1)
	printf("a is 1\n");
else if(a == 2)
	printf("a is 2\n");
else
	printf("a is neither 1 nor 2\n");
```
這些分歧，都是依照變數**a**的值去分的。用switch的話，可以寫成像下面這樣:

```cpp
switch (a){
	case 1:
    	printf("a is 1\n");
        break;
    case 2:
    	printf("a is 2\n");
        break;
    default:
    	printf("a is neither 1 nor 2\n");
        break;
}
```
不符合任何情況的時候，就是執行**default**。這裡不寫一個範例程式介紹switch，請自己把if_else_2.c改造做實驗。

### switch()的陷阱

雖然很輕鬆的就介紹掉了，不過switch意外的是個難的文法，原書作者在初學時也常搞錯。其實，switch有許多陷阱，比起用恐怖實驗來說明，還是用基本事項來說明這些陷阱吧。

首先，switch的()裡面就只能寫「常數式」。**if**的話，可以依據條件結果是0是1的「比較」(&lt; &gt; &lt;= &gt;=)還有「等價」(== !=)的式子，也就是「條件式」。那麼，什麼是「常數式」呢? 如名所示，是一個只有常數的式子，以if_else_2.c來說就是case後面的1還有2這種的。然後，就算說是常數，也不能是一個代入的式子。而這個常數式的部份，被稱為**label**。

如果是if的話，就算是代入式，那個式子也會持有值，所以依然可以做分歧的動作。比如說，第八章的時候的form_5.c
```cpp
if (a = 1 - 1){
	printf("True");
}
```
也是可以正常動作的。但是switch就無法像if一樣用這些條件式。接下來的寫法也會造成compile error。
```cpp
switch (a){
case b = 1 - 1;
		break;
}

switch (a){
case b > 1 - 1;
		break;
}
```
這樣寫會出現error: case label does not reduce to an integer constant。

再來，if可以選擇要不要用block，雖然基本上為了減少錯誤而應該用block，但是也可以不用，比如說:
```cpp
if (a == 1)
	printf("a is 1\n");
```
還有
```cpp
if (a == 1){
	printf("a is 1\n");
}
```
都是有效的。switch雖然也可以不用block，但是不用的話就只能寫一行命令而已。這樣的話，像上例就只能寫成
```
switch (a)
case 1:
	printf("a is 1\n");
```
但是把同樣的條件再寫一次以補足處理，這樣的switch就沒有意義了。所以switch一定要用block。

### 為了不忘記break，推薦使用//FALLTHRU

最後關於switch的**break**有需要注意的點。switch從上面開始找到符合條件的常數式並做完那個block的處理，如果沒有**break**的話就依然會繼續往下做。
```cpp
switch (處理對象的變數) {
	//這裡的條件寫法不是if那種像句子的，而是寫「常數式」
    case 條件1:
		//處理
    case 條件2:
    	//變數滿足條件2的情況下就做處理
		//處理
    case 條件3:
    	//在條件2的處理結束後依然繼續條件3的處理
		//處理
    default:
    	//處理
}
```
舉個例子，下面的程式變數**a**的值如果是2，那麼除了**printf("a is 2\n");**會執行以外，**printf("a is neither 1 nor 2\n");**也會被執行。
```cpp
switch (a){
	case 1:
    	printf("a is 1\n");
    case 2:
    	printf("a is 2\n");
    default:
    	printf("a is neither 1 nor 2\n");
}
```
為了防止這點，一定是需要**break**的。如下所示:
```cpp
switch (a){
	case 1:
    	printf("a is 1\n");
        break;
    case 2:
    	printf("a is 2\n");
        break;
    default:
    	printf("a is neither 1 nor 2\n");
        break;
}
```
也有故意在switch中不用**break**的例子。比如說下面一個if例子:

```cpp
if (a == 1 || a == 2){
	printf("a is 1 or 2\n");
} else{
	printf("other\n");
}
```
可以用switch改成
```cpp
switch (a){
	case 1:
    	//FALLTHRU
    case 2:
    	printf("a is 1 or 2\n");
        break;
    default:
    	printf("other\n");
        break;
}
```
這樣的一個故意不用break的switch寫法。只是，一般程式設計師看到的話，可能會有「switch不用break不會有潛在bug嗎?」的疑惑，這時的註釋就寫**FALLTHRU**。這樣以後看到的人就會知道這是故意不寫break的。

### 不會執行到break的時候，推薦用//NOT REACHED

還有一個同樣是註釋的**NOT REACHED**。因為switch幾乎都會用break，但寫了break卻執行不到break時，就會寫這個註釋。比如說，在switch的block裡，break之前就遇到了**return**，或是遇到了**goto**(之後會解釋)跳到了別的地方去。

在這種時候，為了表明「雖然因為是switch所以用了break，但實際上不會執行喔」的意思，我們會用註解寫上**NOT REACHED**。下面的程式就是遇到return後函數的執行就中止了，並沒有執行到break，所以就寫個NOT REACHED註釋。

```cpp
switch (a){
	case 1:
    	printf("a is 1\n");
        return (1);
        //NOT REACHED
        break;
    case 2:
    	printf("a is 2\n");
        return (2);
        //NOT REACHED
        break;
    default:
    	printf("a is neither 1 nor 2\n");
        return (0);
        //NOT REACHED
        break;
}
```
因為是註釋，所以加上**FALLTHRU**跟**NOT REACHED**是沒影響的。因為switch很難，為了防止錯誤還是寫一寫吧。

### 做出計算機

為了習慣之前所講的東西，這裡就來實行一個範例吧。

**source code**
*switch.c*
```cpp
#include <stdio.h>

int main(){

	int a,b;
	int op;
	int answer;

	a = 1;
	b = 2;
	op = '+';

	switch(op){
		case '+':
			answer = a + b;
			break;
		case '-':
			answer = a - b;
			break;
		case '*':
			answer = a * b;
			break;
		case '/':
			if(b == 0){
				printf("divide by zero\n");
			}
			else{
				answer = a / b;
			}
			break;
		default:
			printf("operator unknown\n");
			break;
	}

	printf("%d %c %d = %d\n", a, op, b, answer);

	return 0;
}
```
**執行結果:**
```
1 + 2 = 3
```

把想計算的值代入變數**a**及**b**，把運算元的文字代入變數**op**進行運算。如果b = 0, op = '/'的話，會產生除以0的error。說到**case '+'**為何是常數式，那是因為+這個文字本身就被當作數字處理，+是10進位的43，16進位的2B。C語言用`''`括起來的東西都會被當作ascii code的數值處理，當然，直接把'+'寫成43也是可以的，但要人家看得懂還是用+比較好吧。詳細的可以參照第七章的「不存在字串」的表。

像這程式有這麼多分歧的話switch是很有用的。雖然很複雜容易出錯，但請像if一樣好好記起來。