關於**陣列**在第七章做過一些說明，這裡再重新說一次吧。

### 陣列是同一個data type的變數併排在一起的東西

陣列是同一個data type的變數在連續的記憶體上併排在一起的東西。普通變數的宣告是:

```cpp
data_type 變數名;
```
比如:
```cpp
int number;
```
#### 記憶體示意圖

32bit的CPU電腦，一般的**int**是4 bytes(32bit)，記憶體會長的像下圖這樣，這個圖縱軸是記憶體address，橫軸是該address記憶體的bit。

![Imgur](http://i.imgur.com/7sHGv9S.png)

上圖0xFFFFFFFC這一塊記憶體會是32bit的CPU的位址中最高的，而0x00000000這一塊通常是不給程式使用的。

假設上面宣告的變數**number**在address 0x00001040這塊記憶體上，而且**number = 1000;**(2進位是1111101000)，那麼記憶體會變成下圖:

![Imgur](http://i.imgur.com/Si79vrf.png)

address 0x00001044跟address 0x00001036可能會被其他程式用到。

#### 陣列的宣告

用法如下:
```cpp
data_type 變數名[元素數量];
```
範例:
```cpp
int number_array[8];
```
#### 記憶體示意圖

32bit的CPU電腦，一般的**int**是4 bytes(32bit)，記憶體會長的像下圖這樣。圖中**number_array**這個陣列佔有了0x00001040~0x0000105C這組address。這裡也是每4bytes跳一個。

![Imgur](http://i.imgur.com/4LtDsoS.png)

灰色部份由下到上八個長條，分別是number_array[0]到number_array[7]可使用的記憶體(number_array[0]就是address 0x00001040的那一整條，以此類推)。address 0x00001060跟address 0x00001036可能會被其他程式用到。

不過這樣實在是太難看了，所以我們在用圖解釋陣列的時候，都是把一個address當成一格，由左往右排，如下圖:

![Imgur](http://i.imgur.com/lRx8QY3.png)

0就是代表number_array[0]，以此類推。0~8這些數字被稱為「index」，可以拿來指定要存取陣列的哪個元素。也可以在初始化陣列時放值(這些值被稱為初期值)進去，方法如下:

```cpp
data_type 變數名[元素數量] = {變數[0]的值, 變數[1]的值...變數[元素數量-1]的值};
```

範例:

```cpp
int number_array[8] = {-3, -2, -1, 0, 1, 2, 3, 4};
```
在初始化時，如果初期值寫的比元素數目還少，剩下沒寫的都是0。舉例:

```cpp
int number_array[8] = {-1};
```
那麼這個陣列的初始化結果會如下圖所示:

![Imgur](http://i.imgur.com/UN11Z5A.png)

還有，寫了初期值可以省略元素數量。如下例:

```cpp
int number_array[] = {0, 1, 2, 3};
```
結果如下所示:

![Imgur](http://i.imgur.com/qzrRXw9.png)

到目前為止說明了陣列的概念，來搭配範例程式加深理解吧。這個程式內容跟第9章的求1加到10的和是相同的。

**source code**
*for_array.c*
```cpp
#include <stdio.h>

int main(){

	int number_array[] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10, -1};
	//-1 is sentinel value

	int i, answer;

	for(i = 0, answer = 0; number_array[i] != -1; i++){
		answer += number_array[i];
	}
	printf("answer = %d\n", answer);

	return 0;
}
```
**執行結果:**
```
answer = 55
```
正常的顯示了55。陣列**number_array[]**的最後放入了-1，這個值被稱為sentinel value(又被稱為flag value, trip value, rogue value, signal value, dummy data等等，日文漢字是"番兵")，如果loop(重複)時遇見了這個值，那麼就會停止loop。sentinel value也被用在像是linked list的最尾端，用**NULL**表示。在這個例子因為沒使用負數，所以sentinel value設成-1，不過陣列中有其他負數的話就不能設成這個了，通常是把中止條件換成重複的回數。

如果是把重複的回數當成中止條件的話，可以不用直接指定數字，而是利用**sizeof**運算元。因為int陣列一個元素是4 bytes(一般來說)，所以**i &lt; sizeof(number_array)**會是10的4倍，也就是會重複40次，結果會變得奇怪。在這種狀況下，寫成

**i &lt; sizeof(number_array)/sizeof(number_array[0])**

就可以了，不管什麼size的data type都可以用這寫法，很方便，請記起來。

### 字串

第七章也說明過，C語言沒有字串型這種data type，是用char的陣列來代替。就算是字串，基本上也是陣列，所以文法也沒不同，只是，在初始化的時候尾端會自動代入'\0'。舉例:

```cpp
char one_string[] = "hello, world\n";
```
它的陣列會長的像下面這樣:

![Imgur](http://i.imgur.com/1kPXDzU.png)

因為是char，所以一格佔1 byte，以32bit的系統看起來就像下圖這樣:

![Imgur](http://i.imgur.com/JpQm9fV.png)

請由下往上，由右往左，每八個為一組看。每一組代表陣列的一個元素，列舉如下:

* 0x00001040 0~7是0x68，就是ASCII code的h(0110是6, 1000是8)
* 0x00001040 8~15是0x65，就是ASCII code的e(0110是6, 0101是5)
* 0x00001040 16~23是0x6C，就是ASCII code的l(0110是6, 1100是C)
* 0x00001040 24~31是0x6C，就是ASCII code的l(0110是6, 1100是C)
* 0x00001044 0~7是0x6F，就是ASCII code的o
* 0x00001044 8~15是0x2C，就是ASCII code的,
* 0x00001044 16~23是0x20，就是ASCII code的' '
* 0x00001044 24~31是0x77，就是ASCII code的w
* 0x00001048 0~7是0x6F，就是ASCII code的o
* 0x00001048 8~15是0x72，就是ASCII code的r
* 0x00001048 16~23是0x6C，就是ASCII code的l
* 0x00001048 24~31是0x64，就是ASCII code的d
* 0x0000104C 0~7是0x0A，就是ASCII code的\n
* 0x0000104C 8~15是0x00，就是ASCII code的\0

了解內容的話，來執行範例程式當作是有趣的實驗吧。即使無法詳細理解，知道電腦內部把所有的情報當數字對待就可以了。

**source code:**
*intworld.c*
```cpp
#include <stdio.h>

int main(){

	int number_array[] = {0x6c6c6568, 0x77202c6f,
						  0x646c726f, 0x0000000a};

	//if PowerPC, use below
	/*
	int number_array[] = {0x68656c6c, 0x6f2c2077,
						  0x6f726c64, 0x0a000000};
	*/
	
	printf("%s", (char *)number_array);

	return 0;
}
```
**執行結果:**
```
hello, world
```
指定了0x6c6c6568, 0x77202c6f,0x646c726f, 0x0000000a這些數值，出來的卻是hello, world。雖然不可思議，但是這個實驗可以感覺到「什麼都是記憶體的操作」吧。