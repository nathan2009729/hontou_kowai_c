在這章，我們學習了C語言特有的preprocessor命令。

雖然是其他大部分的語言都沒有的機能所以不好懂，但是在gcc -E的命令下看到是怎樣置換的，應該會明白到這意外的簡單。

stdio.h等等的head file中有許多被**define**定義的常數，而為了讓UNIX系的OS可以像collection一樣利用list或quene的head file，其機能也幾乎都是用macro來實現的(為了使用時可以不受dat type影響)。

就算是說學習了preprocessor命令，能做的事也不會戲劇性的增加，不過C語言到處都會用到，所以記起來吧。