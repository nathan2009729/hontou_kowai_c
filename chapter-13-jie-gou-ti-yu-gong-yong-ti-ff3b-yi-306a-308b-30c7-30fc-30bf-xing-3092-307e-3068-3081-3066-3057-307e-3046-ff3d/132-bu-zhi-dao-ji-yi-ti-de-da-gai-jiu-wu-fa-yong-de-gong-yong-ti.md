還有一個跟結構體很像的，稱為**共用體**。

### 何謂共用體

在宣告的時候，除了把**struct**改成**union**以外，就跟結構體沒什麼差別了。這到底是什麼東西呢? 首先來執行一下範例程式吧。

**source code**
*unionworld.c*
```cpp
#include <stdio.h>

union tag_world{

	int num[4];
	char one_string[16];
};

int main(){

	union tag_world world;

	char one_string[] = "hello, world\n";
	int i;

	for(i = 0; one_string[i] != '\0'; i++){
		world.one_string[i] = one_string[i];
	}
	world.one_string[i] = one_string[i];

	printf("world.one_string is = %s", world.one_string);
	printf("world.num[] is = 0x%08x, 0x%08x, 0x%08x. 0x%08x\n",
			world.num[0], world.num[1], world.num[2], world.num[3]);

	return 0;
}
```
**執行結果:**
```
world.one_string is = hello, world
world.num[] is = 0x6c6c6568, 0x77202c6f, 0x646c726f. 0x0000000a
```
雖然不記得陣列**world.num[]**放入了什麼東西，但是是有數值表示的。而這個數值正是我們在第12章的intworld.c範例程式的指定數值。

到這裡應該已經明白了，共用體就如名所示，「讓成員變數共用同一個記憶體領域的結構體」。範例程式中，我們從**char one_string[] = "hello, world\n"**把字串複製到**world.one_string[]**裡。作為結果，跟**world.one_string[]**共用同一塊記憶體的**world.num[]**，得到了一樣的值，而且可以用int利用。

可以試試看在剛剛的範例程式的某處插入表示位址的**printf()**函數:

```cpp
printf("%p, %p\n", world.num, world.one_string);
```
**執行結果:**
```
world.one_string is = hello, world
world.num[] is = 0x6c6c6568, 0x77202c6f, 0x646c726f. 0x0000000a
0x7ffc4b1cba20, 0x7ffc4b1cba20
```
可以發現位址是一樣的。但是如果把**union**換成**structure**的話，會變成這樣的執行結果:

**執行結果:**
```
world.one_string is = hello, world
world.num[] is = 0x00000001, 0x00000000, 0x004006cd. 0x00000000
0x7ffe24b135c0, 0x7ffe24b135d0
```
這是因為one_string跟num的位址不同，所以**world.num[]**會是不知所以然的值。

### 共用體的大小

共用體的大小是由裡面大小最大的成員變數決定的，可以用**sizeof**的運算元確認一下。

現在已經理解了共用體的用法，但是可能想不到它的應用吧。應用的話，比如說通訊protocol好了，它的packet有好幾個data type如下。

* type1: int
* type2: double
* type3: 最多7個字的字串

我們想從這個packet取出資料時就會用到union如下:

```cpp
union tag_payload{
	int int_data;
    double double_data;
    char string_data[8];
};
struct tag_packet{
	int type;
    union tag_payload payload;
};
```
像這樣宣告後，想儲存接收到的資料的話，可以寫成像下面的條件分歧:

```cpp
struct tag_packet packet;

對接收的packet做處理的函數(&packet);

switch (packet.type){
	case 1:
    	printf("data is %d\n, packet.payload.int_data");
        break;
    case 2:
    	printf("data is %f\n, packet.payload.double_data");
        break;
    case 3:
    	printf("data is %f\n, packet.payload.string_data");
        break;
	default:
    	printf("error\n");
}
```

雖然可能很少在普通的程式使用，但記起來也沒損失吧。除了這個範例程式以外也寫寫其他的實驗程式來習慣吧。