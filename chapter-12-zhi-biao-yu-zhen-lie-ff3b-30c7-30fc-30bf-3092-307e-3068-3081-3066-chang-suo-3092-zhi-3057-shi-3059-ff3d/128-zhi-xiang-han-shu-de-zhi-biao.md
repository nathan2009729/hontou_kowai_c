變數加上&，可以取得該變數的位址。那麼在記憶體上存在的函數又是怎樣的呢? 其實，是存在著放著函數位址的指標變數的，稱之函數指標，可以自由的代入這種指標變數並執行。

普通的函數的prototype宣告如下:
```cpp
返回值的data type 函數名(參數們);
```
範例:
```cpp
int sum(int, int);
```
而函數指標的宣告如下:
```cpp
返回值的data type (*函數名)(參數們);
```
範例:
```cpp
int (*sum)(int, int);
```
宣告了函數指標後，就可以往裡面代入函數。趕快來試試範例程式吧。

**source code**
*ptr_func.c*
```cpp
#include <stdio.h>

int sum(int, int);
int sub(int, int);
int mul(int, int);
int div(int, int);

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

int main(){

	int num_1;
	int num_2;
	int answer;
	int (*ptr_function)(int, int);

	num_1 = 1;
	num_2 = 2;

	ptr_function = sum;//assign sum() function into function pointer
	//ptr_function = sub;
	//ptr_function = mul;
	//ptr_function = div;
	
	answer = ptr_function(num_1, num_2);
	printf("answer = %d\n",answer);

	return 0;
}
```
**執行結果:**
```
answer = 3
```
結果好好的出來了，代表函數的指標變數**ptr_function()**作為函數執行了。如果使用同一型的函數但處理內容稍微有點不同的時候，使用函數的指標變數(函數指標)是很方便的。比如說，網路封包的處理、或是UNIX系的「synchronous handler」，在啟動多執行緒的函數時的參數也會利用到函數指標。

**恐怖實驗: 可以執行資料!?**

通常電腦對於在記憶體上的命令跟資料是沒有區分的。也就是說，記憶體上的「資料」，比如說"hello, world\n"，是可以執行的。反過來說，可以把應該要執行的「命令」作為資料表示。這是很危險的事嗎?

趕快來執行看看吧。

**source code**
*ptr_func_2.c*
```cpp
#include <stdio.h>

void func(void);

void func(void){
	printf("This is a func.\n");
}

int main(){

	char one_string[] = "hello, world\n";
	void (*ptr_function)(void);

	//ptr_function = func;
	ptr_function = (void(*)(void))one_string;

	ptr_function();

	return 0;
}
```
**執行結果:**
```
程式記憶體區段錯誤 (core dumped)
```
因為"hello, world\n"不是CPU可以解釋的「命令」，當然會異常中止。反過來說，只要是CPU可以解釋的命令就不會異常中止，可以作到「正確執行不正當的動作」，比如偷密碼之類的。

作為對策，總之要慎重對待指標，注意不要超過陣列確保的值。