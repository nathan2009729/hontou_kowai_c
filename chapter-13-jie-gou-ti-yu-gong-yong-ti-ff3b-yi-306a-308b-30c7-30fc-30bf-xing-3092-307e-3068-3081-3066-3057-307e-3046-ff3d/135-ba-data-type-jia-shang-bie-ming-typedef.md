使用結構體，共用體，列舉型，雖然可以取一個好懂的名字，但是意外的宣告卻變長了，像剛剛的**enum tag_week_of_day get_week_of_day(int year, enum tag_month month, int day)**這個函數就是一個例子。

因為我們把**tag_week_of_day**當作返回值的data type，所以不這麼寫不行，但是太長了很難懂。

C語言有一個可以把data type取新名字的機能: **typedef**。利用這個機能，可以寫出更簡潔的程式。來看看例子吧。

```cpp
enum tag_month{
	JAN = 1, FEB, MAR, APR,
	MAY,     JUN, JUL, AUG,
	SEP,     OCT, NOV, DEC,
};

enum tag_week_of_day{
	SUN,
	MON,
	TUE,
	WED,
	THR,
	FRI,
	SAT,
};

typedef enum tag_month month_t;
typedef enum tag_week_of_day wod_t;
```
如果像上面這樣用typedef宣告的話，之後的宣告就很簡單了，像是
```cpp
	enum tag_week_of_day week_of_day;
	enum tag_month month;
```
可以改成
```cpp
	month_t week_of_day;
	wod_t month;
```
還有
```cpp
enum tag_week_of_day 
get_week_of_day(int year, enum tag_month month, int day){

}
```
可以改成
```cpp
wod_t
get_week_of_day(int year, enum tag_month month, int day){

}
```
不用每次都寫**enum**，變得清爽多了。

**typedef**在結構體，共用體，列舉型以外，也可以使用。其實，在標準函數常常見到的**size_t**型態，也是**typedef**出來的; 另外，也可以把指標宣告隱藏起來。

比如說，有一個使用很多**int**型的程式好了。

```cpp
int *a,*b,*c,*d,*e,...;
```
就算是忘記加上*號了，在文法上也沒錯所以compiler不會出現錯誤。
```cpp
int *a,*b,*c, d,*e,...;
```
在這種時候，如果寫了**typedef int* intptr_t**的話，就可以像下面這樣寫，減少了忘記寫*這種事。

```cpp
intptr_t a, b, c, d, e,...;
```
如果濫用的話反而會變得不好懂，不過適當的使用的話可以寫出清楚的程式，所以請找出屬於自己的使用方法。

順代一提，用**define**也可以作到一樣的事情。比如:

```cpp
#define intptr_t int*
```
這樣的話
```cpp
intptr_t a;
```
就是跟typedef是一樣的。
可是
```cpp
intptr_t a, b, c, d, e,...;
```
這樣的話會只有a是指標變數，其他就只是指標變數。

原因的話，因為**define**只是文字的代換，所以
```cpp
intptr_t a, b, c, d, e,...;
```
會變成
```cpp
int* a, b, c, d, e,...;
```
這樣而已。

由於會成為bug的溫床，在這種用途的時候請一定要用**typedef**。