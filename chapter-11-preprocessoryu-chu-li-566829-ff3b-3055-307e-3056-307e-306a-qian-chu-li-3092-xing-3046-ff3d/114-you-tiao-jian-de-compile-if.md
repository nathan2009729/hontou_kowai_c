這個**define**，還有另一個使用方法; 而搭配這個使用方法的是**if**。

### 根據值的內容做有條件的compile (if)

使用**if**的話，會根據被**define**的值讓source code本身分歧(這時還沒被compile)。非常難懂，所以來實驗一下下面的範例程式吧。

**source code**
*define_2.c*
```cpp
#define TEST 1

#if TEST == 1
#include <stdio.h>

int main(){

	printf("hello, world\n");

	return 0;
}

#else
#endif
```
我們先打下面這一行:

`gcc -E define_2.c > define_2.txt`

再來看看define_2.txt的內容，會發現最底下有**main()**函數的內容。那麼，我們把**#define TEST 1**改成**#define TEST 0**實驗看看吧。我們會發現，define_2.txt的內容如下，幾乎是空的:
```cpp
# 1 "define_2.c"
# 1 "<built-in>"
# 1 "<command-line>"
# 1 "/usr/include/stdc-predef.h" 1 3 4
# 1 "<command-line>" 2
# 1 "define_2.c"
```
原因的話，在**#define TEST 1**的時候，符合**#if TEST == 1**的條件，所以會去用到**#if**~**#else**的範圍，但是當**#define TEST 0**的時候因為和**if**條件不符，所以會用到**#else**~**#endif**的範圍，也就是空的。

這次，我們把**#define TEST 1**這一行給刪掉，分別下這兩行命令:

`gcc -E -DTEST=1  define_2.c > define_2.txt`
`gcc -E -DTEST=0  define_2.c > define_2.txt`

這兩行命令所產生的define_2.txt內容不同，-DTEST=1是當作有**main()**函數，-DTEST=0則是沒有。像這樣，**define**的內容也可以透過preprocessor的option指定。這個機能非常特別，C語言以外很少看到。比如說程式中分別寫了Windows用還有Linux用兩種，可以使用Makefile的option來對應兩方的環境。為了這種用途，**define**在沒有寫該置換什麼的話，那麼就會自動補1，也就是說，

```cpp
#define TEST
```
跟
```cpp
#define TEST 1
```
是相同的。

### 調查是否已經定義過的條件compile
就算不用一個一個的寫**if**後面的條件，用**ifdef**這命令的話就跟**#if TEST == 1**是同樣的效果，反過來也有跟**#if TEST == 0**一樣意思的**ifndef**可以用。範例如下:

```cpp
#ifdef TEST
#include <stdio.h>

int main(){

	printf("hello, world\n");

	return 0;
}

#else
#endif

#ifndef TEST
#else
#include <stdio.h>

int main(){

	printf("hello, world\n");

	return 0;
}
#endif
```
(筆者注: 筆者不知道這樣寫有什麼意義，因為不管TEST是1是0都是執行一樣的程式，再說，#define跑到哪了呢？)

### 已經定義的define

gcc的命令已經在內部define了許多的字串。比如說linux的gcc，就算gcc什麼option都不加，也在preprocess的時候**#define linux 1**。也就是說，如果想寫一個可以跨平台(linux,windows等不同作業系統)的程式，可以像下面這樣寫:

```cpp
#ifdef linux
	//Linux專用處理
#else
	//Linux以外的OS的專用處理
#endif
```
如果打了以下命令

```
gcc -dM -xc -E /dev/null
```
就可以列出linux已經#define好的字串，筆者電腦顯示如下:
```
#define __SSP_STRONG__ 3
#define __DBL_MIN_EXP__ (-1021)
#define __UINT_LEAST16_MAX__ 0xffff
#define __ATOMIC_ACQUIRE 2
#define __FLT_MIN__ 1.17549435082228750797e-38F
#define __GCC_IEC_559_COMPLEX 2
#define __UINT_LEAST8_TYPE__ unsigned char
#define __SIZEOF_FLOAT80__ 16
#define __INTMAX_C(c) c ## L
#define __CHAR_BIT__ 8
#define __UINT8_MAX__ 0xff
#define __WINT_MAX__ 0xffffffffU
#define __ORDER_LITTLE_ENDIAN__ 1234
#define __SIZE_MAX__ 0xffffffffffffffffUL
#define __WCHAR_MAX__ 0x7fffffff
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_1 1
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_2 1
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_4 1
#define __DBL_DENORM_MIN__ ((double)4.94065645841246544177e-324L)
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_8 1
#define __GCC_ATOMIC_CHAR_LOCK_FREE 2
#define __GCC_IEC_559 2
#define __FLT_EVAL_METHOD__ 0
#define __unix__ 1
//中間好多行...
#define linux 1
#define __SSE2__ 1
#define __LDBL_MANT_DIG__ 64
#define __DBL_HAS_QUIET_NAN__ 1
#define __SIG_ATOMIC_MIN__ (-__SIG_ATOMIC_MAX__ - 1)
#define __code_model_small__ 1
#define __k8__ 1
#define __INTPTR_TYPE__ long int
#define __UINT16_TYPE__ short unsigned int
#define __WCHAR_TYPE__ int
#define __SIZEOF_FLOAT__ 4
#define __UINTPTR_MAX__ 0xffffffffffffffffUL
#define __DEC64_MIN_EXP__ (-382)
#define __INT_FAST64_MAX__ 0x7fffffffffffffffL
#define __GCC_ATOMIC_TEST_AND_SET_TRUEVAL 1
#define __FLT_DIG__ 6
#define __UINT_FAST64_TYPE__ long unsigned int
#define __INT_MAX__ 0x7fffffff
#define __amd64__ 1
#define __INT64_TYPE__ long int
#define __FLT_MAX_EXP__ 128
#define __ORDER_BIG_ENDIAN__ 4321
#define __DBL_MANT_DIG__ 53
#define __SIZEOF_FLOAT128__ 16
#define __INT_LEAST64_MAX__ 0x7fffffffffffffffL
#define __GCC_ATOMIC_CHAR16_T_LOCK_FREE 2
#define __DEC64_MIN__ 1E-383DD
//之後省略
```
如果想寫可以在複數環境下執行的程式，利用這命令，可以利用已經定義好的值來寫程式。

**恐怖實驗:如果已經define的字串跟變數名相同**

利用像是**#define linux 1**等等的話，在寫需要在複數環境下執行的程式時非常便利，但是其中有陷阱。下面的範例程式，會發生什麼事呢?

**source code**
*define_3.c*
```cpp
#include <stdio.h>

int main(){

	char linux[16] = "For Linux!";

	printf("%s\n", linux);

	return 0;
}
```
在compile時會出現以下的警告跟錯誤信息
```
define_3.c: In function ‘main’:
define_3.c:5:7: error: expected identifier or ‘(’ before numeric constant
  char linux[16] = "For Linux!";
       ^
define_3.c:7:9: warning: format ‘%s’ expects argument of type ‘char *’, but argument 2 has type ‘int’ [-Wformat=]
  printf("%s\n", linux);
         ^
```
這是因為系統已經**#define linux 1**了，所以對我們的程式碼做置換，變成了
```cpp
char 1[16] = "For Linux!";
```
所以就產生奇怪的結果。

在gcc裡定義的大部分#define都有__(兩個underscore)，所以不太發生這種事，但還是注意一下。除了**linux**可能會不小心跟**#define**重複到，其他還有**i386**,**unix**也是跟**linux**同樣的情形。