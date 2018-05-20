目前為止雖然在說動態記憶體的確保，但是只在呼叫**malloc()**跟**calloc()**才會指定需要的記憶體的大小; 所以在進行處理時，臨時想要更多的記憶體會發生困難。

這時候就會用到一個**malloc()**跟**calloc()**的夥伴，**realloc()**這個函數，來達成執行途中擴張記憶體的任務。宣告如下:

```cpp
void *realloc(void *想擴張的指標，size_t 擴張後的大小);
```
被擴張的領域會作為返回值被返回，擴張失敗則會返回**NULL**。但是並不是把原來指標所指的區域給變大，而是另外找一塊新的，符合擴張後大小的記憶體。

要說為什麼的話，這是因為記憶體領域要連續的才有意義。比如說之前的圖

![Imgur](http://i.imgur.com/iXTqFDu.png)

是不可能變成下面這樣的:

![Imgur](http://i.imgur.com/5L6yP9b.png)

所以，像下面這樣寫會是錯的:

```cpp
one_string = realloc(one_string, 32);
```
如果像上面這樣寫，可以不改變one_string位址並擴張的話是運氣好，但是有可能無法確保32 bytes而返回**NULL**。如果返回**NULL**的話，會變得不知道哪個資料存在哪個記憶體位址(因為one_string裡的位址變成**NULL**了)，所以存取或變更或**free()**都無法作到。

正確的使用方法如下面範例程式所示:

**source code**
*memory_2.c*
```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>


int main(){

	char *one_string, *new_string;

	one_string = malloc(14);
	if(one_string == NULL){
		printf("Memory allocation error!\n");
		return -1;
	}
	printf("one_string=%p\n", one_string);
	
	strncpy(one_string, "hello, world\n", 14 - 1);
	one_string[14 - 1]= '\0';

	printf("%s", one_string);

	new_string = realloc(one_string, 32);
	if(new_string == NULL){
		printf("Memory allocation error!\n");
		free(one_string);
		return -1;
	}
	//show the pointer of extended memory
	printf("new_string=%p\n", new_string);
	one_string = new_string;

	strncpy(one_string, "hello, world\nHELLO, WORLD\n",
			32 - 1);
	one_string[32 - 1]= '\0';
		
	printf("%s", one_string);

	free(one_string);

	return 0;
}

```
**執行結果:**
```
one_string=0x12c7010
hello, world
new_string=0x12c7440
hello, world
HELLO, WORLD
```
在執行結果上，我們可以看到記憶體的位址改變了。**realloc()**函數常常被誤用，請心裡好好記住位址會變這件事來使用這函數。

另外，雖然可能不常用到，但可以指定新的大小比原來還小。在這情況下，剩下沒被使用的領域會被無效化(就跟被**free()**了一樣)，會無法被存取，請注意。

**恐怖實驗: 使用因為擴張而被無效化的地方**

**realloc()**函數就像memory_2.c程式裡的用法一樣，先把擴張後的記憶體代入別的指標變數，如果那個指標變數不是**NULL**的話就再代入原來的指標變數。

但是，如果忘記在memory_2.c程式裡寫上**one_string = new_string;**的話，就會變成存取無效的領域，造成程式異常中止。我們來試試看在**one_string = new_string;**這句前面加上`//`把這行指令註解掉後重新編譯執行程式。

**執行結果:**
```
one_string=0xe9c010
hello, world
new_string=0xe9c440
hello, world
HELLO, WORLD
*** Error in `./memory_2': double free or corruption (fasttop): 0x0000000000e9c010 ***
======= Backtrace: =========
/lib/x86_64-linux-gnu/libc.so.6(+0x77725)[0x7fb649ea7725]
/lib/x86_64-linux-gnu/libc.so.6(+0x7ff4a)[0x7fb649eaff4a]
/lib/x86_64-linux-gnu/libc.so.6(cfree+0x4c)[0x7fb649eb3abc]
./memory_2[0x4007ac]
/lib/x86_64-linux-gnu/libc.so.6(__libc_start_main+0xf0)[0x7fb649e50830]
./memory_2[0x4005c9]
======= Memory map: ========
00400000-00401000 r-xp 00000000 08:09 3149971                            /home/asus/honto_kowai_c/ch15/memory_2
00600000-00601000 r--p 00000000 08:09 3149971                            /home/asus/honto_kowai_c/ch15/memory_2
00601000-00602000 rw-p 00001000 08:09 3149971                            /home/asus/honto_kowai_c/ch15/memory_2
00e9c000-00ebd000 rw-p 00000000 00:00 0                                  [heap]
7fb644000000-7fb644021000 rw-p 00000000 00:00 0 
7fb644021000-7fb648000000 ---p 00000000 00:00 0 
7fb649c1a000-7fb649c30000 r-xp 00000000 08:06 789802                     /lib/x86_64-linux-gnu/libgcc_s.so.1
7fb649c30000-7fb649e2f000 ---p 00016000 08:06 789802                     /lib/x86_64-linux-gnu/libgcc_s.so.1
7fb649e2f000-7fb649e30000 rw-p 00015000 08:06 789802                     /lib/x86_64-linux-gnu/libgcc_s.so.1
7fb649e30000-7fb649ff0000 r-xp 00000000 08:06 809170                     /lib/x86_64-linux-gnu/libc-2.23.so
7fb649ff0000-7fb64a1ef000 ---p 001c0000 08:06 809170                     /lib/x86_64-linux-gnu/libc-2.23.so
7fb64a1ef000-7fb64a1f3000 r--p 001bf000 08:06 809170                     /lib/x86_64-linux-gnu/libc-2.23.so
7fb64a1f3000-7fb64a1f5000 rw-p 001c3000 08:06 809170                     /lib/x86_64-linux-gnu/libc-2.23.so
7fb64a1f5000-7fb64a1f9000 rw-p 00000000 00:00 0 
7fb64a1f9000-7fb64a21f000 r-xp 00000000 08:06 809166                     /lib/x86_64-linux-gnu/ld-2.23.so
7fb64a3fc000-7fb64a3ff000 rw-p 00000000 00:00 0 
7fb64a41b000-7fb64a41e000 rw-p 00000000 00:00 0 
7fb64a41e000-7fb64a41f000 r--p 00025000 08:06 809166                     /lib/x86_64-linux-gnu/ld-2.23.so
7fb64a41f000-7fb64a420000 rw-p 00026000 08:06 809166                     /lib/x86_64-linux-gnu/ld-2.23.so
7fb64a420000-7fb64a421000 rw-p 00000000 00:00 0 
7fffab393000-7fffab3b4000 rw-p 00000000 00:00 0                          [stack]
7fffab3b4000-7fffab3b6000 r--p 00000000 00:00 0                          [vvar]
7fffab3b6000-7fffab3b8000 r-xp 00000000 00:00 0                          [vdso]
ffffffffff600000-ffffffffff601000 r-xp 00000000 00:00 0                  [vsyscall]
已經終止 (core dumped)
```
從位址0xe9c010開始從14個元素的領域擴張到32個的結果，是從位址0xe9c440開始確保了一塊32個元素的新領域。但是因為沒有了**one_string = new_string;**這行，所以**strncpy(one_string, "hello, world\nHELLO, WORLD\n",32 - 1);**這行對已經無效化的位址0xe9c010寫入了字串。

可怕的是，執行這裡的時候是不會出現錯誤的。從螢幕上好好的顯示了HELLO, WORLD就可以明白這一點。這是因為雖然被無效化，但並沒有被禁止使用，所以**strncpy()**依然可以執行。

這次出現的錯誤，是因為**free()**函數把已經被解放的位址0xe9c010(因為realloc把原來的記憶體解放去確保新的記憶體)又解放了一次。如果忘記了**free()**函數的話，就不會出現警告了。但是這樣會出現一個只在特定情況下會異常的bug。

總之要十分注意。