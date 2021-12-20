[TOC]
<font face = "Consolas">

### win控制台字体
通过`chcp 437`更换为英语,右键标题栏,属性即可更换字体
> 无法输入中文
`chcp 936`换回中文

### vmware共享文件夹
/虚拟机/设置中设置后,在`/mnt/hgfs/`中

### win防火墙
管理员shell中`off/on`
`NetSh Advfirewall set allprofiles state off`

### line_profiler
逐行解析代码函数用时,需在函数头用`@profile`修饰,后再cmd调用
`kernprof -l -v D:\vscodePython\draft\test1.py`

### git无法连接:修改为ssh连接
```
git remote -v  //显示当前的地址
git remote set-url origin git@github.com:youname/housename.git
// 换为git@github.com
git push -u origin master
```


### 汇编
* c：`D:\vscodeC\cfiles\draft>gcc -S 1.c`

### gdb
`gdb: 在文件夹下 gdb filename.exe -q(quiet免责声明)`