最後的制御構造是**goto**。goto跟其他的制御文不同，是可以任意的跳到任何地方的。像BASIC這樣的語言用這個是很普通的，但是像C語言這樣結構化的語言很少用，這是因為跟其他的制御文不同，太自由了容易產生bug。那麼，來看看該怎麼寫吧。

```cpp
	goto label名稱;

label名稱:
```

把label名稱寫在程式的任何地方，goto label名稱後，就會立刻跳到label名稱執行指令。來看看範例程式吧。

**source code**
*goto.c*
```cpp
#include <stdio.h>

int main(){

	goto label_1;
	printf("This statement doesn't execute.\n");

label_1:
	return 0;
}
```
執行後可以看到沒有東西印出來，這是因為goto直接跳到label_1的return 0;，所以沒執行到printf()就結束了。可以從重複中脫出的break，只能跳出現在執行中的**while**跟**for**而已，想要一口氣從多重的迴圈中跳出的話，用**goto**比較方便。比如下例:

```cpp
while(條件){ // while 1
	//處理
    while(條件){ // while 2
    	//處理
        while(條件){ // while 3
        	//這裡的break只能跳出while 3，想從這裡跳出全部的while
            //就使用goto，如下面的程式碼
            break;
        }
    }
}
```
```cpp
while(條件){ // while 1
	//處理
    while(條件){ // while 2
    	//處理
        while(條件){ // while 3
            goto label;;
        }
    }
}
label:
```

但是，濫用的話會很難搞清楚程式的流程，也很容易造成bug，所以要注意。

**恐怖實驗: 如果用goto寫重複**

如果對寫程式不熟悉的話，可能無法好好利用**while, for**，造成濫用**goto**。那麼，為何會使用**goto**呢? 首先，是用**goto**來寫重複吧。把第七章的ascii.c拿來用**goto**改造一下:

**source code**
*ascii_2.c*
```cpp
#include <stdio.h>

int main(){
	int character = 0;

	loop:
		printf("%c ", character);
		character = character + 1;
		if((character % 16) == 0){
			printf("\n");
		}
		if(character < 256){ //>
			goto loop;
		}
	
	return 0;
}
```
執行之後可以發現結果跟ascii.c是一樣的，但是程式碼跟while的版本比起來比較不好讀了; 不過還是讀的下去吧。那麼，來把這一章求質數的程式用**goto**來寫吧。

**source code**
*prime_4.c*
```cpp
#include <stdio.h>

int main(){
	
	int i,j,end;
	int flag_not_prime; /*flag_not_prime = 0 is prime, 1 is not prime*/

	end = 100; //find prime until 100
    i = 1;

	label_1: //>find prime from 1~end
		if(i <= end){//>
		flag_not_prime = 0;
		j = 2; 
		/*check whether i is prime, if i%j==0, i is not prime. 
		  Otherwise, i is prime. j is 2~i-1*/  
	label_2:
			if(j < i){//>
				if(i % j == 0){
					flag_not_prime = 1;
					goto label_3;
				}
				j = j + 1;
				goto label_2;
			}
	label_3:
			if(i == 1){
				i = i + 1;
				goto label_1;
			}

			if(flag_not_prime == 0){
				printf("%d\n", i); //
			}
			i = i + 1;
			goto label_1;
		}
	return 0;
}
```
執行結果跟prime_3.c是相同的，但是source code變得很囉唆吧。原書作者為了寫這個也花了比其他source code更多的時間。BASIC還有assembly language是只有**goto**可以用，所以寫起程式來就是prime_4.c這種感覺。但是像C語言這樣有治癒構造可以使用的話，應該要極力避免使用goto。也因為這種理由，比C新的語言Java已經把goto刪除了。