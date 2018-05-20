一但寫了**define**的命令，就對宣告那一行之後的程式碼就一直都有效。如果因為什麼理由而想把**define**無效化的話，可以用**undef**。使用方法很簡單如下:

```cpp
#undef 想無效化的define
```
範例:
```cpp
#undef INTEGER_NUM_1
```
可以對剛剛的define.c實驗看看，在**main()**之前寫上這一行，**int a = INTEGER_NUM_1;**這一行就會出現錯誤。