我們來介紹linked list作為動態記憶體的使用方式的例子。

### 何謂linked list

比如說，要寫住所錄的話，雖然也可以使用結構體陣列，但是這樣要刪除資料很麻煩。

假設現在有**address[0]**,**address[1]**,**address[2]**三筆資料好了，如果刪除**address[1]**的話，就必須把**address[2]**的資料往前移到**address[1]**的位置。

再假設，有一筆新資料想插入**address[1]**跟**address[2]**之間，那麼就得把**address[2]**的資料移動到**address[3]**的位置，再把新資料插入到**address[2]**的位置。

這樣寫出來，就會發現很麻煩了。而這裡如果用linked list這個資料結構就會很方便。詳細的說明就交給演算法與資料結構的書，簡單來說，linked list就是一個實際上不是在記憶體上連續配置，但看起來像連續並排的陣列的東西，如下圖。

![Imgur](http://i.imgur.com/AyIm9FK.png)

像這樣利用指標來讓資料連在一起，如果要連續讀取的話就用指標一個一個往前指。如果要刪除data 2的話，就把data 2給**free()**掉，把data 1的指向下個資料的指標覆寫上data 3的位址。

### 寫住所錄程式

我們就應用這個來寫一個簡單的住所錄程式吧。雖然是有些長的程式，不過實際執行看看再來解讀應該可以理解。為了不讓程式碼不好讀，特地讓**main()**進行了許多處理，error的處理比較簡潔。如果是作為實際應用的程式，機能跟error處理還不十分完善，作為範例程式也是可以了。

**source code**
*address.c*
```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_CHARACTER (256)

struct tag_address{
	int no;
	char name[MAX_CHARACTER];
	char addr[MAX_CHARACTER];
	struct tag_address *next;//指向下個data的指標
};

//記憶list開頭的指標變數
//程式啟動時什麼都沒指所以是NULL
//
struct tag_address *address = NULL;

int enter(int, const char *, const char *);
struct tag_address *find(int);
int delete(int);
void show_all(void);
void delete_all(void);

int main(){
	
	struct tag_address *address_data;
	char in_buf[8];
	char in_no[MAX_CHARACTER];
	char in_addr[MAX_CHARACTER];
	char in_name[MAX_CHARACTER];
	char *c;
	int no, menu_no;

	do{
		printf("Input 1[ret] to enter, 2[ret] to search, "
			   "3[ret] to delete, 4[ret] to all, 9[ret] to quit.\n");
		printf("Input menu number> ");
		fgets(in_buf, sizeof(in_buf), stdin);
		menu_no = strtol(in_buf, NULL, 10);
		switch (menu_no){
		case 1:

			printf("Input number> ");
			fgets(in_no, sizeof(in_no), stdin);
			no = strtol(in_no, NULL, 10);

			printf("Input name> ");
			fgets(in_name, sizeof(in_name), stdin);
			c = strchr(in_name, '\n');
			if(c != NULL){
				*c = '\0';
			}

			printf("Input address> ");
			fgets(in_addr, sizeof(in_addr), stdin);
			c = strchr(in_addr, '\n');
			if(c != NULL){
				*c = '\0';
			}

			if(enter(no, in_name, in_addr) == -1){
				printf("error\n\n\n");
			}
			else{
				printf("entered\n\n\n");
			}
			break;

		case 2:

			printf("Input number> ");
			fgets(in_no, sizeof(in_no), stdin);
			no = strtol(in_no, NULL, 10);

			address_data = find(no);
			if(address_data == NULL){
				printf("no data\n\n\n");
			}
			else{
				printf("no = %d\nname = %s\naddress = %s\n\n\n",
					   no, address_data->name, address_data->addr);
			}
			break;
		case 3:

			printf("Input number> ");
			fgets(in_no, sizeof(in_no), stdin);
			no = strtol(in_no, NULL, 10);

			if(delete(no) == -1){
				printf("no data\n\n\n");
			}
			else{
				printf("deleted\n\n\n");
			}
			break;
		case 4:
			show_all();
			break;
		default:
			break;
		}
	}while(menu_no != 9);
	
	delete_all();
	return 0;
}

int enter(int no, const char *name, const char *addr){
	struct tag_address *ptr_now, *ptr_before, *ptr_new;

	if(address == NULL){
		address = malloc(sizeof(struct tag_address));
		ptr_now = address;
		ptr_before = NULL;
		ptr_now->no = no;
		strncpy(ptr_now->name, name, sizeof(ptr_now->name) - 1);
		ptr_now -> name[sizeof(ptr_now->name) - 1] = '\0';
		strncpy(ptr_now -> addr, addr, sizeof(ptr_now->addr) - 1);
		ptr_now -> addr[sizeof(ptr_now->addr) - 1] = '\0';
		address -> next = NULL;

	}

	else{
		ptr_now = address;
		ptr_before = NULL;

		while(ptr_now != NULL){
		
			if(no == ptr_now->no){
				printf("already exist\n");
				return (-1);
			}

			if(no < ptr_now->no){
				break;
			}

			ptr_before = ptr_now;
			ptr_now = ptr_now -> next;
		}

		if(ptr_now == NULL){
			ptr_new = malloc(sizeof(struct tag_address));
			if(ptr_new == NULL){
				printf("Memory Allocation error!\n");
				return (-1);
			}
			ptr_new->no = no;
			strncpy(ptr_new->name, name, sizeof(ptr_new->name) - 1);
			ptr_new -> name[sizeof(ptr_new->name) - 1] = '\0';
			strncpy(ptr_new -> addr, addr, sizeof(ptr_new->addr) - 1);
			ptr_new -> addr[sizeof(ptr_new->addr) - 1] = '\0';
			ptr_now = ptr_new;			
			ptr_new -> next = NULL;
			ptr_before -> next = ptr_new;
		}
		else{
			ptr_new = malloc(sizeof(struct tag_address));
			if(ptr_new == NULL){
				printf("Memory Allocation error!\n");
				return (-1);
			}
			ptr_new->no = no;
			strncpy(ptr_new->name, name, sizeof(ptr_new->name) - 1);
			ptr_new -> name[sizeof(ptr_new->name) - 1] = '\0';
			strncpy(ptr_new -> addr, addr, sizeof(ptr_new->addr) - 1);
			ptr_new -> addr[sizeof(ptr_new->addr) - 1] = '\0';
	
			ptr_new -> next = ptr_now;
	
			if(ptr_before != NULL){
				address = ptr_new;
			}
		}
	}

	return 0;
}

struct tag_address *find(int no){
	struct tag_address *ptr;

	ptr = address;

	while(ptr != NULL){
		if(no == ptr->no){
			return (ptr);
		}

		if(no < ptr->no){
			break;
		}

		ptr = ptr -> next;
	}

	return (NULL);
}

int delete(int no){

	struct tag_address *ptr_now, *ptr_before;

	ptr_now = address;

	if(ptr_now == NULL){
		return -1;
	}

	while(ptr_now != NULL){
		
		if(no == ptr_now -> no){
			break;
		}

		if(no < ptr_now -> no){
			return (-1);
		}

		ptr_before = ptr_now;
		ptr_now = ptr_now -> next;
	}
	if(ptr_now == NULL){
		return -1;
	}

	if(address == ptr_now){
		address = ptr_now -> next;
	}
	else{
		ptr_before -> next = ptr_now -> next;
	}

	free(ptr_now);

	return 0;
}

void show_all(void){

	struct tag_address *ptr_now;

	ptr_now = address;

	while(ptr_now != NULL){

		printf("no = %d\nname = %s\naddress = %s\n\n\n",
			   ptr_now -> no, ptr_now->name, ptr_now->addr);
		ptr_now = ptr_now -> next;
	}
}

void delete_all (void){

	struct tag_address *ptr_now, *ptr_before;

	ptr_now = address;

	while(ptr_now != NULL){
		ptr_before = ptr_now;
		ptr_now = ptr_now -> next;
		free(ptr_before);
	}
}
```
**執行結果:**
```
Input 1[ret] to enter, 2[ret] to search, 3[ret] to delete, 4[ret] to all, 9[ret] to quit.
Input menu number> 1
Input number> 1
Input name> Motoki Taneda
Input address> Tokyo
entered


Input 1[ret] to enter, 2[ret] to search, 3[ret] to delete, 4[ret] to all, 9[ret] to quit.
Input menu number> 1
Input number> 2
Input name> Shuwa System
Input address> Minatoku
entered


Input 1[ret] to enter, 2[ret] to search, 3[ret] to delete, 4[ret] to all, 9[ret] to quit.
Input menu number> 1
Input number> 3
Input name> Toilet Hanako
Input address> Toilet
entered


Input 1[ret] to enter, 2[ret] to search, 3[ret] to delete, 4[ret] to all, 9[ret] to quit.
Input menu number> 1
Input number> 4
Input name> Taro Nippon
Input address> Japan
entered


Input 1[ret] to enter, 2[ret] to search, 3[ret] to delete, 4[ret] to all, 9[ret] to quit.
Input menu number> 3
Input number> 3
deleted


Input 1[ret] to enter, 2[ret] to search, 3[ret] to delete, 4[ret] to all, 9[ret] to quit.
Input menu number> 2
Input number> 1
no = 1
name = Motoki Taneda
address = Tokyo


Input 1[ret] to enter, 2[ret] to search, 3[ret] to delete, 4[ret] to all, 9[ret] to quit.
Input menu number> 4
no = 1
name = Motoki Taneda
address = Tokyo


no = 2
name = Shuwa System
address = Minatoku


no = 4
name = Taro Nippon
address = Japan


Input 1[ret] to enter, 2[ret] to search, 3[ret] to delete, 4[ret] to all, 9[ret] to quit.
Input menu number> 9
```
(筆者注:因為原書作者的**enter()**函數是錯的，執行會segementation fault，所以上面的source code筆者把**enter()**函數重新改寫了。原來的**enter()**可以看下面，可以想想它是哪裡錯了)
```cpp
int enter(int no, const char *name, const char *addr){
	struct tag_address *ptr_now, *ptr_before, *ptr_new;

	ptr_now = address;
	ptr_before = NULL;

	while(ptr_now != NULL){
		
		if(no == ptr_now->no){
			printf("already exist\n");
			return (-1);
		}

		if(no < ptr_now->no){
			break;
		}

		ptr_before = ptr_now;
		ptr_now = ptr_now -> next;
	}

	ptr_new = malloc(sizeof(struct tag_address));
	if(ptr_new == NULL){
		printf("Memory Allocation error!\n");
		return (-1);
	}
	ptr_new->no = no;
	strncpy(ptr_new->name, name, sizeof(ptr_new->name) - 1);
	ptr_new -> name[sizeof(ptr_new->name) - 1] = '\0';
	strncpy(ptr_new -> addr, addr, sizeof(ptr_new->addr) - 1);
	ptr_new -> addr[sizeof(ptr_new->addr) - 1] = '\0';

	if(address == NULL){
		ptr_before -> next = ptr_new;
	}

	ptr_new -> next = ptr_now;

	if(ptr_before != NULL){
		address = ptr_new;
	}

	return 0;
}
```



如果沒辦法理解的話，就在代入指標的地方加上**printf("ptr_now = %p,  ptr_before = %p", ptr_now, pre_before);**這一句，看看實際上的數值畫圖看看。

慢慢花時間追蹤程式一定可以明白它的動作。

### 注意memory leak

像前一節這樣複雜的程式，就是一個容易發生memory leak的例子。所謂memory leak，就是使用完畢的記憶體沒有立刻解放。如果linked list把資料消除卻沒有執行**free()**的話，那麼將會永遠無法參照那一塊領域，經過了許多次的登錄跟消除後，這種系統無法使用的記憶體就越來越多。如果就這麼擺著不管，可能系統的記憶體就會不足導致不安定。

只做簡單測試的話是很難發現memory leak的，不少是在長時間執行才第一次發現。而且就算執行慢了些，也常常不知道具體上是什麼症狀，所以我們希望可以在寫成是的階段就發現它。

如果把剛剛的address.c程式的**delete()**最後的**free()**給忘掉，就會造成。在寫程式時，如果要進行確保的處理，請記住哪裡要解放。