今天域到了一個問題，就是在某個文件，常常會出現一對&amp&amp，裡面夾一些數學公式的情形，也就是 &amp&amp數學公式$$`。現在希望把它改成`$ 數學公式 $`的形式，所以寫了一個程式如下表

```c
#include <stdio.h>
#include <stdlib.h>
int main(){

    FILE *in = fopen("exercise.md","r");
    FILE *out = fopen("back.txt","w");
    
    int ch = 0;
    int counter = 0;
    if(!in){
        printf("read fail");
        exit(1);
    }
    if(!out){
        printf("create fail");
        exit(1);
    }
    while((ch = getc(in)) != EOF){
        if(ch == '$'){
            counter++;
            if(counter == 1){
                fputc('`' ,out);
            }
            else if(counter == 2){
                fputc(ch, out);
                fputc(' ',out);
            }
            else if(counter == 3){
                fputc(' ',out);
                fputc(ch, out);
            }
            else if(counter == 4){
                fputc('`' ,out);
                counter = 0;
            }
        }
        else{
            fputc(ch, out);
        }
    }
    /*for(ch = 33; ch <=100; ch++){
        putc(ch, out);
    }*/
    fclose(in); fclose(out);
    printf("ok\n");

}```
