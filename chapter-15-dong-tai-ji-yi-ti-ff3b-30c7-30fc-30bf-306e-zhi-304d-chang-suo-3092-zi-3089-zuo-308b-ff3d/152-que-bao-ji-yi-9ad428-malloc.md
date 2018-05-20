目前為止的範例程式，都是預先決定了陣列的大小。比如說，像下面的**one_string[]**陣列，就不能容納比"hello, world\n"還長的字串。

```cpp
char one_string[] = "hello, world\n";
```
如果想容納更長的字串，該怎麼辦呢?
```cpp
char one_string[1000000000] = "hello, world\n";
```
這樣的話，不但很浪費，而且根據系統情況還可能超過stack大小的限制而異常中止。

在這種時候，就宣告一個身為自動變數的指標變數，對應必要的記憶體開始的位址。宣告如下:

```cpp
char *one_string;
```
但是，有「大概0x00890100這個位址可以用吧(毫無根據)...」這個想法，也不能就像下面這樣宣告:

```cpp
char *one_string = (char *)0x00890100;
```
有一個可以好好的利用正式手續從系統那裡借記憶體的函數--**malloc()**。可以記成Memory ALLOCate比較好記。

```cpp
void *malloc(size_t 必要的大小);
```
**malloc()**函數，可以把大小交給系統後在系統的某處確保記憶體，並返回**void**指標。如果記憶體不足無法確保的話，則會返回**NULL**。**malloc()**函數的prototype宣告在stdlib.h裡面，所以要用時要include。

如果像下面這樣:
```cpp
char *one_string;
one_string = malloc(14);
```
的話，就可以把指標變數**one_string**當作**one_string[14]**這樣的陣列(字串)來用。

但是，如果就這樣直接用**one_string**的話，搞不好會參照到**NULL**指標也說不定。所以，在**malloc()**函數執行後一定要確認它是不是好好的確保記憶體了，寫法如下:
```cpp
if(one_string == NULL){
	printf("Error: memory\n");
    return (-1);
}
```