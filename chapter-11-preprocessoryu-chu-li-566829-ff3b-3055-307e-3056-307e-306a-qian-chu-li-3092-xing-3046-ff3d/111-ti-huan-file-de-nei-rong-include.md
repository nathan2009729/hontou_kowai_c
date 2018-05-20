第五章，接觸了include這個preprocessor的命令。這個命令是說在**#include < file名稱 >**的情況下，從MinGW底下的include或/usr/include資料夾下找到同名檔案並置換掉這一行。比如說，我們寫了

```cpp
#include <stdio.h>
```
這一行，那麼就會被換成以下東西:

```cpp
# 28 "/usr/include/stdio.h" 2 3 4
# 1 "/usr/lib/gcc/x86_64-linux-gnu/5/include/stddef.h" 1 3 4
# 216 "/usr/lib/gcc/x86_64-linux-gnu/5/include/stddef.h" 3 4
# 216 "/usr/lib/gcc/x86_64-linux-gnu/5/include/stddef.h" 3 4
typedef long unsigned int size_t;
# 34 "/usr/include/stdio.h" 2 3 4
# 1 "/usr/include/x86_64-linux-gnu/bits/types.h" 1 3 4
# 27 "/usr/include/x86_64-linux-gnu/bits/types.h" 3 4
# 1 "/usr/include/x86_64-linux-gnu/bits/wordsize.h" 1 3 4
# 28 "/usr/include/x86_64-linux-gnu/bits/types.h" 2 3 4
typedef unsigned char __u_char;
typedef unsigned short int __u_short;
typedef unsigned int __u_int;
typedef unsigned long int __u_long;
typedef signed char __int8_t;
typedef unsigned char __uint8_t;
typedef signed short int __int16_t;
typedef unsigned short int __uint16_t;
typedef signed int __int32_t;
typedef unsigned int __uint32_t;

typedef signed long int __int64_t;
typedef unsigned long int __uint64_t;

typedef long int __quad_t;
typedef unsigned long int __u_quad_t;
# 121 "/usr/include/x86_64-linux-gnu/bits/types.h" 3 4
# 1 "/usr/include/x86_64-linux-gnu/bits/typesizes.h" 1 3 4
# 122 "/usr/include/x86_64-linux-gnu/bits/types.h" 2 3 4

typedef unsigned long int __dev_t;
typedef unsigned int __uid_t;
typedef unsigned int __gid_t;
typedef unsigned long int __ino_t;
typedef unsigned long int __ino64_t;
typedef unsigned int __mode_t;
typedef unsigned long int __nlink_t;
typedef long int __off_t;
typedef long int __off64_t;
typedef int __pid_t;
typedef struct { int __val[2]; } __fsid_t;
typedef long int __clock_t;
typedef unsigned long int __rlim_t;
typedef unsigned long int __rlim64_t;
typedef unsigned int __id_t;
typedef long int __time_t;
typedef unsigned int __useconds_t;
typedef long int __suseconds_t;

typedef int __daddr_t;
typedef int __key_t;

/*中間好多行*/

extern void funlockfile (FILE *__stream) __attribute__ ((__nothrow__ , __leaf__));
# 942 "/usr/include/stdio.h" 3 4
```

在稍微詳細說明一下**include**命令吧。file名稱通常是指定一種叫head file的file。head file一般來說會有data type、常數、還有函數的定義。用<>括起來的東西，會在MinGW底下的/include或/usr/include資料夾這兩個標準C語言library的head file放置處，尋找相同名稱的檔案。

如果想自己寫很多函數的話，那麼就會想要一個專為那群函數所用的head file。在這種時候，用`""`把自己寫的head file名稱括起來。這樣就會在跟source code一樣的目錄中尋找。首先來把範例程式build起來吧。

**source code**
*main.c*
```cpp
#include <stdio.h>
#include "functions.h" //this head file the functions made by myself

int main(){

	int num_1;
	int num_2;
	int answer;

	num_1 = 1;
	num_2 = 2;

	//execute function sum() which prototype is defined in functions.h
	answer = sum(num_1, num_2);
	printf("answer = %d\n", answer);

	answer = sub(num_1, num_2);
	printf("answer = %d\n", answer);

	answer = mul(num_1, num_2);
	printf("answer = %d\n", answer);

	answer = div(num_1, num_2);
	printf("answer = %d\n", answer);

	return 0;
}
```
**source code**
*function.h*
```cpp
int sum(int, int);
int sub(int, int);
int mul(int, int);
int div(int, int);
```

**source code**
*function.c*
```cpp

int sum(int a, int b){

	int return_value;

	return_value = a + b;

	return (return_value);
}

int sub(int a, int b){

	int return_value;

	return_value = a - b;

	return (return_value);
}

int mul(int a, int b){

	int return_value;

	return_value = a * b;

	return (return_value);
}

int div(int a, int b){

	int return_value;

	return_value = a / b;

	return (return_value);
}
```
compile時要下三條指令
```
gcc -g -Wall -c -o main.o main.c
gcc -g -Wall -c -o functions.o functions.c
gcc -g -Wall -o include main.o functions.o
```
這樣會生出一個名為include的執行檔。如果是makefile的話，就會寫成這樣:

```makefile
PROGRAM   = include
OBJS      = main.o functions.o
SRCS      = $(OBJS: %.o=%.c)
CC        = gcc
CFLAGS    = -g -Wall
LDFLAGS   =
$(PROGRAM):$(OBJS)
    $(CC) $(CFLAGS) $() -o $(PROGRAM) $(OBJS) $(LDLIBS)
```
因為這次是自己定義的head file實驗，所以試試了「分割compile」。來解釋一下compile那三行命令的意義吧。第一行是compile main.c，assemble後生成main.o; 第二行是compile functions.c，assemble後生成functions.o; 第三行是把main.o跟functions.o一起跟標準C library link起來，生成include這個執行檔。

雖然有點繞遠路，但讓我們來思考一下為何分割compile還有自己定義的head file會很重要吧。在第十章時自己寫函數的時候，說明了一定要宣告prototype。為了分割compile，我們把程式分成好幾個file去寫，在functions.c裡追加了新的函數; 這樣的話，就不需要在main.c中追加新的prototype宣告。

還有一點，就是main.c跟function.c的開發者是不同人的情況。假如我們一開始是把prototype宣告寫在main.c，而且寫function.c的人把函數的宣告稍微改了一下，但寫main.c的人不知道這件事所以沒改prototype。在這種情況下，就算compile跟link都沒異常的結束了，在執行時也可能會出現錯誤。

要避免這種事，寫function.c的人也要同時負責寫宣告prototype的functions.h，而寫main.c的人只要include functions.h就可以。這樣的話，如果function.c有變更的話compile時就會出現警告error，寫main.c的人注意到就可以修正用到函數的地方。

還有，一人在開發的時候，如果想讓別的程式也使用function.c的話，只要複製那個file(?)跟functions.h，讓別的程式去include，就可以使用function.c的函數。

因為這樣的理由，所以我們在把程式分成好幾個檔案去寫並分割compile時，我們也會分開去定義head file。標準C library的函數的定義都被整理在head file裡面，也是這個理由所造成的。比如說，如果沒有標準C library的話，想要利用到**printf()**或**fgets()**函數時，每一次都要自己在程式開頭宣告prototype，如下圖:

```cpp
int printf(const char *format, ...);
char *fgets (char *str, int size, FILE *stream);
typedef long time_t;
time_t time(time_t *tloc);
char *ctime(const time_t *clock);
```
每次都要寫這些，很麻煩吧，但是像下面這樣寫就輕鬆多了:

```cpp
#include <stdio.h>
#include <time.h>
```
下面是include這個執行檔的執行畫面:
**執行結果:**
```
answer = 3
answer = -1
answer = 2
answer = 0
```
就和預想的一樣吧。那麼，我們輸入以下指令，來看看main.c中**#include "function.h"**是怎麼置換的吧:

`gcc -E main.c > main.txt`

可以發現輸出的txt檔有以下訊息:
```cpp
//前面省略
# 2 "main.c" 2
# 1 "functions.h" 1

# 1 "functions.h"
int sum(int, int);
int sub(int, int);
int mul(int, int);
int div(int, int);
# 3 "main.c" 2
//以後省略
```
順代一提，**include**這個命令除了head file外，還可以用在讀取的置換上。就當做是玩玩把下面的範例build看看吧。

**source code**
*a.c*
```cpp
#include "a.5"

;
	return 0;
}
```
*a.1*
```cpp
#include <stdio.h>
```
*a.2*
```cpp
#include "a.1"

int 
```
*a.3*
```cpp
#include "a.2"

main(){
```
*a.4*
```cpp
printf
```
*a.5*
```cpp
#include "a.3"
#include "a.4"

("hello, world\n")
```
只要打
```
gcc -g -Wall a.c -o a
```
再打
```
./a
```
就會執行經典的hello, world程式了

**執行結果:**
```
hello, world
```
只要是文字文件什麼都可以include，雖然絕對不會像上面這樣用就是了，不過刻意這樣做可以讓大家有更強的「**include**就是文字置換」的感覺。