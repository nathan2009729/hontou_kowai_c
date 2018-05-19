目前為止說明的運算元，大部分是為了組成式子而且好懂的東西。但是，C語言也有少部份比較奇怪的運算元，介紹一下吧。

#### 求出大小的運算元 sizeof

雖然好像是一個單詞，但它是運算元。功用是返回變數或data type的大小。C語言的data type跟記憶體的大小有密切的關係，所以需要這種運算元。使用方法如下:

```cpp
sizeof 常數
sizeof 變數名
sizeof (data_type)
```
執行範例程式是理解的捷徑，來執行看看吧。

**source code**
*sizeof.c*
```cpp
#include <stdio.h>

int main(){
	char a;
	int b;
	double c;

	printf("size of literal '1' is %zu\n", sizeof 1);
	printf("size of literal 'hello, world\\n' is %zu\n", sizeof "hello, world\n");

	printf("size of variable a is %zu\n", sizeof a);
	printf("size of variable b is %zu\n", sizeof b);
	printf("size of variable c is %zu\n", sizeof c);

	printf("size of type long long is %zu\n", sizeof (long long));

	return 0;
}
```
**執行結果:**
```
size of literal '1' is 4
size of literal 'hello, world\n' is 14
size of variable a is 1
size of variable b is 4
size of variable c is 8
size of type long long is 8
```
要注意，如果compile產生error的話，把%zu改成%u看看。

照順序解說吧。**sizeof 1**是返回1這個常數到底佔了幾byte的記憶體。如果沒有特別指定的話，CPU會利用可以最高速動作的**int**，所以結果是4 bytes。如果打**sizeof 9223372036854780000**的話，這個數字需要64bit才裝的下，所以是8 bytes。

**sizeof "hello, world\n"**則是會返回"hello, world\n"這串文字常數的記憶體大小，結果是14 bytes。數一下文字數包括換行(\n)，總共是13個字，記得文字常數最後還有\0，所以是14。有疑問的話可以去看看第七章「如何處理字串」的說明。

**sizeof a**、**sizeof b**、**sizeof c**是返回變數**a,b,c**用了多少bytes的記憶體。嚴格來說，C語言並沒有規定data type的大小，一般的電腦都是**char** 1 byte，**int** 4 byte，**double** 8 byte。第七朝＝章有做過相關的說明。

最後的**sizeof (long long)**則是返回**long long**這個data type要多少bytes的記憶體。只有這種才需要用**()**括起來。data type像**long long**這種的，因為中間有空白，所以需要用**()**括起來。而**sizeof**這個運算元通常都用在查data type的大小，所以幾乎都用**()**括起來。因為如此，**sizeof**運算元很容易跟函數搞混，請記住不是這樣的。

### 把句子並排的運算元 「,」

,運算元(就是逗號)，是為了把複數的句子寫成一行，使用方法如下:

```cpp
句子1, 句子2, 句子3;
```
舉個例子:
```cpp
a = 1 + 1, b = 2 + 2, c = 3 + 3;
```

如果是用到這個「,運算元」的式子，那麼這個式子的值會是最後一個句子的值。也就是說，如果把上面的例子assign給另一個變數，它的值會是**c = 3 + 3**的結果，6。因為不好理解，所以試試範例程式吧。

**source code**
*comma.c*
```cpp
#include <stdio.h>

int main(){
	int a,b,c;
	int value;

	value = (a = 1 + 1, b = 2 + 2, c = 3 + 3);

	printf("a is %d\n", a);
	printf("b is %d\n", b);
	printf("c is %d\n", c);

	printf("value of expression is %d\n", value);

	return (0);
}
```

**執行結果:**
```
a is 2
b is 4
c is 6
value of expression is 6
```
會把使用「,運算元」的式子括起來是因為這個運算元的優先順位非常的低。考慮到式子的值，雖然有點囉唆，把複數句子寫成一個式子還是比較便利。但是，如果亂用的什麼都寫成1行的話，就會變成一個不知在幹嘛的程式，請注意。

### 怕麻煩的人用的複合運算元 (+=之類)

**複合運算元**是想要代入與運算同時進行所用的。複合運算元有下面這些種類:

* 加         +=
* 減			-=
* 乘 *=
* 除	/=
* mod %=
* 論理積 &=
* 論理和 |=
* 排他的論理和 ^=
* 左shift <<=
* 右shift >>=

使用方式如下所示。

```cpp
變數名 複合運算元 值或變數
```

比如說，**n += 1**的寫法，意思是把**n**加上1的結果再代入n (筆者註: **n += 1**其實就是**n = n + 1**，其他的複合運算元也是以此類堆)。**n += 1**也可以寫成**n++**，感覺這複合運算元也沒比較好用，但是，在重複處理時，如果希望**n**每次加2就有用了。

除了加法以外，上表有的其他複合運算元也是一樣的道理。雖然在習慣之前是不好讀的文法，但偶爾會用，就用範例程式的執行確認一下吧。

**source code**
*combo.c*
```cpp
#include <stdio.h>

int main(){
	int n;

	n = 1;

	n += 2;
	printf("n is %d\n", n);

	n -= 3;
	printf("n is %d\n", n);

	return (0);
}
```
**執行結果:**
```
n is 3
n is 0
```