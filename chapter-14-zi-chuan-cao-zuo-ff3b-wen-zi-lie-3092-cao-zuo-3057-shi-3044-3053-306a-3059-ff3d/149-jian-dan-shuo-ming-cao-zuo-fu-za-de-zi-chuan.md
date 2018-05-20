本章最後，稍微說明一下複雜的字串操作。如果我們想組合兩個字串的話，我們會用到**strcat()**函數，但是，要把好幾個字串組起來要怎麼辦呢?

比如說，我們想把"hello, world"後面加上"Mr."或"Ms."，再加上姓名。因為**strcat()**函數是回傳字串的指標，所以上述的例子我們可以像下面這樣寫:

```cpp
char msg[256] = "";
char hello[] = "hello, world";
char title[] = "Mr.";
char space[] = " ";
char name[] = "Taro";

strcat(strcat(strcat(strcat(msg, hello), space), title), name);

printf("%s\n", msg);
```

但是，這樣寫完全看不出這個程式的目的是什麼。如果是聰明的大家，可能會用**printf()**像下面這樣寫吧:

```cpp
printf("%s %s %s\n", hello, title, name);
```
但是，**printf()**只會就這樣把三段字串放在一行，**msg[]**中並不會放入銀幕上印出來的結果。

為了這種情況，有一種可以把結果放入變數(輸出目標)的**printf()**變形函數。

```cpp
int sprintf(char *輸出目標, 顯示,...);
int snprintf(char *輸出目標, size_t 輸出目標大小,顯示,...);
```
返回值是輸出結果的大小。**snprintf()**因為有指定大小，所以在超過輸出目標大小時會停止輸出。但是要注意返回值是在大小足夠時才會返回。

像剛剛我們用**strcat()**函數寫的例子，用**snprintf()**會寫成下面這樣:

```cpp
snprintf(msg, sizeof(msg), "%s %s %s", hello, title, name);
```

其實用**sprintf()**也可以的，但是不指定輸出目標的大小比較危險，所以沒有特別理由的話就利用**snprintf()**吧。

用**snprintf()**也可以像**printf()**一樣使用變換指定，所以把整數換成16進位字串也很簡單。雖然簡單，但還是寫個範例程式實驗一下吧。

**source code**
*snprintf_test.c*
```cpp
#include <stdio.h>

int main(){

	char msg[256] = "";
	char hello[] = "hello, world";
	char title[] = "Mr.";
	char name[] = "Taro";

	snprintf(msg,
			 sizeof(msg),
			 "%s %s%s\n%#o\n%04d\n% 4d\n|%-4d|%-4d|\n|%4d|%4d|\n"
			 "%+d\n%+d\n%.4d\n%.5s\n%hhu\n%hhu\n%hhu\n%e\n%f\n%%\n",
			 hello, title, name, 8, 10, 10, 1, 2, 1, 2, 10, -10, 10,
			 "hello, world\n", 255, 256, 257, 0.1, 0.1 
			);

	printf("%s", msg);

	return 0;
}
```
**執行結果:**
```
hello, world Mr.Taro
010
0010
  10
|1   |2   |
|   1|   2|
+10
-10
0010
hello
255
0
1
1.000000e-01
0.100000
%
```
如何呢? 我們把放入陣列**msg[]**的東西印出來了。要注意的是，實際的程式不會像範例程式這樣什麼都寫在一起。就算說再怎麼便利，

```
			 "%s %s%s\n%#o\n%04d\n% 4d\n|%-4d|%-4d|\n|%4d|%4d|\n"
			 "%+d\n%+d\n%.4d\n%.5s\n%hhu\n%hhu\n%hhu\n%e\n%f\n%%\n",
```
寫這種指定變換，之後要修正會很麻煩。適度的把它們分開吧。

**恐怖實驗: 注意輸出大小的處理**

**snprintf()**函數的返回值，是輸出的大小(長度)。比如說如果我們寫:

```cpp
printf("%d\n", snprintf(msg, sizeof(msg), "a"));
```
的話，會印出1，因為我們把a一個字輸進了msg[]，所以會出現1。

利用這件事，我們可以設定，如果輸出目標的大小不夠的情況，就顯示error。趕快實驗一下吧。

**source code**
*snprintf_test_2.c*
```cpp
#include <stdio.h>

int main(){

	char msg[13] = "";
	char hello[14] = "hello, wprld\n";

	if(snprintf(msg, sizeof(msg), "%s", hello) > sizeof(msg)){
		printf("Error: buffer over flow\n");
	}

	printf("%s",msg);

	return 0;
}
```

執行後會發現是沒有換行的奇怪的結果，也沒輸出錯誤訊息。這是因為**snprintf()**函數並沒有把'\0'給算在輸出的大小裡，考慮到這點，應該要改成

```cpp
if(snprintf(msg, sizeof(msg), "%s", hello) > sizeof(msg) - 1){
	printf("Error: buffer over flow\n");
}
```