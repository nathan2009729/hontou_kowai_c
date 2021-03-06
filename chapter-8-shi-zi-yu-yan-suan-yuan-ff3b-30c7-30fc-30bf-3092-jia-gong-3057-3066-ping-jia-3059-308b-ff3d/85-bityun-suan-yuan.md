現在開始介紹的運算元，可能不需要馬上用到吧。不過bit運算可說是C語言的特徵，記下來也沒損失。

### 什麼是bit運算

電腦進行記憶跟運算的最小單位被稱為bit(把數字用2進位表現後1位數就是1 bit)，對bit直接進行運算就叫**bit運算**。這與普通的運算有哪裡不同呢?

普通的使用10進位來計算的話，不太會意識到2進位是怎樣的狀態。10進位的話，1 + 1的結果2並不會造成進位，但2進位的話1 + 1會變成10，是會進位的。簡單來說，bit運算就是對1bit的每個對象進行運算但不造成進位。順代一提，雖然不會進位，

為了不進位的bit運算，CPU上裝了簡單的迴路，所以以前為了高速會用bit運算。但是現在電腦、伺服器速度都很快，不太會為了速度特地利用bit運算。那麼，是用在哪呢?

像網路的IP位址就是有利用到bit運算。詳細的說明交給網路的專門書，這裡只做簡單的說明。

//以後再補p177頁最下方網路的說明

總之，網路上需要把IP位址跟子網路遮罩做or(論理積)的計算，比如說IP位址是192.168.0.1，子網路遮罩是255.255.255.0，那麼就可以得出網路位址192.168.0.0。論理積是兩個1碰在一起才會是1，其他情況是0。因為沒有IP位址的知識所以很難理解，首先來實驗看看論理積的範例程式吧。

**source code**
*and.c*
```cpp
#include <stdio.h>

int main(){
	unsigned char a;
	unsigned char b;

	a = 192;
	b = 255;
	printf("first octet is %u\n", a&b);

	a = 168;
	b = 255;
	printf("second octet is %u\n", a&b);

	a = 0;
	b = 255;
	printf("third octet is %u\n", a&b);

	a = 1;
	b = 0;
	printf("fourth octet is %u\n", a&b);

	return (0);
}
```

**執行結果:**
```
first octet is 192
second octet is 168
third octet is 0
fourth octet is 0
```
從上面看下來，表示「192 168 0 0」這個像網路位址的值。

### 論理積

and.c這個範例程式利用到的是論理積(and)。把論理積的結果做成表格就像下圖這樣。

| 值1 | 值2 | 結果(論理積) |
|-----|:---:|:------------:|
| 0   |  0  |       0      |
| 1   |  0  |       0      |
| 0   |  1  |       0      |
| 1   |  1  |       1      |

需要求出每個bit的論理積的話，需要用到&運算元。知道了這個表，再重新看一次網路位址的計算會有更深的理解。IP位址是192 168 0 1、子網路遮罩是255 255 255 0。換成2進位如下表:

|                |          |          |          |          |
|----------------|----------|----------|----------|----------|
| IP位址         | 11000000 | 10101000 | 00000000 | 00000001 |
| 子網路遮罩     | 11111111 | 11111111 | 11111111 | 00000000 |
| 結果(網路位址) | 11000000 | 10101000 | 00000000 | 00000000 |


### 論理和(or)

**論理和**是只要有一方是1，結果就會是1的運算。把論理和的結果做成表格就像下圖這樣。

| 值1 | 值2 | 結果(論理和) |
|-----|:---:|:------------:|
| 0   |  0  |       0      |
| 1   |  0  |       1      |
| 0   |  1  |       1      |
| 1   |  1  |       1      |

需要求出每個bit的論理和的話，需要用到 **| 運算元**。

### 排他的論理和(xor)

**排他的論理和**是只要值1跟值2不同，結果就會是1的運算。

| 值1 | 值2 | 結果(排他的論理和) |
|-----|:---:|:------------:|
| 0   |  0  |       0      |
| 1   |  0  |       1      |
| 0   |  1  |       1      |
| 1   |  1  |       0      |

需要求出每個bit排他的論理和的話，需要用到 **^ 運算元**。

### 論理否定

**論理否定**是在一元運算元的小節中介紹過的進行「反轉」的運算元。利用了**~運算元**，正確來說是**補數運算元**。雖然已經實驗過知道它是怎樣的動作，但還是整理一下表吧。因為是一元運算元，會把給予的值反轉。

| 值  | 結果(否定)|
|-----|:-------:|
| 0   |     1   |
| 1   |     0   |

再來把用到論理積、論理和、排他的論理和、論理否定的範例程式給實行一下吧，data type是用**unsigned char**。會用**unsigned char**是因為**char**會處理負數，而是不是負數會被2進位最左端的bit影響，這對我們做實驗不方便。

**source code**
*bit.c*
```cpp
#include <stdio.h>

int main(){
	unsigned char a;
	unsigned char b;
	unsigned char answer;

	a = 5;//00000101
	b = 3;//00000011

	answer = a & b;
	printf("00000101 &(and) 00000011 is %u\n", answer);

	answer = a | b;
	printf("00000101 |(or) 00000011 is %u\n", answer);

	answer = a ^ b;
	printf("00000101 ^(xor) 00000011 is %u\n", answer);

	a = 1;//00000001
	answer = ~a;
	printf("~00000001 is %u\n", answer);

	return 0;
}
```

**執行結果:**
```
00000101 &(and) 00000011 is 1
00000101 |(or) 00000011 is 7
00000101 ^(xor) 00000011 is 6
~00000001 is 254
```

實際執行看看的話應該不知不覺能理解吧。對於2進位不是很清楚的人，Mac OS還有Windows的計算機程式可以在2進位跟16進位間切換，很方便。還有，第九章重複執行的範例會介紹把10進位換成2進位的範例程式，請多加利用。

### shift運算

式子與運算元這一章的最後，是**shift運算**。shift運算是把bit往左或往右移一位，舉個例子，0010往左shift是0100，往右shift是0001。2進位的話，往左shift 1位數值會是原來的兩倍，往右shift 1位數值會是原來的一半，往左shift的符號是**<<**、往右是**>>**。

趕快來實驗一下吧。

**source code**
*shift.c*
```cpp
#include <stdio.h>

int main(){

	unsigned int n;

	n = 32768;//00000000000000001000000000000000
	printf("one left shift %u\n", n << 1);
	printf("rwo left shift %u\n", n << 2);

	printf("one right shift %u\n", n >> 1);
	printf("rwo right shift %u\n", n >> 2);

	return 0;
}
```
**執行結果:**
```
one left shift 65536
rwo left shift 131072
one right shift 16384
rwo right shift 8192
```

往左shift 1位會是32768的2倍65536，往左shift 2位會是32768的4倍131072。往右shift 1位會是32768的一半16384，往右shift 2位會是32768的1/4倍8192。shift運算可以當作乘和除的代替。CPU有可能沒有為了乘跟除所設計的迴路跟命令，在這情況下，C runtime的乘利用左shift跟加法，除利用右shift跟減法來實行。

### bit運算的注意點

bit運算是只能用在整數型的data type上的，**float**這種浮點小數的型態不能用。原因的話，浮點小數型態的data type的記憶體分成了三部份:符號、指數、有效數組成(IEEE754)，放進去的1跟整數型2進位的1意義不同。還有，在右shift的時候依照最左邊的bit的處理又可以分成算術右移跟邏輯右移，這根據語言而有所不同。但是，C語言是一存在compiler跟CPU的構造上，這就不是programmer可以選擇的。因為如此，用最左邊沒有特別意義的**unsigned**這個data type比較安全。