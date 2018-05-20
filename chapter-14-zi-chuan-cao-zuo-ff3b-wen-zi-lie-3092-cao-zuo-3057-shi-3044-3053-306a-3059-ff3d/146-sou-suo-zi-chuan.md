在可以做字串的複製與比較後，也會想做搜索吧。

### 字串搜索函數

跟字串搜索有關的函數有很多。

* memchr: 掃描記憶體以搜索文字
* memcmp: 比較記憶體領域
* memcpy: 複製記憶體領域 (複製源頭跟目標的記憶體領域不可重疊)
* memmove: 複製記憶體領域 (複製源頭跟目標的記憶體領域可重疊)
* memset: 在一定byte的記憶體內丟入某個值
* strchr: 特定字串中文字的位置(最初開始)
* strcat: 連結兩個字串
* strcmp: 比較兩個字串
* strcpy: 複製字串
* strcspn: 從字串搜索文字的set
* strerror: 返回說明錯誤編號的字串
* strcoll: 比較兩個用locale的字串
* strlen: 計算字串長度
* strncat: 連結兩個字串(有長度限制)
* strncmp: 比較兩個字串(有長度限制)
* strncpy: 複製字串(有長度限制)
* strpbrk: 從字串檢出文字set中的文字
* strtchr: 特定字串中文字的位置(最後開始)
* strspn: 從字串搜索文字的set
* strstr: 表示部份字串的位置
* strtok: 從字串取出token
* strxfrm: 字串的變換

本書只介紹其中的一部分，就算不介紹的也可以在compiler的manual找到，習慣的話就查manual吧。compiler可能也有自己擴張的版本，調查看看的話搞不好會知道比較便利的東西。比如說，linux的OS，有可以區分大小寫的**strcmp()**，但也有不區分大小寫的**strcasecmp()**; Windows的話也有同樣功能的**_stricmp()**。這些函數的prototype通通都在string.h中，輸入範例程式時不要忘了include。

### 搜索文字(strchr)

strchr()是從字串找出特定字母場所的函數。它的宣告如下:

```cpp
char *strchr(const char *字串，int 想檢索的字母)
```

返回值是「想找的字母」第一次出現的位置的指標，找不到的話返回**NULL**。這個函數在份空白區隔的字串，比如姓名等等，可以找出空白的位置，只改變特定的部份，很方便。來介紹實際的例子吧。

**source code**
*strchr_test.c*
```cpp
#include <stdio.h>
#include <string.h>

int main(){
	
	char full_name[256] = "NIPPON Taro";
	char first_name[256] = "Hanako";
	char *ptr;

	//find the bigin position of full_name, save it at ptr
	ptr = strchr(full_name, ' ');
	ptr++;
	strcpy(ptr, first_name);//change Taro into Hanako

	//more safe
	/*strncpy(ptr, first_name, 
	  sizeof(full_name) - 1 - strlen(first_name) - 1);
	 full_name[sizeof(full_name) - 1] = '\0';*/

	printf("Full name = %s\n", full_name);

	return 0;
}
```
**執行結果:**
```
Full name = NIPPON Hanako
```
可以看到NIPPON Taro這姓名變成了NIPPON Hanako。這個範例程式除了**strchr()**以外，還用了**strcpy()**，如果一開始是宣告**char full_name[] = "NIPPON Taro";**的話，就會因為full_name空間不足而異常動作，也可能異常中止。在這種場合下，不要用**strcpy()**，而是用**strncpy()**可以防止異常動作。

### 搜索複數字母 (strpbrk)

**strpbrk()**也是在字串搜索字母場所的函數，但是它可以搜索複數的字母，所以也可以用在模糊的搜索。它的宣告如下:

```cpp
char *strchr(const char *字串，const char *想搜索的字母);
```
返回值是第一個「想搜索的字母」出現的位置的指標，想搜索的字母以字串的形式當作參數。比如說，想要找到空白或逗號，就以字串" ,"當參數。這裡就以strchr_test.c這個範例程式改造一下來實驗吧。

**source code**
*strpbrk.c*
```cpp
#include <stdio.h>
#include <string.h>

int main(){
	
	char full_name[256] = "NIPPON,Taro";
	char first_name[256] = "Hanako";
	char *ptr;

	//find the bigin position of name, save it at ptr
	ptr = strpbrk(full_name, " ,");
	ptr++;
	strcpy(ptr, first_name);//change Taro into Hanako

	//more safe
	/*strncpy(ptr, first_name, 
	  sizeof(full_name) - 1 - strlen(first_name) - 1);
	 full_name[sizeof(full_name) - 1] = '\0';*/

	printf("Full name = %s\n", full_name);

	return 0;
}
```
**執行結果:**
```
Full name = NIPPON,Hanako
```
可以看到NIPPON,Taro變成了NIPPON,Hanako了。因為空白也是檢索的目標，所以寫成NIPPON Taro也是可以動作的。

在像是計算機程式，會在輸入數字以外的場合出現error，或是輸入了menu沒有的文字會error的程式也會用到。

### 字串的搜索(strstr)

**strstr()**函數可以在字串中尋找字串。目前為止的**strchr()**或是**strpbrk()**都只能尋找一個字母，但**strstr()**可以指定字串，所以可以找到英語單詞並置換。它的宣告如下:

```cpp
char *strstr(const char *字串，const char *想搜索的字串);
```

返回值是「想搜索的字串」第一次出現的位置的指標，找不到的話回傳**NULL**。下面的範例程式，是求出都道府縣的縣的數量。

**source code**
*strstr_test.c*
```cpp
#include <stdio.h>
#include <string.h>

int main(){
	char *database[47] = {
		"Hokkaido", "Aomori Prefecture", "Iwate Prefecture",
		"Miyagi Prefecture", "Akita Prefecture", 
		"Yamagata Prefecture", "Fukushima Prefecture",
		"Ibaraki Prefecture", "Tochigi Prefecture",
		"Gumma Prefecture","Saitama Prefecture", "Chiba Prefecture",
		"Tokyo", "Kanagawa Prefecture", "Niigata Prefecture",
		"Toyama Prefecture", "Ishikawa Prefecture",
		"Fukui Prefecture", "Yamanashi Prefecture",
		"Nagano Prefecture", "Gifu Prefecture", "Shizuoka Prefecture",
		"Aichi Prefecture", "Mie Prefecture", "Shiga Prefecture",
		"Kyoto", "Osaka", "Hyogo Prefecture", "Nara Prefecture",
		"Wakayama Prefecture", "Tottori Prefecture",
		"Shimane Prefecture", "Okayama Prefecture",
		"Hiroshima Prefecture", "Yamaguchi Prefecture",
		"Tokushima Prefecture", "Kagawa Prefecture",
		"Ehime Prefecture", "Kochi Prefecture", "Fukuoka Prefecture",
		"Saga Prefecture", "Nagasaki Prefecture", 
		"Kumamoto Prefecture", "Oita Prefecture",
		"Miyazaki Prefecture", "Kagoshima Prefecture",
		"Okinawa Prefecture",
	};

	int i,cnt;

	cnt = 0;
	for(i = 0; i < 47; i++){//>
		if(strstr(database[i],"Prefecture") != NULL){
			printf("%s\n", database[i]);
			cnt++;
		}
	}
	printf("%d\n", cnt);

	return 0;
}
```
**執行結果:**
```
Aomori Prefecture
Iwate Prefecture
Miyagi Prefecture
Akita Prefecture
Yamagata Prefecture
Fukushima Prefecture
Ibaraki Prefecture
Tochigi Prefecture
Gumma Prefecture
Saitama Prefecture
Chiba Prefecture
Kanagawa Prefecture
Niigata Prefecture
Toyama Prefecture
Ishikawa Prefecture
Fukui Prefecture
Yamanashi Prefecture
Nagano Prefecture
Gifu Prefecture
Shizuoka Prefecture
Aichi Prefecture
Mie Prefecture
Shiga Prefecture
Hyogo Prefecture
Nara Prefecture
Wakayama Prefecture
Tottori Prefecture
Shimane Prefecture
Okayama Prefecture
Hiroshima Prefecture
Yamaguchi Prefecture
Tokushima Prefecture
Kagawa Prefecture
Ehime Prefecture
Kochi Prefecture
Fukuoka Prefecture
Saga Prefecture
Nagasaki Prefecture
Kumamoto Prefecture
Oita Prefecture
Miyazaki Prefecture
Kagoshima Prefecture
Okinawa Prefecture
43
```
我們只是單純的搜索「Prefecture」這個字串而已。和2012的都1、道1、府2、縣43是符合的。