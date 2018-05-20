### 把值給取名字

寫實用程式的話，可能會想把值給取名字。第八章的zeller.c是利用了zeller公式來求出星期幾，得出的結果是0的話就是星期天、1的話就是星期一...並不是很好懂。

如果我們想寫一個月曆程式，希望只有在禮拜六跟禮拜天時顏色不一樣，那一定會寫成像下面光看是看不懂的程式:

```cpp
if(week == 0){
	color = 1;
} else if (week == 6) {
	volor = 2;
} else {
	color = 0;
}
```
如果說什麼「星期0的話，顏色是1」之類的會搞不清楚吧。

所以能在寫程式時給值賦予名稱的話，程式會比較好懂。比如說，第11章的define.c，利用了preprocessor來給值賦予名稱:

```cpp
#define INTEGER_NUM_1 100
#define FLOAT_NUM_1 3.14
```
所以在寫**int a = 100**的時候，可以寫成**int a = INTEGER_NUM_1**。不過define.c只是單純的範例，**INTEGER_NUM_1**跟**FLOAT_NUM_1**並沒有特別的意義，不過應用這個可以把剛剛的例子寫的比較好懂:

```cpp
#define SUN 0
#define MON 1
#define TUE 2
#define WED 3
#define THU 4
#define FRI 5
#define SAT 6

#define BLACK 0
#define RED   1
#define BLUE  2

if(week == SUN){
	color = RED;
} else if (week == SAT) {
	volor = BLUE;
} else {
	color = BLACK;
}
```
變得比較好懂了吧。但是，使用**define**這個方法是有弱點的。

因為這是preprocessor的命令，是由單純的文字置換所實現的，即使用debugger來追查bug的原因，debugger也無法理解這些名字。這是因為在compile前就全部被置換掉，在執行當中並沒留下SUN這個名字的原因。如果搞錯把**#define SUN 0**給寫成了**#define SUN "0"**，那麼**week == SUN**就會變成**week == "0"**，可能就是執行起來很奇怪的原因。

### 何謂列舉型

而這裡，C語言有一個叫做**列舉型**的，把列舉出來的數值取名的專用的data type，用法如下:

```cpp
enum tag名稱 {名稱,...};
```
或是
```cpp
enum tag名稱 {名稱=值,...};
```
範例:
```cpp
enum tag_week{
	SUN,
    MON,
    TUE,
    WED,
    THU,
    FRI,
    SAT,
};

enum tag_week week;
```
像這樣定義的話，就會自動從0開始編號，所以SUN是0, MON是1，以此類推。也可以自己定義編號:
```cpp
enum tag_week{
	SUN = 0,
    MON = 1,
    TUE = 2,
    WED = 3,
    THU = 4,
    FRI = 5,
    SAT = 6,
};
```
如果在中途開始定義號碼的話，之後沒定義號碼的數字會接著定義的號碼連號，範例:
```cpp
enum tag_color{
	BLACK,
    RED,
    BLUE,
    TELLOW = 30,
    GREEN,
};
```
以上例來說，GREEN是31。來用列舉型做一個月曆吧。

**source code**
*calendar.c*
```cpp
#include <stdio.h>

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

enum tag_week_of_day get_week_of_day(int, enum tag_month, int);

int main(){

	enum tag_week_of_day week_of_day;
	enum tag_month month;
	int monthlength[] = {-1, 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 
						 30, 31};

	int year;
	int day;

	year = 2011;

	if(((year % 4 == 0) && (year % 100 != 0)) || (year % 400 == 0)){
		monthlength[FEB] = 29;
	}

	for(month = JAN; month <= DEC; month++){//>
		day = 1;
		week_of_day = get_week_of_day(year, month, day);
		day -= week_of_day;

		printf(" Sun Mon Tue Wed Thr Fri Sat\n");
		for(;day <= monthlength[month]; day++){
			if (day <= 0 ) {
				printf("    ");
				continue;
			}
			printf("% 3d ", day);
			week_of_day = get_week_of_day(year, month, day);
			if(week_of_day == SAT){
				printf("\n");
			}
		}
		printf("\n\n");
	}
		
	return 0;
}

enum tag_week_of_day
get_week_of_day(int year, enum tag_month month, int day){
	if(month == JAN || month == FEB){
		year--;
		month += 12;
	}

	return ((year + year/4 - year/100 + 
			year/400 + (13 * month + 8) / 5 + day) % 7);
}
```

**執行結果:**
```
Sun Mon Tue Wed Thr Fri Sat
                          1 
  2   3   4   5   6   7   8 
  9  10  11  12  13  14  15 
 16  17  18  19  20  21  22 
 23  24  25  26  27  28  29 
 30  31 

 Sun Mon Tue Wed Thr Fri Sat
          1   2   3   4   5 
  6   7   8   9  10  11  12 
 13  14  15  16  17  18  19 
 20  21  22  23  24  25  26 
 27  28 

 Sun Mon Tue Wed Thr Fri Sat
          1   2   3   4   5 
  6   7   8   9  10  11  12 
 13  14  15  16  17  18  19 
 20  21  22  23  24  25  26 
 27  28  29  30  31 

 Sun Mon Tue Wed Thr Fri Sat
                      1   2 
  3   4   5   6   7   8   9 
 10  11  12  13  14  15  16 
 17  18  19  20  21  22  23 
 24  25  26  27  28  29  30 


 Sun Mon Tue Wed Thr Fri Sat
  1   2   3   4   5   6   7 
  8   9  10  11  12  13  14 
 15  16  17  18  19  20  21 
 22  23  24  25  26  27  28 
 29  30  31 

 Sun Mon Tue Wed Thr Fri Sat
              1   2   3   4 
  5   6   7   8   9  10  11 
 12  13  14  15  16  17  18 
 19  20  21  22  23  24  25 
 26  27  28  29  30 

 Sun Mon Tue Wed Thr Fri Sat
                      1   2 
  3   4   5   6   7   8   9 
 10  11  12  13  14  15  16 
 17  18  19  20  21  22  23 
 24  25  26  27  28  29  30 
 31 

 Sun Mon Tue Wed Thr Fri Sat
      1   2   3   4   5   6 
  7   8   9  10  11  12  13 
 14  15  16  17  18  19  20 
 21  22  23  24  25  26  27 
 28  29  30  31 

 Sun Mon Tue Wed Thr Fri Sat
                  1   2   3 
  4   5   6   7   8   9  10 
 11  12  13  14  15  16  17 
 18  19  20  21  22  23  24 
 25  26  27  28  29  30 

 Sun Mon Tue Wed Thr Fri Sat
                          1 
  2   3   4   5   6   7   8 
  9  10  11  12  13  14  15 
 16  17  18  19  20  21  22 
 23  24  25  26  27  28  29 
 30  31 

 Sun Mon Tue Wed Thr Fri Sat
          1   2   3   4   5 
  6   7   8   9  10  11  12 
 13  14  15  16  17  18  19 
 20  21  22  23  24  25  26 
 27  28  29  30 

 Sun Mon Tue Wed Thr Fri Sat
                  1   2   3 
  4   5   6   7   8   9  10 
 11  12  13  14  15  16  17 
 18  19  20  21  22  23  24 
 25  26  27  28  29  30  31
```
這樣就表示了一個漂亮的月曆。因為列舉型跟**int**可以互換，所以求出禮拜幾的函數**enum tag_week_of_day get_week_of_day(int year, enum tag_month month, int day)**，可以寫成**int get_week_of_day(int year, int month, int day)**也沒問題。

這次為了強調列舉型的使用，所以用**enum**宣告了。

下面這段為了讓日曆顯示起來好看的程式碼雖然有些難懂，除了這段以外是不是意外的簡單?
```cpp
		day -= week_of_day;

		printf(" Sun Mon Tue Wed Thr Fri Sat\n");
		for(;day <= monthlength[month]; day++){
			if (day <= 0 ) {
				printf("    ");
				continue;
			}

```

列舉型，**if(week_of_day == SAT)**這一行，是為了可以在星期六的時候換行。