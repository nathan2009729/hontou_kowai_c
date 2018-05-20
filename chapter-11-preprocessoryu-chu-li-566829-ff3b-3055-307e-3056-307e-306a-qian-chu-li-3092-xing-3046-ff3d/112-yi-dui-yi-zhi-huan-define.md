還有一個跟**include**類似的**define**命令。不過跟**include**置換檔案不同，**define**是可以置換source code中的內容。用法如下:

```cpp
#define 置換前 置換後
```
第九章求出質數的peime_3.c的範例程式，之中的**end = 100;**代表我們可以求到100為止的質數。可以用**define**來改寫成下面這樣:

```cpp
#define MAX 100
	end = MAX;
```
在preprocess的時候，**MAX**這字串就會因為**define**的命令通通換成100。趕快來實驗看看吧。

**source code**
*define.c*
```cpp
#include <stdio.h>

#define INTEGER_NUM_1 100
#define FLOAT_NUM_1 3.14
#define STRING_1 "%s"
#define STRING_2 "hello, world\n"

int main(){

	#define INTEGER_NUM_2 200
	#define FLOAT_NUM_2 2.71

	int a = INTEGER_NUM_1;
	printf("%d %d\n", a, INTEGER_NUM_2);

	float b = FLOAT_NUM_1;
	float c = FLOAT_NUM_2;

	printf("%f %f\n", b,c);

	printf(STRING_1, STRING_2);

	return 0;
}
```
**執行結果:**
```
100 200
3.140000 2.710000
hello, world
```
如何呢? 應該能了解就是單純的文字替換吧。