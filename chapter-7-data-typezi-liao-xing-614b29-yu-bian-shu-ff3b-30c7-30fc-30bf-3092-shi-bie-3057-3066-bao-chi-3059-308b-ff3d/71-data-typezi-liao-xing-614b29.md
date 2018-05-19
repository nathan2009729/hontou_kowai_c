我們希望變數保持的值有「數值」「文字」，還有許許多多的情報。這些情報要如何區別呢？
### data type是決定記憶體要占多大
_ _ _
其實，在電腦的記憶體中，不管是數值、文字、影像、聲音、程式本身等等都是數值的羅列。那麼，數字的1是怎麼被儲存的呢？最近的電腦，memory有32bit(4bytes)還有64bit(8bytes)，就像下圖這樣放著:

![Imgur](http://i.imgur.com/7tiNt5k.png)

所謂bit，是0和1區別的最小單位。也就是說，是2進位的一位。有32bit(32位)的話，就可以有2的32次方種組合，可以表示0~4294967295共4294967296個數字。32bit CPU(Intel Pentium等)是以32bit為一單位，64bit CPU(Intel Core2Duo等)是以64bit為一單位。比如說，32bit CPU如果需要放64bit(8bytes)資料的話，就需要像下圖這樣重複利用兩個32bit。

![Imgur](http://i.imgur.com/y6FtAJu.png)

首先，電腦把所有的情報都作為數字認識; 用越多的memory就可以識別越多的情報。32bit只能處理到4294967295的整數，64bit的話就可以處理到 18446744073709600000的整數。整數的話，幾乎在所有的場合都夠用了，但是文字的情報呢？

為了簡單些，先考慮使用ASCII code的文字吧。ASCII code是把英數字等半型符號定義成256種組合。為了表現256種組合，需要8bit(1 byte)，所以ASCII code一個文字用8bit來表示。比如說，A的話就是65這個數字，數字的0就是用48表示。那像第五章提過的「hello, world」這個文字資訊含空白總共12個字，需要96bit(12bytes)來儲存。

看來64bit memory不夠用呢。

再來，畫面是顏色情報來識別所以1畫素就需要好幾個bit，聲音也是1秒就好幾個bit。memory是電腦的有限資源，需要適切的對待。

所以C語言用為了收納而為必要的memory容量所取的名字--data type，來宣言變數。想收納一個字的話，就用8bit memory的data type，想收納0-4294967295的整數的話就要用32bit memory的data type來宣告變數。

### data type的「儲存」與「處理」是重要的
_ _ _

data type的概念，除了所佔的記憶體大小外還有其他的重點，就是如何利用。32bit(32位)的話，因為有4294967296種組合，可以表示0~4294967295共4294967296個數字。

但是在這個範圍中並不含負數。當然，對計算很拿手的電腦卻不能計算負數是一點用都沒有的，所以我們在這個範圍中取了一些組合當作負數。

平常我們使用的Windows還有Mac, Linux的電腦，都是使用2的補數來表示負數。如果利用這個方法，32bit可以表示-2147483648～2147483647這些數字。

但是當宣告一個32bit的變數時，compiler會不知道這是想表示0~4294967295呢？還是-2147483648～2147483647呢？

舉個例子，2147483648用2進位表示成10000000000000000; 但是-2147483648用二的補數也表示成10000000000000000，明明是不同的數字，memory內部的表現卻是一樣的。所以，data type除了記憶體的大小以外還要規定如何處理。