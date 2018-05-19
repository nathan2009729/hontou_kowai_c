source code經過preprocessor的處理、再被compile過後會變成assembly language的source，再經由assembler變成machine language的檔案。

GNU Compiler Collection(gcc)有compile跟assemble的指令，也有`as`這個assembler的指令，我們在這裡利用gcc的指令把nop.s做assemble吧。

如果只打`gcc nop.c`，不加其他option的話就會從頭開始一直進行到最後的link生成執行檔，加上`-c`這個option則會生成machine language。
打以下指令:
`gcc -c nop.s`
就會在當前目錄生出nop.o這個檔案。那麼，這個nop.o真的能執行嗎？讓我們在linux下作個實驗:
首先，把nop.o賦予執行權限，指令如下:
`chmod 755 nop.o`
再讓nop.o執行，指令如下:
`./nop.o`
會發現螢幕出現`bash: ./nop.o: cannot execute binary file`這串字，代表無法執行。

要說為什麼的話，nop.o雖然的確是machine language，但是作為執行檔而言，它的為了執行的情報、為了讓C語言所寫的程式如何動所需的情報都不足。link這個動作就是讓nop.o這個檔案變成執行檔所需的作業。

這個不能執行的machine language檔案(nop.o)被稱為==object file==。在進行link之前，先檢查它的大小吧。輸入指令`ls -l nop.o`即可知道nop.o有多大，筆者電腦顯示是1232 bytes。

接下來就是對nop.o進行link。不過筆者不知該打什指令(書上指令不管用，筆者是ubuntu 16.04)。總之，生出來的執行檔a.out大小大幅增加。

這些多出來的大小就是link所補足的為了執行的情報還有為了讓C語言所寫的程式如何動所需的情報。"為了讓C語言所寫的程式如何動所需的情報"我們稱之為==startup routine==。含有這個startup routine的檔案我們稱之為==C runtime==。

要讓程式可以執行，除了C runtime以外還要有一個叫==libc==的library，它可以讓C語言所寫的程式動起來。

C runtime是讓語言之所以成立的最低限program，比如說，為了呼叫main()所需的處理，在cpu的乘除無法對應時由C runtime解決。libc則是放著一些C語言會用到的函式，像是在螢幕上印出字來之類的程式。

把souce code跟上面說的東西結合的動作，就叫做link。C語言所寫的程式一定要經過這個步驟才可以變成可執行的檔案。

順帶一提，現在的"什都不做的程式"的source code只需要一個檔案即可，但是到大規模程式的話一般都會分成好幾個檔案，為的是好懂。在這情況下，從複數的source code個別生出object code，最後再用C runtime跟libc以及其他library來生成執行檔。

###看看startup routine吧
_ _ _

因為gcc是open source，想要的話是可以讀的到startup routine的source code的; 但是，那source code的複雜程度不是C語言的初學者看得懂的。所以在這裡，我們把剛剛的"什麼都不做的程式"(nop.c)試著加上讓它可以執行的startup routine。

gcc的話，startup routine的名字一定是_start(windows的話是__main)。現在來看看linux上的寫法。以下是nop.c再加上startup routine的source code(nop_nocrt_linux.c)。
```cpp
int main(){
	return (0);
}
void _start(int args){
	main(0,0);

	asm("movl $0,%ebx\n"
		"movl $1,%eax\n"
		"int $0x80");
}
```
想執行的話就用以下指令:
`gcc -S nop_nocrt_linux.c`
`gcc -c nop_nocrt_linux.c`
`ld nostdlib nop_nocrt_linux.o`
`./a.out`

像這樣，如果C語言卻不用C runtime和libc的話，依據機器的不同會有不同的程式(以上是linux的寫法，windows跟Mac的寫法都不同)。反過來說，C語言的移植性可說是由C runtime和libc擔保的。