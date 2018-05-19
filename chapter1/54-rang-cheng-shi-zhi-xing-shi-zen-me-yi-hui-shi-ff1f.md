剛剛已經照順序看過了preprocess, compile, assemble, link等處理了。那麼，經過這些工程生成的執行檔在執行的期間會發生什麼事呢？

這次的例子並沒有特別指定輸出的執行檔的檔名，所以會生成a.exe或a.out為名的執行檔。要啟動輸出的執行檔，首先會執行OS的程式==program loader==。program loader會讀入執行檔裡的記憶體跟必要的共有library，才能執行啟動的檔案。

所謂的共有library(shared library)，是指某個library，它含有很多程式所需要的處理。Windows的共有library就是.dll檔，Mac OS X則是.dylib檔，linux則是.so檔。

執行檔因為link的option，可以把libc還有其他library做static link，也可以選擇dynamic link。把library做static link的話，可以把指定的library跟執行檔連結。這樣的話，就算執行時沒有那個library檔案，也可以執行程式; 但大小會變大。另外，因為是跟執行檔連結起來，所以如果library出現bug的話，就需要再重新連結一次。

如果是用dynamic link的話，執行時會透過program loader來參照(reference)記憶體上的shared library。所以執行檔會比static link所生出的來的小，而且library出現bug的話就只需要換掉library，不用再重新link，在系統上的應用比較方便。

現在為了對應所有的OS，沒有特別理由的話幾乎都是用dynamic link。就算是用dynamic link，也需要知道執行檔裡用了哪些library，link這個處理還是必要的。

在<b>5.3 linker: compile後的處理</b>中用的就是dynamic link，在<b>看看startup routine吧</b>用的則是static link。可以實際用指令`ldd`查詢這兩個不同的a.out。如果是用dynamic link的話，那麼輸入指令`ldd a.out`會出現

	linux-vdso.so.1 =>  (0x00007ffc553da000)  
    
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f207f657000)  
    
	/lib64/ld-linux-x86-64.so.2 (0x000056404263c000)  
    
這些訊息(筆者的電腦)。如果是用`ld -nostdlib nop_nocrt_linux.o`所static link的程式，則`ldd a.out`會出現`不是動態可執行檔案`。如果是windows所compile的程式，一定都會需要ntdll.dll跟kernel32.dll這兩個shared library，所以在windows是不能做static link的。