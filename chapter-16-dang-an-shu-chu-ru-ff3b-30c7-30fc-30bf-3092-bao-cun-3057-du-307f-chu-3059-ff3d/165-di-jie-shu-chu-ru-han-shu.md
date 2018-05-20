有C語言的教科書會記載檔案函數有分成高階跟低階的輸出入函數，這是怎麼一回事呢? 實際上，C語言的標準規格只有之前說明過的f系檔案函數。但是，C語言的發祥環境UNIX的system call(呼叫OS機能的API)，有考慮到C語言的準標準。

而C語言library提供的f系檔案函數就被稱為「高階輸出入函數」，OS機能的system call被稱為「低階輸出入函數」。

|          | 高階輸出入函數 |   低階輸出入函數  |      Windows API     |
|:--------:|:--------------:|:-----------------:|:--------------------:|
|          |    C語言標準   | UNIX的System call | Windows的System call |
| 打開檔案 |     fopen()    |       open()      |     CreateFile()     |
| 寫入檔案 |    fwrite()    |      write()      |      WriteFile()     |
| 讀出檔案 |     fread()    |       read()      |      ReadFile()      |

Windows跟UNIX的System call一樣，有**CreateFile()**等等專用的API(Application Programming Interface)可以使用，也可以使用**open()**。這是因為Windows的前身MS-DOS是參考UNIX做的，所以也可以用以前的低階輸出入函數。

在MS-DOS的時代，高階跟低階的輸出入函數只差在buffer的有無。像是**fgets()**跟**fprintf()**是一行一行處理，但是低階輸出入函數是一個byte一個byte處理。如果一行一行處理的話，就需要用到buffer，每儲存一行就交給程式去處理。buffer可以在輸出入處理的時候，為了調整吸收硬碟跟CPU處理速度的差距暫時用到記憶體。

這是因為跟CPU還有記憶體比起來，硬碟是非常慢的。如果不用buffer的話，程式就要等到硬碟寫入完成才繼續下個處理，但是，如果使用buffer，可以在寫入記憶體的時候就當作寫入到硬碟了，可以比較快的進行下個處理。只要等到CPU閒的時候把記憶體的東西寫到硬碟內就可以了。而這動作稱作**flush**。

高階輸出入函數，就是因為有buffer，所以才處理的比較快。

但是UNIX跟Windows有其他更重要的地方，就是可以作到OS相關的控制。像**fopen()**，只能新建跟開啟檔案而已。但是**open()**可以指定要做怎樣權限的檔案，要用怎樣的方式寫進硬碟，這些細微的事都可以指定。

像Windows的**CreateFile()**也可以作到細微的安全條件指定。現在的話不需要管高階跟低階輸出入函數，只要把library函數跟system call跟API這些歷史的經緯記起來，以後念新的參考書可能有用。