利用電腦強力的情報處理能力，可以用很快的速度重複同一個處理。「重複」有**while, do while, for**三個種類，有各自的特徵。**while**在英語是「當~」的意思，在滿足條件的時候，進行重複的處理。

### 基本的while

了解if的話，文法是一樣的所以很簡單。只要像下面這樣寫，在滿足條件的時候，處理都會重複。

```cpp
while (條件){
	//處理
}
```
因為在第七章的範例已經出現過**while**，所以來試試不一樣的例子吧。下面是求出1加到10的範例程式。喜歡數學的人的話會知道這題可以用等差數列的公式簡單的求出，這裡是展現重複的範例，所以用**while**。

順代一提，使用公式的話是(10*(1+10))/2來求出。

**source code**
*while.c*
```cpp
#include <stdio.h>

int main(){
	int answer;
	int cnt;

	answer = 0;
	cnt = 0;
	while(cnt < 10){ //>
		answer = answer + (cnt + 1);
		cnt = cnt + 1;
	}
	printf("answer = %d\n", answer);

	return 0;
}
```


**執行結果:**
```
answer = 55
```

再詳細的說明一下這程式吧。變數**answer**跟**cnt**在while開始之前都是0。所以在一開始0 &lt; 10是滿足的，就會執行block內的指令。block裡面，是把**answer**加上**cnt**再加上1代入**answer**。也就是說，第一次是**answer = 0 + (0 + 1)**。**注意，()是不需要的，只是為了好懂加上去的而已。**

接下來**cnt**的值加1，再次檢查**while**內的條件是否滿足。因為1 &lt; 10，依然滿足條件，所以block內的東西還會再實行一次，直到**cnt &lt; 10**為止。

這個while執行的過程以表格整理的話就像下面這樣。

| answer =  | cnt = |           代入answer的式子          |
|:---------:|:-----:|:---------------------------------:|
|     0     |   0   |            0 + (0 + 1)            |
|     1     |   1   |            1 + (1 + 1)            |
|     3     |   2   |            3 + (2 + 1)            |
|     6     |   3   |            6 + (3 + 1)            |
|     10    |   4   |            10 + (4 + 1)           |
|     15    |   5   |            15 + (5 + 1)           |
|     21    |   6   |            21 + (6 + 1)           |
|     28    |   7   |            28 + (7 + 1)           |
|     36    |   8   |            36 + (8 + 1)           |
|     45    |   9   |            45 + (9 + 1)           |
|     55    |   10  | 不滿足cnt &lt; 10，所以不再執行block |

對answer代入的式子，就是1 + 2 + 3 + 4 + 5 + 6 + 7 + 8 + 9 + 10的照順序加下去。因為意外的囉唆，在無法理解的時候就自己寫一張表，像下面的程式這樣插入**printf()**來追蹤值。

```cpp
	while(cnt < 10){ //>
		answer = answer + (cnt + 1);
        printf("answer = %d + (%d + 1)\n", answer, cnt);
		cnt = cnt + 1;
        printf("cnt = %d + 1\n", cnt);
	}
```

**恐怖實驗: 不會結束的無限loop(又稱無窮迴圈)**

雖然寫程式一定會遇到重複處理，但是走錯一步的話可能就會發生重大的bug，最常見的就是被稱為**無窮迴圈**的現象: 不管經過多久都滿足條件，永遠都會執行while的block。

比如說，把這次的while.c中的**while(cnt &lt; 10)**換成**while(cnt &lt; cnt + 10)**的話，不管何時都會滿足條件，所以程式永遠不會結束。常見電腦程式的「freeze」「固定住了」等等大半都是因為無窮迴圈這個bug。複雜的程式在通常範圍的利用下是可以好好的返回值然後正常結束的，但是在特定的條件下可能就會變成無窮迴圈。

不使用重複的話就無法寫出像樣的程式，請小心不注意的話就會發生bug。機會難得，可以試試看把**while**內的條件換一下實驗看看無窮迴圈。想結束的話，只要同時按下Ctrl跟C就可以了，請放心。

這次的範例程式改成**while(cnt != 10)**也是沒有錯的，但是如果我們把程式改成cnt每一次是+3而不是+1的話，就會產生無窮迴圈。但是寫**while(cnt &lt; 10)**至少不會出現無窮迴圈。用「重複」的時候盡量用&lt;或&gt;來寫條件式。

### 停止重複 break

重複處理在不滿足條件的話就會自動結束。但是，我們有時會希望在不滿足條件之前，遇到特定的狀況時也可以結束重複。這種時候，要用到的就是break。

在「重複」中遇到**break**的話，那個時間點就會無條件結束重複。

```cpp
while(條件){
	//處理
	break;
}
```
因為這個特性，break通常與if一起使用，範例如下:

```cpp
while(條件){
	//處理
    if(條件){
    	break; //在遇到這個條件時中止重複
    }
}
```
機會難得，讓我們介紹求質數的範例程式吧。

**source code**
*prime.c*
```cpp
#include <stdio.h>

int main(){
	
	int i,j,end;
	int flag_not_prime; /*flag_not_prime = 0 is prime, 1 is not prime*/

	end = 100; //find prime until 100

	i = 1;

	while(i <= end){ //>find prime from 1~end
		flag_not_prime = 0;
		j = 2; 
		/*check whether i is prime, if i%j==0, i is not prime. 
		  Otherwise, i is prime. j is 2~i-1*/  
		while(j < i){//>
			if(i % j == 0){
				flag_not_prime = 1;
				break;
			}
			j = j + 1;
		}
		if(flag_not_prime == 0){
			printf("%d\n", i); //
		}
		i = i + 1;
	}
	return 0;
}
```
執行結果會印出1還有其他2~100內的質數; 1嚴格來說不是質數，下一節講述另一個用在重複上的keyword: **continue**時，會說明如何把1去掉。

這個程式最初的重複條件，是為了調查1~100內有沒有質數。
```cpp
	while(i <= end){ //>find prime from 1~end
		i = i + 1;
	}
```
這個**while**的block內的另一個**while**，則是從2開始當除數進行mod看能不能整除。要說為何是從二開始的話，一方面是因為1一定會整除，一方面質數的定義是「除了1跟自身以外都不能整除的數」。

```cpp
		j = 2; 
		/*check whether i is prime, if i%j==0, i is not prime. 
		  Otherwise, i is prime. j is 2~i-1*/  
		while(j < i){//>
			if(i % j == 0){
				flag_not_prime = 1;
				break;
			}
			j = j + 1;
		}
```
假設現在變數**i**是4的話，這個**while**內的block會進行4%2。由於4%2會是0代表整除，所以**if**這個block裡面的變數**flag_not_prime**會被代入1。再來，因為被整除已經可以確定它不是質數，即使繼續4%3也沒意義，所以直接用break從這個除數字的重複中跳出。跟break沒什麼直接關係，**flag_not_prime**這種只會有0或1兩種值的變數，我們稱之為「flag」。

雖然出現兩個**while**進行重複很複雜，不過可以瞬間算出人來計算需要花很多時間的東西，果然是很像電腦的範例程式。如果覺得困難的話，可以在途中插入**printf()**，用眼追蹤執行。假設我們把變數**end**的值換成5，把重複的部份寫成下面這樣:

```cpp
	while(i <= end){ //>find prime from 1~end
		printf("\nTest for %d\n", i);
		flag_not_prime = 0;
		j = 2; 
		/*check whether i is prime, if i%j==0, i is not prime. 
		  Otherwise, i is prime. j is 2~i-1*/  
		while(j < i){//>
			printf("    Can %d divide by %d?\n", i, j);
			if(i % j == 0){
				printf("      Yes, not prime\n");
				flag_not_prime = 1;
				break;
			}
			printf("       No, continuing\n");
			j = j + 1;
		}
		if(flag_not_prime == 0){
			printf("     %d is prime\n", i);
		}
		i = i + 1;
	}
```
**執行結果:**
```
Test for 1
     1 is prime

Test for 2
     2 is prime

Test for 3
    Can 3 divide by 2?
       No, continuing
     3 is prime

Test for 4
    Can 4 divide by 2?
      Yes, not prime

Test for 5
    Can 5 divide by 2?
       No, continuing
    Can 5 divide by 3?
       No, continuing
    Can 5 divide by 4?
       No, continuing
     5 is prime
```

### 繼續重複(continue)

除了break，還有另一個在重複中使用的**continue**。break是在用的當下停止重複，continue是在用的當下回到重複的開頭重來一次，如下圖所示:

```cpp
while (條件){//1
	//處理
    continue;//執行到這一句時回到1
}
```
也跟break一樣，通常搭配if來使用。
```cpp
while (條件){//1
	//處理
    if(條件){
    	continue;//執行到這一句時回到1
    }
}
```
雖然prime.c這個範例程式是拿來求質數的，但是一開始會印出1。嚴格來說，質數為了質因數分解的唯一性而不含1。在這裡，我們用continue來讓程式不顯示出1。

**source code**
*prime_2.c*
```cpp
#include <stdio.h>

int main(){
	
	int i,j,end;
	int flag_not_prime; /*flag_not_prime = 0 is prime, 1 is not prime*/

	end = 100; //find prime until 100

	i = 1;

	while(i <= end){ //>find prime from 1~end
		flag_not_prime = 0;
		j = 2; 
		/*check whether i is prime, if i%j==0, i is not prime. 
		  Otherwise, i is prime. j is 2~i-1*/  
		while(j < i){//>
			if(i % j == 0){
				flag_not_prime = 1;
				break;
			}
			j = j + 1;
		}

		if(i == 1){
			i = i + 1;
			continue;
		}

		if(flag_not_prime == 0){
			printf("%d\n", i); //
		}
		i = i + 1;
	}
	return 0;
}

```
這樣顯示出的結果就是列出了從2開始到100的質數。本來應該把**i = 1;**改成**i = 2;**就好了，這裡為了舉例才用了continue。在**continue**之前如果不執行**i = i + 1;**的話變數**i**的值將永遠不會變，就會成為無窮迴圈。break還有continue都是把制御流程給強制改變，注意不要發生這種事。

### 求圓周率

結束了while的說明，來介紹一下求圓周率的範例程式吧; 雖然是要用到一些之後的章節才會講到的內容。要用到的之後才會講的東西，就只有「cast(強制轉型)」跟「亂數」而已，對數學有興趣的應該很開心吧。

**source code**
*pi.c*
```cpp
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

int main(){

	double x, y, insect_cnt;
	int i;

	insect_cnt = 0.0;
	i = 0;

	srand(time(NULL));

	while(i < 100000000){//>
		x = (double) rand() / RAND_MAX; //x get a random number of 0~1
		y = (double) rand() / RAND_MAX;
		if(x * x + y * y < 1.0){ //>
			//
			insect_cnt = insect_cnt + 1.0;
		}
		i = i + 1;
	}

	// insect_cnt / i = probability of x^2 + y^2 = 1.0 
	//*4 = 1 cycle
	printf("%f\n", insect_cnt / i * 4.0);

	return 0;
}
```
**執行結果:**
```
3.141654
```

這個程式是利用到了「Monte Carlo method」來求出圓周率的程式。所謂Monte Carlo method，就是把跟機率沒什麼關係的問題用機率解決。

```cpp
		x = (double) rand() / RAND_MAX; //x get a random number of 0~1
		y = (double) rand() / RAND_MAX;
		if(x * x + y * y < 1.0){ //>
		}
```
求圓周率的情況下，先隨便用亂數決定x座標y座標，再代入圓方程式。因為圓方程式是x\^2+y^2 = r^2(圓心在座標(0, 0))，所以**x \* x + y * y &lt; 1.0**代表亂數的x, y落在圓心(0, 0)半徑為1的圓。

這個處理在經過相當次數(越多次越準)的重複之後，數有幾次會收斂在這個圓裡。這個程式，以**(double) rand() / RAND_MAX**來取得介於0~1之間的亂數。由於這樣只會取到正數，所以嚴格來說我們算的是收斂在1/4圓(就是第一象限那1/4)裡的次數。

重複結束後，我們計算落在1/4圓的機率(**insect_cnt / i**)，把它再*4，就是亂數落在整個圓的機率。如果改變重複的次數，精度也會跟著改變; 比如說重複次數1000，結果就會變成3.12這種不是很準的數字。來改變條件實驗看看吧。

順代一提我們是用執行的時刻來當「亂數種子(seed)」，所以不同時間冊同一程式結果會有些不同。學習到現在能做的事情也越來越廣泛，務必實驗一下除了範例以外的程式。