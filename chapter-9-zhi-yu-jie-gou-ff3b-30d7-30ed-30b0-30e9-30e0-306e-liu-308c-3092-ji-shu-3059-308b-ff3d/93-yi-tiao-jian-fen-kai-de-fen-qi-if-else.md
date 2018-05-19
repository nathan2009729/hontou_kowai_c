關於分歧，用的是**if**。if的意思是「如果」，用起來就像「如果是~那就」這樣的意思。

### 「如果是~那就」的 if

最基本的if文法就像下面這樣:

```cpp
if(條件){
	//處理
}
```

條件是以式子記述下來。一般來說，條件式就是是否相等、大於小於這樣。在第八章也有說明過，C語言的式子全部都有值，像**1 + 1**這式子就有2這個值。C語言的if，式子的值是0的話就是偽，反之為真。像下面的式子就可以沒問題的通過編譯。
```cpp
if (1 + 1){
	printf("true\n");
}
```
只是，這樣寫的話分歧就沒有意義了(因為必定為真，一定會執行)，要注意。

### 「如果不是，那就」的 else

除了if的「如果是~那就」之外，還有「如果是~那就...，如果不是就...」的記述。這種時候，就要用到**else**。
```cpp
if(條件){
	//處理
}else{
	//處理
}
```
此外，**else**還可以追加條件。這種情況，就用下面這樣記述:
```cpp
if(條件1){
	//處理
}else if (條件2){
	//處理
}else if (條件3){
	//處理
}else{
	//處理
}
```
目前為止的範例程式有if的登場，但else只有在這裡說明而已，來實驗看看範例程式吧。

**source code**
*if_else.c*
```cpp
#include <stdio.h>

int main(){
	int a = 1;

	if(a == 1){
		printf("a is 1\n");
	}
	else if(a == 2){
		printf("a is 2\n");
	}
	else{
		printf("a is neither 1 nor 2\n");
	}

	return 0;
}
```
**執行結果:**
```
a is 1
```
範例中把1代入**a**裡面，所以畫面顯示了「a is 1」。改一下**a**的初期值在實驗看看吧。

### block的意義

if在分歧的處理會用{}括起來，這叫做**block**，因為if條條件成立之後的處理可能有好幾句。但是只有一句的話，就不需要{}括起來，如下:
```cpp
if (a == 1)
	printf("a is 1\n");
```
雖然看起來有減少行數的好處，但其中有些陷阱。

**恐怖實驗: 沒有block的if**

不用block的if會有怎樣的陷阱呢?來實驗看看吧。首先把剛剛的if_else.c不用block看看吧。

**source code**
*if_else_2.c*
```cpp
#include <stdio.h>

int main(){
	int a = 1;

	if (a == 1)
		printf("a is 1\n");
	else if(a == 2)
		printf("a is 2\n");
	else
		printf("a is neither 1 nor 2\n");

	return 0;
}
```
執行過後可以發現它跟原本的程式行為是一樣的。那麼，我們把`printf("a is 1\n");`註解掉，變成`//printf("a is 1\n");`。所謂註解掉，是用在第六章介紹過的//或/\*\*/把程式中的句子無效化。請記住，//還有/\*\*/除了寫註釋以外，還可以在實驗時暫時把句子無效化。

在編譯的時候，應該會出現以下錯誤訊息:
```
if_else_2.c: In function ‘main’:
if_else_2.c:8:2: error: expected expression before ‘else’
  else if(a == 2)
  ^
```
會有這錯誤訊息是因為**if**接下來一定要有一個執行的句子或是一個block，但是我們把`printf("a is 1\n");`註解掉，突然接**else**了。對於compiler來說，這個程式長成底下這個樣子:
```cpp
	if (a == 1)
	else if(a == 2)
```
如果是if_else.c的話，把`printf("a is 1\n");`註解掉也沒事的，因為block({})還在。總之if不用block的話，就容易有這些想不到的錯誤。特別是新手，if(或其他的制御文)，推薦一定要用block。舉個同樣不危險的例子，來考慮下面:

**source code:**
*if_else_3.c*
```cpp
#include <stdio.h>

int main(){
	int a = 1;

	if(a != 0)
		printf("a is %d\n", a);
	
	return 0;
}
```
**執行結果:**
```
a is 1
```
如果在`printf("a is %d\n", a);`的後面加上一句`printf("a have a value\n");`的話，如下:
```cpp
	if(a != 0)
		printf("a is %d\n", a);
		printf("a have a value\n");
```
雖然不會有error，但是這程式的意義跟我們所想像的不同。

**if(a != 0)**所處理的分歧，在沒有block的前提下，就只會處理到`printf("a is %d\n", a);`這一句。用block寫的簡單易懂一點，就像下面這樣:
```cpp
	if(a != 0){
		printf("a is %d\n", a);
	}
    printf("a have a value\n");
```
犯下這種錯誤的話，因為compiler不會出現錯誤，所以執行時可能會覺得「好像哪裡奇怪阿」的煩惱。