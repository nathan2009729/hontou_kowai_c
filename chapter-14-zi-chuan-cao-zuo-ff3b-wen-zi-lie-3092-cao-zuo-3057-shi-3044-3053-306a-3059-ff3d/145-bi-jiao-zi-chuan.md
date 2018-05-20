我們也常常希望知道輸入的字串是否與預想一致。比如說，利用**fgets()**，使程式只有在輸入"yes"的時候才會進行處理。

### 何謂字串的比較

C語言的話，因為字串是個**char**的陣列，所以不能像整數一樣用**if(number == 0)**之類的進行比較。比如說，來考慮以下程式碼:

```cpp
char a[] = "yes";
char b[] = "yes";

if(a == b){
	//處理
}
```
在第十二章說明過，如果是陣列，那麼只寫名稱的話是代表陣列的開頭位址。陣列**a[]**跟**b[]**是在不同的記憶體位址，當然**a == b**絕對不會為真。

如果要調查**a[]**跟**b[]**的內容是否一致的話，可能寫成**if(a[0]==b[0] && a[1]==b[1] && a[2]==b[2]...)**之類的，這種寫法如果搞錯字串長度的話就會接觸到奇怪的記憶體位址導致程式異常中止。

所以在這種情況下，一般會用專用的函數。

### 字串的比較(strcmp)

**strcmp()**是STRingCoMPare的簡稱，可以進行字串比較的函數。它的宣告如下:

```cpp
int strcmp(const char *字串1，const char *字串2);
```
返回值的話，字串1比字串2大的話正數，字串1比字串2小的話負數，相等的話0。

在這裡，出現了字串1跟字串2的大小概念，這是什麼意思呢?

舉個例子，字串1是"foo"，字串2是"bar"，那麼字串1較大。直覺好的人可能發現了，這裡的大小比較指的是字典順序。在字典裡，foo會出現的比bar後面，我們說foo比bar大。這情況下，**strcmp()**會返回正數，反之負數。

跟其他的字串函數一樣，**strcmp()**的prototype在string.h中，所以要include它。來用範例程式確認看看它的動作吧。

**source code**
*strcom_test.c*
```cpp
#include <stdio.h>
#include <string.h>

int main(){

	printf("foo bar = %d\n", strcmp("foo", "bar"));
	printf("bar foo = %d\n", strcmp("bar", "foo"));
	printf("foo foo = %d\n", strcmp("foo", "foo"));

	return 0;
}
```
**執行結果:**
```
foo bar = 1
bar foo = -1
foo foo = 0
```
這次的範例程式返回正數是1，負數是-1，但是也有可能是4或-4之類的。所以不要寫\==1或==-1，要寫> 0或&lt; 0去比較。

**恐怖實驗: strcmp()是在compile時才進行比較**

Linux或Mac OS X等等最近的電腦，如果像下面這樣改造的話會出現不一樣的結果。

```cpp
	printf("foo bar = %d\n", strcmp("foo", "bar"));
	printf("bar foo = %d\n", strcmp("bar", "foo"));
	printf("foo foo = %d\n", strcmp("foo", "foo"));

```
改成
```cpp
	char a[] = "foo";
    char b[] = "bar";
	printf("foo bar = %d\n", strcmp(a, b));
	printf("bar foo = %d\n", strcmp(b, a));
	printf("foo foo = %d\n", strcmp(a, a));
```
筆者跟原書作者的執行結果都如下:

```
foo bar = 4
bar foo = -4
foo foo = 0
```
跟剛剛的結果有些不同。這是因為最近OS的compiler在見到**strcmp()**函數時，如果在compile時期就知道結果的話，就不會特地把函數呼叫出來，而是直接把值丟進去。還沒改造的程式，對**strcmp()**直接丟入"foo"跟"bar"的字串常數。在第七章說過，字串常數是放在一個讀出專用的區域，程式執行途中絕對不能替換。所以，compiler並沒有特地叫出**strcmp()**，而是直接把**strcmp()**換成1跟-1。

只要用第五章介紹過的輸出組合語言的指令去看組合語言就可以知道了。輸入
`gcc -S strcmp_test.c`就會出現strcmp_test.s的檔案。
(筆者注: 可以上[這個網站(gogbolt)](http://gcc.gogbolt.org)，左邊輸入C程式碼，右邊就會出現相對應的組合語言，還可以選要用什麼compiler生成)。

在筆者的環境(ubuntu 16.04 gcc 5.4)，未改造的strcmp_test.c的組合語言長這樣:

```x86asm
.LC0:
        .string "foo bar = %d\n"
.LC1:
        .string "bar foo = %d\n"
.LC2:
        .string "foo foo = %d\n"
main:
        push    rbp
        mov     rbp, rsp
        mov     esi, 1 ;不呼叫strcmp,直接變成1
        mov     edi, OFFSET FLAT:.LC0
        mov     eax, 0
        call    printf ;呼叫printf
        mov     esi, -1 ;不呼叫strcmp,直接變成-1
        mov     edi, OFFSET FLAT:.LC1
        mov     eax, 0
        call    printf ;呼叫printf
        mov     esi, 0 ;不呼叫strcmp,直接變成0
        mov     edi, OFFSET FLAT:.LC2
        mov     eax, 0
        call    printf ;呼叫printf
        mov     eax, 0 ;return 0
        pop     rbp
        ret
```
但是如果是像剛剛改造的那樣，把字串常數代入陣列的話，因為陣列是有可能在程式執行時才被修改，所以compiler無法預測結果。所以就便成了需要把strcom叫出來一起compile。看看被改後的strcmp_test.c的組合語言strcmp_test.s，就知道有呼叫**strcmp()**函數。

```x86asm
.LC0:
        .string "foo bar = %d\n"
.LC1:
        .string "bar foo = %d\n"
.LC2:
        .string "foo foo = %d\n"
main:
        push    rbp
        mov     rbp, rsp
        sub     rsp, 32
        mov     DWORD PTR [rbp-16], 7303014
        mov     DWORD PTR [rbp-32], 7496034
        lea     rdx, [rbp-32]
        lea     rax, [rbp-16]
        mov     rsi, rdx
        mov     rdi, rax
        call    strcmp ;呼叫strcmp
        mov     esi, eax
        mov     edi, OFFSET FLAT:.LC0
        mov     eax, 0
        call    printf ;呼叫printf
        lea     rdx, [rbp-16]
        lea     rax, [rbp-32]
        mov     rsi, rdx
        mov     rdi, rax
        call    strcmp ;呼叫strcmp
        mov     esi, eax
        mov     edi, OFFSET FLAT:.LC1
        mov     eax, 0
        call    printf ;呼叫printf
        mov     esi, 0 ;因為一定是0，所以不呼叫strcmp
        mov     edi, OFFSET FLAT:.LC2
        mov     eax, 0
        call    printf ;呼叫printf
        mov     eax, 0
        leave
        ret
```
因為C語言compiler很重視效率，在這種程式員看不到的地方都下了功夫。通常來說是沒問題的，可以讓執行檔變快是好事，但是這其中可能有想不到的陷阱。在寫C語言程式的時候，不要忘記有這種看不見東西在進行。

除了傳遞陣列以外，如果把**strcmp()**代入函數指標來執行的話，compiler因為不知道什麼函數會傳進函數指標，所以也無法向第一個例子一樣做最佳化。來實驗看看吧。

把
```cpp
	printf("foo bar = %d\n", strcmp("foo", "bar"));
	printf("bar foo = %d\n", strcmp("bar", "foo"));
	printf("foo foo = %d\n", strcmp("foo", "foo"));

```
改成
```cpp
int (*ptr)(const char*, const char*) = strcmp;
	printf("foo bar = %d\n", ptr("foo", "bar"));
	printf("bar foo = %d\n", ptr("bar", "foo"));
	printf("foo foo = %d\n", ptr("foo", "foo"));
```
這樣的話應該也會出現4跟-4這樣的數字。

話說為何是4跟-4的話，那是因為**strcmp()**的函數內部如下:

```cpp
int strcmp(const char *s1, const char *s2){
	while(*s1 == *s2){
    	if(*s1++ == 0){
        	return 0;
        }
    }
    //如果比較"foo"跟"bar"，在一開始的f跟b就不同
    //再來，'f'-'b' = 0x66 - 0x62 = 4
    return (*(unsigned char*) s1 - *(unsigned char*) --s2);
}
```

### 指定長度的文字比較 (strncmp)

**strcmp()**函數，就像**strcpy()**還有**strcat()**一樣有'n'的版本。也就是，**strncmp()**。

```cpp
int strncmp(const char *字串1, const char *字串2, size_t 要比較的數目)
```
這個'n'，大概是number的省略吧。使用方法跟**strcmp()**幾乎是一樣的，只是可以在第三個參數可以指定比較到第幾個字母。因為很簡單，請大家自己實驗。