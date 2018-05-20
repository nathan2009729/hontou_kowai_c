最近用了虛擬記憶體的OS，都導入了copy on write，只是執行**malloc()**或**calloc()**可能無法確保記憶體。

總之，先確保記憶體，根據場合把這些記憶體通通填上0並用程式來看看，確定實際上會進行從程式寫入記憶體這個動作，可以高速化並利用記憶體的效率。

趕快來實驗看看吧。(如果這個實驗有什麼寫錯的話，記憶體少的電腦會變得不穩定，如果覺得不安的話看看實行例就好)

**source code**
*memory_3.c*
```cpp
#include <string.h>

int main(){

	int i;
	char *ptr;

	ptr = malloc(2147483647); //get 2GB memory
	if(ptr == NULL){
		printf("Memory allocation error!\n");
		return -1;
	}

	getchar(); // press Enter
	for(i = 0; i < 2147483647; i++){//>
		ptr[i] = 1;
	}	

	free(ptr);

	return 0;
}
```
linux可以在執行後立刻下`top`指令來看看記憶體用量，在原書作者按下Enter之前是316KB，在按下後是1GB。這是因為虛擬記憶體用了一部分的硬碟當作記憶體，所以是不會到2GB的。

這意味著就算很無理確保記憶體也不會立刻用到記憶體，所以要用程式檢查記憶體是否不足是很難的。所以用了許多**malloc()**的程式有時處理會變得慢。就算虛擬記憶體再怎麼多，也要好好考慮過再確保。