<font face = "Consolas">
<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [win控制台字体](#win控制台字体)
- [vmware共享文件夹](#vmware共享文件夹)
- [win防火墙](#win防火墙)
- [line_profiler](#line_profiler)
- [git无法连接:修改为ssh连接](#git无法连接修改为ssh连接)
- [汇编](#汇编)
- [gdb](#gdb)
- [MLDL conda](#mldl-conda)
- [Colab](#colab)
- [pip指定版本](#pip指定版本)
- [vscode插件`Markdown Preview Enhanced`的TOC](#vscode插件markdown-preview-enhanced的toc)

<!-- /code_chunk_output -->




### win控制台字体
通过`chcp 437`更换为英语,右键标题栏,属性即可更换字体
> 无法输入中文
`chcp 936`换回中文
持久修改: 注册表`计算机/HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Command Processor`添加`字符串值`名称`autorun`数值`chcp 437`
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

### MLDL conda
`activate school_MLDL && d: && cd D:\projects\boatCNN_vgg13 && python train.py`

### Colab
```
!unzip "/content/drive/MyDrive/boatCNN_GoogleNet_bak.zip" -d "boatCNN_GoogleNet_bak"
!cd /content/boatCNN_GoogleNet_bak && python dataset_processor.py
!cd /content/boatCNN_GoogleNet_bak && python train.py
!cp /content/boatCNN_GoogleNet_bak/weight/training_statistic.json /content/drive/MyDrive
!cp /content/boatCNN_GoogleNet_bak/weight/GoogLeNet_best.pth /content/drive/MyDrive
```

### pip指定版本
`pip install scikit-image==0.14.0`

### vscode插件`Markdown Preview Enhanced`的TOC
`cmd-shift-p` `Markdown Preview Enhanced: Create Toc`
将生成的`<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->
`放到行首