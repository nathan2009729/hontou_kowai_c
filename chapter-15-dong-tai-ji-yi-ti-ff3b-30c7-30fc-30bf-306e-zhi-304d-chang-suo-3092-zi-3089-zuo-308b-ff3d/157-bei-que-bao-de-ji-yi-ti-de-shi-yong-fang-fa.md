用**malloc()**或**calloc()**確保記憶體後，會返回**void**指標所指的領域。這裡程式員要怎麼利用都可以。

*int*
```cpp
int *num;
num = malloc(sizeof(int));
//*num can be used as a int type variable until free()

int *num_array;
num_array = calloc(元素數, sizeof(int));
//same as malloc(元素數*sizeof(int));
//*num_array can be used as a int type array until free()
```

*double*
```cpp
double *num;
num = malloc(sizeof(double));
//*num can be used as a double type variable until free()

double *num_array;
num_array = calloc(元素數, sizeof(double));
//same as malloc(元素數*sizeof(double));
//*num_array can be used as a double type array until free()
```

*char*
```cpp
char *character;
character = malloc(sizeof(char));
//*character can be used as a char type variable until free()

char *one_string;
one_string = calloc(元素數, sizeof(char));
//same as malloc(元素數);
//*one_string can be used as a string until free()
```

*結構體*
```cpp
#define MAX_CHARACTER (256)
#define MAX_DATA (1024)

struct tag_address{
    int no;
    char last_name[MAX_CHARACTER];
    char first_name[MAX_CHARACTER];
    char last_name_furigana[MAX_CHARACTER];
    char first_name_furigana[MAX_CHARACTER];
    int post_code;
    char address_1[MAX_CHARACTER];
    char address_2[MAX_CHARACTER];
    char phone_no[MAX_CHARACTER];
    char fax_no[MAX_CHARACTER];
    char cellular_no[MAX_CHARACTER];
    char remarks[MAX_CHARACTER];
};

struct tag_address *address;
address = malloc(sizeof(struct tag_address));
/*address can be used as a tag_address type struct variable until free(). 因為address是指標變數，所以存取成員變數不是用.運算元，而是用->運算元*/

struct tag_address *address_array;
//same as address = malloc(元素數*sizeof(struct tag_address));
address = calloc(元素數, sizeof(struct tag_address));
//*address_array can be used as a tag_address type array until free()
```
就像這樣把**malloc()**的結果代入**int**或**double**的指標變數，來儲存**int**或**double**的值，char的話就是字串。也可以代入自己定義的結構體的指標變數，當作結構體陣列使用。

如果是像下面這樣直接把位址給複製，並不會複製到被存取的位址的值。
```cpp
int *num_1;
int *num_2;

num_1 = malloc(sizeof(int));
*num_1 = 1000;

num_2 = num_1;
```
上面這樣寫的意思是: 我們用num_1指向了**malloc();**給的記憶體領域，並且讓num_2指向跟num_1一樣的地方，所以當我們寫***num_1 = 2000;**的話，*num_2也會是2000。

如果是想另外複製一份值的話，應該像下面這樣寫:
```cpp
int *num_1;
int *num_2;

num_1 = malloc(sizeof(int));
*num_1 = 1000;

num_2 = malloc(sizeof(int));
*num_2 = *num_1;
```
這樣的意思是指標變數num_1跟num_2各指向了不同的地方，所以如果在這之後寫了***num_1 = 2000;**對*num_2也不會有影響了。

雖說是理所當然，但要知道**malloc()**跟**calloc()**所確保的領域以及指標變數的代入、變數名的處理，都需要考慮並利用到指標變數的特徵。