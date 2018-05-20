理解了指標後，來介紹函數中對指標變數的處理。不過，給**printf()**函數字串這件事，本身就是把char陣列的開頭位址交給**printf()**處理。所以，記住函數接受指標的方法的話，也不是很難。

指標變數就像這樣宣告:

```cpp
data_type *變數名;
```
在宣告函數要用到指標變數時，也是像這樣宣告。趕快執行個範例程式看看吧。

**source code**
*func_4.c*
```cpp
#include <stdio.h>

int sum(int *, int *);

int sum(int *a, int *b){

	int return_value;
	return_value = *a + *b;
	
	return (return_value);
}

int main(){

	int num_1;
	int num_2;
	int answer;
	
	num_1 = 1;
	num_2 = 2;

	answer = sum(&num_1, &num_2);

	printf("answer = %d\n", answer);

	return 0;
}
```
**執行結果:**
```
answer = 3
```
執行結果和預想的一樣吧。再來試試把**main()**函數改成下面這樣，看看用陣列是不是也是一樣的結果。

```cpp
int main(){

	int number_array[2];
	int answer;
	
	number_array[0] = 1;
	*(number_array + 1) = 2;

	answer = sum(number_array, &number_array[1]);

	printf("answer = %d\n", answer);

	return 0;
}
```
很簡單吧。這次，寫一個返回指標的函數吧(main是剛剛改過)。
首先，把prototype宣告改成
```cpp
int *sum(int *,int *);
```
把函數的body也寫成
```cpp
int *sum(int *a,int *b);
```
在main()函數中，也做一些改變:
int answer換成
```cpp
int *answer;
```
answer = sum(number_array, &number_array[1]);換成
```cpp
answer = *sum(number_array, &number_array[1]);
```
printf("answer = %d\n", answer);換成
```cpp
printf("answer = %d\n", *answer);
```
總之，程式應該改成這樣:
```cpp
#include <stdio.h>

int *sum(int *, int *);

int *sum(int *a, int *b){

	int return_value;
	return_value = *a + *b;
	
	return (&return_value);
}

int main(){

	int number_array[2];
	int *answer;
	
	number_array[0] = 1;
	*(number_array + 1) = 2;

	answer = sum(number_array, &number_array[1]);

	printf("answer = %d\n", *answer);

	return 0;
}

```
筆者在compile時會出現警告，
```
func_4_modified_2.c: In function ‘sum’:
func_4_modified_2.c:10:9: warning: function returns address of local variable [-Wreturn-local-addr]
  return (&return_value);
         ^
```
執行結果是
```
程式記憶體區段錯誤 (core dumped)
```
但是原書作者雖然會出現警告，但是執行時依然會出現answer = 3。在原書作者的範例，如果在
```cpp
	answer = sum(number_array, &number_array[1]);

	printf("answer = %d\n", *answer);
```
之間插入一句
```cpp
printf("The answer is ...\n");
```
的話，answer會顯示32767。

現在來討論為何原書作者會出現這個結果吧(筆者依然是core dumped)。這個現象在第七章的auto-var.c也出現過。因為**sum()**函數中的變數**return_value**不是靜態變數，所以在**sum()**函數執行結束時這個變數的記憶體已經被其他呼叫的函數給覆寫了。

以這個例子來說，就是被**printf("The answer is ...\n");**給破壞，而被破壞而無效的位址被指標變數**answer**給間接參照，所以會出現奇怪的值。

對策的話，可以把**return_value**宣告成靜態變數，或是把想儲存結果的變數的位址給交出去也可以。因為之前已經說明過宣告成靜態變數的方法，現在就來介紹把想儲存結果的變數的位址給交出去的方法。

**source code**
*func_5.c*
```cpp
#include <stdio.h>

int sum(int *, int *, int *);

int sum(int *a, int *b, int *ans){

	*ans = *a + *b;

	return 0;
}

int main(){

	int num_1;
	int num_2;
	int *answer;
	int ans;

	num_1 = 1;
	num_2 = 2;
	answer = &ans;

	if(sum(&num_1, &num_2, &ans) != 0){
		//not reach, because sum() return 0
		printf("error\n");
	}

	printf("The answer is ...\n");

	printf("answer = %d\n", *answer);

	return 0;
}
```
**執行結果:**
```
The answer is ...
answer = 3
```

在這範例中，我們想把結果儲存在ans裡。**"想儲存結果的變數的位址"**就是**&ans**，我們把**&ans**交給了main中的指標變數，answer。

這個範例程式的**sum()**函數，把想儲存結果的變數**ans**的位址當作參數，把計算結果代入那個位址。所以不會出現先前那樣的現象。

函數雖然返回的值只能有一個，***但是使用上面這種寫法的話，不管一個函數想返回多少的值都沒問題***。因為是常用的方法，請記起來。

**恐怖實驗: 把NULL給指標**

使用"把想儲存結果的變數的位址給交出去"這個方法的話，在一些情況，比如說要同時求出座標的x, y, z的時候是非常方便的。雖然會因為如此而忍不住多用，但是要注意它有很可怕的陷阱--NULL。

NULL是為了讓一個指標變數表示「沒指向任何東西」的特別的值。可以判斷指標變數有沒有放值或是處理有沒有問題，就像下面用法:

```cpp
char *str = NULL;

//處理

if(str == NULL){
	//處理失敗
} else {
	printf("result is %s\n", str);
}
```

像這樣，用**NULL**把指標變數初始化可說是一定的。讓我們來執行一下範例程式吧; 把剛剛的程式(func_5.c)中的**answer = &ans;**換成**answer = NULL;**來實行看看。

**執行結果:**
```
The answer is ...
程式記憶體區段錯誤 (core dumped)
```
(筆者注: 以下內容原書寫的讓人看不懂，怎麼突然說是ans是NULL呢? 建議直接寫ans = NULL，answer = &ans不要改吧)

因為我們把**NULL**代入**ans**所以無法代入執行結果，所以會異常中止。像這樣參照**NULL**指標是常發生的錯誤，而且會造成異常中止。

如果想防範的話，就在**sum()**進行計算(** \*ans = \*a + *b;**)前，加入
```cpp
if(ans == NULL){
	return (-1);
}
```
caller(也就是main)會在**sum()**的返回值是-1的時候執行printf("error\n");，所以可以放心。

順代一提，C語言的標準library幾乎都沒有這種錯誤檢查。像是複製字串的函數**strcpy()**如果把**NULL**當參數一定會異常中止，在Solaris的**printf()**也是一樣。不過在linux跟Mac OS X的**printf()**，用%s遇到**NULL**時，只會顯示**(null)**不會異常中止。這是需要注意的。

大部分的系統，都把**NULL**當作0。所以把0代入也是跟上述一樣的結果。

**恐怖實驗: 不管什麼值都能用指標參照**

除了NULL指標參照以外，再介紹其他指標可怕的地方吧。就像我們拿指標去參照**NULL**一樣，系統完全不管指標變數放入了什麼值。也就是說，不管指標變數的內容是什麼，都是可以間接參照的。

如果放入的值是**NULL**的話可能就是異常中止，或是參照到奇怪的記憶體位址的話就會讀出奇怪的值，總之寫程式的人要付全責。而且是**NULL**的話因為會異常中止所以還可以知道有bug，但是參照到奇怪的值程式依然會繼續下去，在實際應用上可能會發生意想不到的事態。

來執行一下範例程式來實證一下吧。

**source code**
*ptr_bug.c*
```cpp
#include <stdio.h>

int main(){

	int num_x = 1;
	int num_y = 2;
	int num_z;
	int num_1 = 1000;
	int num_2 = 2000;

	num_z = num_x + num_y;

	int *pnum_1 = &num_x;
	int *pnum_2 = &num_y;

	pnum_1 += 2; //pointer bug
	pnum_2 += 2;
	
	printf("%d + %d = %d\n", *pnum_1, *pnum_2, *pnum_1 + *pnum_2);

	return 0;
}
```
**執行結果:**
```
1000 + 2000 = 3000
```
我們的程式是寫
```cpp
	int num_x = 1;
	int num_y = 2;

	int *pnum_1 = &num_x;
	int *pnum_2 = &num_y;
```
所以應該是1 + 2 = 3這個結果。但是我們寫了
```cpp
	pnum_1 += 2; //pointer bug
	pnum_2 += 2;
```
所以參照的地方就改變了，計算結果也跟著改變。因為變數**num_1,num_2**並不是陣列，所以把位址作加法就會超出變數的記憶體範圍外。但是對compiler來說不過是把指標變數**pnum_1**,**pnum_2**給加2而已，合法的句子所以不會有警告。結果就是，剛好指到了範圍外的**num_1**跟**num_2**，所以可以看到普通的結果。

如果是很單純的程式的話，這種bug可以很快發現，但是程式變得複雜的話就很難注意到了，直到成品出來後才被客人說:「好像有點怪怪的...」才第一次注意到。

指標的運算真的要很慎重。所指向的地方，真的是我們想要的位址嗎...?