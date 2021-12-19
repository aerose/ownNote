[TOC]
<font face = "Consolas">

> 分号隔开命令`command1; command2; command3...`
鼠标选中为复制,鼠标中键粘贴
tab自动补全
`less filename`阅读文本,q退出
`df`/`free` 磁盘/内存剩余空间
`pwd`输出当前绝对路径
`-`在shell中作为标准输入的代称
`.`是`source`的同义词,读取指定的文件

> 任何东西都是文件
双引号中 $ \ ' 也会被解释 ; 单引号禁止所有展开 ; \转义阻止展开
一般备份文件加扩展名.bak

# bash快捷键
操作 |按键 | 操作 | 按键
-|-|-|-
撤销(ctl+shift+-) | Ctrl + _ | 自动补齐 | TAB
清屏 | Ctrl + l
光标到最前(ahead) | Ctrl + a |光标到最后(end)| Ctrl + e 
光标按词左移 | Alt + b |光标按词右移 | Alt + f
向前剪切 | Ctrl + u | 向后剪切 | Ctrl + k
向前剪切一词 | Ctrl + w | 向后剪切一词 | Alt + d
粘贴剪切板| Ctrl + y
执行并继续选择 | Ctrl + o| 交换前一词 | Alt + t
向后改词大写 |Alt + u | 向后改词小写| Alt + l
终止 | Ctrl + c | 挂起 |Ctrl + z
退出终端(空命令行)|Ctrl + d
命令历史 | 上下箭头 | 参数历史 | Alt + .
# `ls`
-后可跟多个选项如`-lh`
选项	| 长选项 |    描述
-|:-:|-
-a	| --all	| 列出所有文件,甚至包括文件名以圆点开头的默认会被隐藏的隐藏文件.
-d |	--directory | 通常,如果指定了目录名,ls 命令会列出这个目录中的内容,而不是目录本身. 把这个选项与 -l 选项结合使用,可以看到所指定目录的详细信息,而不是目录中的内容.
-F	| --classify | 这个选项会在每个所列出的名字后面加上一个指示符.例如,如果名字是 目录名,则会加上一个'/'字符.
-h|	--human-readable |	当以长格式列出时,以人们可读的格式,而不是以字节数来显示文件的大小.
-l	||	以长格式显示结果.
-r|	--reverse	| 以相反的顺序来显示结果.通常,ls 命令的输出结果按照字母升序排列.
-S	|| 命令输出结果按照文件大小来排序.
-t	||	按照修改时间来排序.
-i  || 展示文件索引节点(i-node)(用于确定硬链接)
## ls长格式字段
`-rw-r--r-- 1 root root   47584 2007-04-03 11:05 logo-Edubuntu.png`
-rw-r--r-- | 1  | root  | root   |  47584  | 2007-04-03 11:05  | logo-Edubuntu.png
:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:
文件访问权限 | 硬链接数目 | 用户名 | 用户组名 | 字节大小 | 上次修改时间 | 文件名

# 文件访问权限
属性	|文件类型
-|-
-	|一个普通文件
d	|一个目录
l	|一个符号链接.注意对于符号链接文件,剩余的文件属性总是"rwxrwxrwx",而且都是虚拟值.真正的文件属性是指符号链接所指向的文件的属性.
c	|一个字符设备文件.这种文件类型是指按照字节流来处理数据的设备. 比如说终端机或者调制解调器
b	|一个块设备文件.这种文件类型是指按照数据块来处理数据的设备,例如一个硬盘或者 CD-ROM 盘.

Owner | Group | World
-|-|-
rwx|rwx|rwx

属性	|文件	|目录
-|-|-
r	|允许打开并读取文件内容.	|允许列出目录中的内容,前提是目录必须设置了可执行属性（x）.
w	|允许写入文件内容或截断文件.但是不允许对文件进行重命名或删除,重命名或删除是由目录的属性决定的.	|允许在目录下新建、删除或重命名文件,前提是目录必须设置了可执行属性（x）.
x	|允许将文件作为程序来执行,使用脚本语言编写的程序必须设置为可读才能被执行.|	允许进入目录,例如：cd directory .
## `chmod`更改文件模式
* 八进制表示法
三个八进制数字设置三者权限 每个八进制数字翻译为三个二进制数字按rwx顺序1表示有权限`[me@linuxbox ~]$ chmod 600 foo.txt`
* 符号表示法
    x | y
    -|-
    u	|"user"的简写,意思是文件或目录的所有者。
    g	|用户组。
    o	|"others"的简写,意思是其他所有的人。
    a	|"all"的简写,是"u", "g"和“o”三者的联合。(默认)
    s   | 文件setuid权限/目录setgid权限
    t   |目录sticky权限
    **例子**|
    u+x	|为文件所有者添加可执行权限。
    u-x	|删除文件所有者的可执行权限。
    +x	|为文件所有者,用户组,和其他所有人添加可执行权限。 等价于 a+x。
    o-rw	|除了文件所有者和用户组,删除其他人的读权限和写权限。
    go=rw	|g,o权限变成rw-
    u+x,go=rw	|给文件拥有者执行权限并给组和其他人读和执行的权限。多种设定可以用逗号分开。
## `umask` 设置默认权限
> 只在当前shell中生效,当前shell结束后必须重新设置
* `umask` 不带参数查看当前掩码值
* `umask xxxx(4位八进制)`设置掩码值
    > 第一个数字为其他权限设置,一般0就可
    默认0000时权限为--- rw- rw- rw-
    后三个数字为屏蔽模式,哪个位为1就禁止该权限
    如000 000 000 010时,权限为--- rw- rw- r--

其他权限设置:
权限 | 位 | 意义
-|-|-
setuid | 0100 | 将可执行文件的有效用户id设置成程序所有者的id,(如root所有的程序设置了setuid后这个程序就有了root权限)
setgid | 0010 |  把有效用户组id从真正的用户组id更改为文件所有者的组id
sticky | 0001 | 将可执行文件标记位"不可交换的".linux中文件会忽略,目录设置后会阻止除了目录所有者/文件所有者/root的其他人删除或重命名文件
如何设置见上chmod
# 通配符
特殊字符来快速指定一组文件名
通配符  |	意义
:-:|:-
*	|匹配任意多个字符（包括零个或一个）
?	|匹配任意一个字符（不包括零个）
[characters]	|匹配任意一个属于字符集(characters)中的字符
[!characters]	|匹配任意一个不是字符集中的字符
\[[:class:]]	|匹配任意一个属于指定**字符类**中的字符
**字符类**|	意义
[:alnum:]	|匹配任意一个字母或数字
[:alpha:]	|匹配任意一个字母
[:digit:]	|匹配任意一个数字
[:lower:]	|匹配任意一个小写字母
[:upper:]	|匹配任意一个大写字母
**例子**	|
*	|所有文件
g*	|文件名以“g”开头的文件
b*.txt	|以"b"开头,中间有零个或任意多个字符,并以".txt"结尾的文件
Data???	|以“Data”开头,其后紧接着3个字符的文件
[abc]*	|文件名以"a","b",或"c"开头的文件
BACKUP.[0-9][0-9][0-9]|	以"BACKUP."开头,并紧接着3个数字的文件
[[:upper:] ]*	|以大写字母开头的文件
[![:digit:]]*	|不以数字开头的文件
*[[:lower:]123]	|文件名以小写字母结尾,或以 “1”,“2”,或 “3” 结尾的文件

# 文件操作
省略号表示那个参数可以跟多个
单个圆点 . 可表示当前目录
## `mkdir directory...`创建目录
## `cp item1 item2`/`cp item... directory`复制
选项|长选项	|意义
:-:|:-:|:-
-a |--archive	|复制文件和目录,以及它们的属性,包括拥有者和所有权. 通常情况下,文件拷贝具有执行拷贝操作的用户的默认属性.
-i |--interactive	|在覆盖已存在文件之前,提示用户确认.如果这个选项不指定, cp 命令会默认覆盖文件.
-r |--recursive	|递归地复制目录及目录中的内容.当复制目录时, 需要这个选项（或者 -a 选项）.
-u |--update	|当把文件从一个目录复制到另一个目录时,仅复制 目标目录中不存在的文件,或者是文件内容新于目标目录中已经存在文件的内容的文件.
-v |--verbose	|显示翔实的命令操作信息
## `mv item1 item2`(覆盖)/`mv item... directory` 移动和重命名文件
file到dir时dir必须存在,dir到dir目的dir可以不存在
与cp共享iuv选项
## `rm item...`删除文件和目录
<mark>rm不能撤销</mark>
:用通配符时先用ls测试再用上箭头换成rm
选项|长选项	|意义
:-:|:-:|:-
-i| --interactive	|在删除已存在的文件前,提示用户确认信息. 如果不指定这个选项,rm 会默默地删除文件
-r| --recursive	|递归地删除文件,这意味着,如果要删除一个目录,而此目录 又包含子目录,那么子目录也会被删除.要删除一个目录,必须指定这个选项.
-f |--force	|忽视不存在的文件,不显示提示信息.这选项覆盖了“--interactive”选项.
-v |--verbose	|在执行 rm 命令时,显示翔实的操作信息.

## `ln file link`创建硬链接/`ln -s item link`符号链接
> 符号链接item可以为文件或目录
* 硬链接
    直接链接数据,文件的所有硬链接删除后文件才会删除
    * 每个文件默认会有一个硬链接, 这个硬链接给予文件名字
    * 我们每创建一个硬链接,就为一个文件创建了一个额外的目录项
    > 局限性
    一个硬链接不能关联它所在文件系统之外的文件.这是说一个链接不能关联 与链接本身不在同一个磁盘分区上的文件.
    一个硬链接不能关联一个目录.
* 符号链接
    创建一个特殊类型的文件,这个文件包含一个关联文件或目录的文本指针
# 文件查找
## `locate` 
文件名:快速执行路径名数据库搜索,输出匹配的路径名
`[root!458 ~]# locate bin/zip`找到名字以“zip”开头的程序
可以结合grep等`locate zip | grep bin`
> locate数据库由updatedb程序创建,这是一个定时任务周期运转,大多数一天运行一回.所以最新的文件找不到.可以通过root运行`updatedb`手动运行
## `find`
搜索可多个目录名及文件 `find ~`输出家目录的路径名列表
* 可加限制条件如`find ~ -type d`
    文件类型	|find name -type X
    -|-
    b	|块特殊设备文件
    c	|字符特殊设备文件
    d	|目录
    f	|普通文件
    l	|符号链接
* 额外测试条件:`[me@linuxbox ~]$ find ~ -type f -name "*.JPG" -size +1M`查找所有文件名匹配通配符模式“*.JPG”和文件大小大于1M的普通文件
        <details>
        <summary><mark>部分测试条件</summary>

    部分测试条件	|描述
    -|-
    -cmin n	|匹配内容或属性最后修改时间正好在 n 分钟之前的文件或目录。 指定少于 n 分钟之前，使用 -n，指定多于 n 分钟之前，使用 +n。
    -cnewer file	|匹配内容或属性最后修改时间晚于 file 的文件或目录。
    -ctime n	|匹配内容和属性最后修改时间在 n*24小时之前的文件和目录。
    -empty	|匹配空文件和目录。
    -group name	|匹配属于一个组的文件或目录。组可以用组名或组 ID 来表示。
    -iname pattern	|就像-name 测试条件，但是不区分大小写。
    -inum n	|匹配 inode 号是 n的文件。这对于找到某个特殊 inode 的所有硬链接很有帮助。
    -mmin n	|匹配内容被修改于 n 分钟之前的文件或目录。
    -mtime n	|匹配的文件或目录的内容被修改于 n*24小时之前。
    -name pattern	|用指定的通配符模式匹配的文件和目录
    -newer file	|匹配内容晚于指定的文件的文件和目录。这在编写执行备份的 shell |脚本的时候很有帮。 每次你制作一个备份，更新文件（比如说日志），然后使用 find 命令来判断哪些文件自从上一次更新之后被更改了。
    -nouser	|匹配不属于一个有效用户的文件和目录。这可以用来查找 属于被删除的帐户的文件或监测攻击行为。
    -nogroup	|匹配不属于一个有效的组的文件和目录。
    -perm mode	|匹配权限已经设置为指定的 mode的文件或目录。mode 可以用 八进制或符号表示法。
    -samefile name	|类似于-inum 测试条件。匹配和文件 name 享有同样 inode 号的文件。
    -size n	|匹配大小为 n 的文件
    -type c	|匹配文件类型是 c 的文件。
    -user name	|匹配属于某个用户的文件或目录。这个用户可以通过用户名或用户 ID 来表示。
    </details>
* 逻辑关系`[me@linuxbox ~]$ find ~ \( -type f -not -perm 0600 \) -or \( -type d -not -perm 0700 \)`

# 显示命令信息
* `type` 显示命令类型
    > shell 内部命令
    ```
    [me@linuxbox ~]$ type type
    type is a shell builtins
    [me@linuxbox ~]$ type ls
    ls is aliased to `ls --color=tty`
    [me@linuxbox ~]$ type cp
    cp is /bin/cp
    ```
* `which` 显示可执行程序的位置
    ```
    [me@linuxbox ~]$ which ls
    /bin/ls
    ```
* `whatis` － 显示非常简洁的命令说明

* `help` 得到 shell 内建命令的帮助文档
    ```
    [me@linuxbox ~]$ help cd
    cd: cd [-L|-P] [dir]
    Change ...
    ```
* `--help` 显示用法信息
    ```
    [me@linuxbox ~]$ mkdir --help
    Usage: mkdir [OPTION] DIRECTORY...
    Create ...
    ```
* `man` 显示用户手册
* `apropos` 显示适合的命令
    ```
    [me@linuxbox ~]$ apropos floppy
    create_floppy_devices (8)   - udev callout to create all possible
    ...
    ```
* `info` 显示程序 Info 条目

# `alias`别名
`alias name='string'` string赋值给name
```
[me@linuxbox ~]$ type foo
bash: type: foo: not found
[me@linuxbox ~]$ alias foo='cd /usr; ls; cd -'
[me@linuxbox ~]$ foo
bin   games   kerberos  lib64    local   share  tmp
...
```
`alias` 查看所有定义在系统环境中的别名
`unalias foo` 删除别名

# 标准IO
程序(如ls)将结果输送到叫标准输出(stdout)的特殊文件,状态信息送到标准错误输出(stderr)文件
默认情况下stdout和stderr都连接到屏幕
许多程序从标准输入(stdin)设备得到输入

# 输出重定向
>更改输出地点和输入来源
* 重定向stdout: > filename
    `[me@linuxbox ~]$ ls -l /usr/bin > ls-output.txt`
    > 重写目标文件(可用来清空文件内容`[me@linuxbox ~]$ > ls-output.txt`)
    *   \>> 结果追加到目标文件尾
        `[me@linuxbox ~]$ ls -l /usr/bin >> ls-output.txt`
* 重定向stderr: > filename
* 重定向stdout和stderr到同一文件: 
    * `[me@linuxbox ~]$ ls -l > ls-output.txt 2>&1`
        > 首先重定向标准输出,然后重定向文件描述符2(标准错误输出)到文件描述符1(标准输出)使用表示法2>&1 

        >标准错误输出的重定向必须总是出现在标准输出重定向之后

    * `[me@linuxbox ~]$ ls -l  &> ls-output.txt`
* 丢弃输出: 
    `[me@linuxbox ~]$ ls -l > /dev/null`
    >重定向到/dev/null,一个系统设备,数字存储桶

# 输入重定向
## cat [file] 
> 读取一个或多个文件,复制到标准输出
   可用通配符
* 没有参数时: 读入标准输入(ctrl+D文件结尾)
    > 可用于创建文本文件`[me@linuxbox ~]$ cat > lazy_dog.txt`

    > 重定向stdin: <
    `[me@linuxbox ~]$ cat < lazy_dog.txt`
## 管道操作符 |
> 命令从标准输入读取数据并输送到标准输出的能力被一个称为管道线的shell功能所利用
管道操作符|可将命令的标准输出通过管道送到另一文件的标准输入`[me@linuxbox ~]$ ls -l | less`

组合成复杂操作:
### `sort`,`uniq` 排序删重
* `[me@linuxbox ~]$ ls /bin /usr/bin | sort | uniq | less`
    把目录/bin 和/usr/bin 中的可执行程序都联合在一起,再把它们排序,删除重复行,然后浏览执行结果
    >`uniq`接受有序列表,删除重复行
### `wc` 显示行数字数字节数
* `[me@linuxbox ~]$ ls /bin /usr/bin | sort | uniq | wc`
    >`wc`显示文件所包含的行数、字数和字节数
### `grep` 打印匹配行
`grep [options] regex [file...]`(global regular expression print)
<details>
<summary><mark>grep选项</summary>

选项	|描述
-|-
-i	|忽略大小写。不会区分大小写字符。也可用--ignore-case 来指定。
-v	|不匹配。通常，grep 程序会打印包含匹配项的文本行。这个选项导致 grep 程序只会打印不包含匹配项的文本行。也可用--invert-match 来指定。
-c	|打印匹配的数量（或者是不匹配的数目，若指定了-v 选项），而不是文本行本身。 也可用--count 选项来指定。
-l	|打印包含匹配项的文件名，而不是文本行本身，也可用--files-with-matches 选项来指定。
-L	|相似于-l 选项，但是只是打印不包含匹配项的文件名。也可用--files-without-match 来指定。
-n	|在每个匹配行之前打印出其位于文件中的相应行号。也可用--line-number 选项来指定。
-h	|应用于多文件搜索，不输出文件名。也可用--no-filename 选项来指定。
</details>

* `[me@linuxbox ~]$ ls /bin /usr/bin | sort | uniq | grep zip`
    >`grep pattern [file...]`打印匹配行,可用正则

### `head`/`tail` 打印开头结尾/实时浏览
```
[me@linuxbox ~]$ ls /usr/bin | tail -n 5
znew
...

[me@linuxbox ~]$ head -n 5 ls-output.txt
total 343496
...
```
> head打印前几行,tail后几行
默认10行,可用-n指定
```
[me@linuxbox ~]$ tail -f /var/log/messages
Feb 8 13:40:05 twin4 dhclient: DHCPACK from 192.168.1.1
....
```
> -f 实时浏览,在文件添加新的内容时同时写入,直到ctrl+C

### `tee` 从Stdin读取数据,同时输出到Stdout和(可多个)文件
```
[me@linuxbox ~]$ ls /usr/bin | tee ls.txt | grep zip
bunzip2
bzip2
....
```
> 在grep过滤管道线的内容之前,来捕捉整个目录列表到文件ls.txt和stdout

# shell中的表达方式
* echo:文本参数打印到标准输出
    ```
    [me@linuxbox ~]$ echo this is a test
    this is a test
    ```
* shell中先解释通配符再传递命令
    ```
    [root@localhost ~]# echo *
    公共 模板 视频 图片 文档 下载 音乐 桌面 anaconda-ks.cfg initial-setup-ks.cfg
    ```
* shell中$(())执行算术表达式
    ```
    [me@linuxbox ~]$ echo $(($((5**2)) * 3))
    75
    ```
* 花括号展开创建多个文本字符串
    > 可是逗号分开字符串列表,整数区间,字符区间(可嵌套)
    ```
    [me@linuxbox ~]$ echo Front-{A,B,C}-Back
    Front-A-Back Front-B-Back Front-C-Back
    ```
    ```
    [me@linuxbox ~]$ echo Number_{1..5}
    Number_1  Number_2  Number_3  Number_4  Number_5
    ```
    ```
    [me@linuxbox ~]$ echo {Z..A}
    Z Y X W V U T S R Q P O N M L K J I H G F E D C B A
    ```
    ```
    [me@linuxbox ~]$ echo a{A{1,2},B{3,4}}b
    aA1b aA2b aB3b aB4b
    ```
## $参数展开(shell中的变量)
* USER 用户名
```
[me@linuxbox ~]$ echo $USER
me
```
* printenv | less 查看有效变量列表
> 参数展开中找不到变量名会展开空字符串
## $()命令替换(将命令的输出作为另一个命令的一部分使用)
```
[root@localhost ~]# echo $(ls)
公共 模板 视频 图片 文档 下载 音乐 桌面 anaconda-ks.cfg initial-setup-ks.cfg
```
```
[root@localhost ~]# ls -l $(which bash)
-rwxr-xr-x. 1 root root 1150704 7月  22 2020 /usr/bin/bash
```

# !历史记录
写于.bash_history,可直接修改.修改后需刷新缓存`history -r`
`[me@linuxbox ~]$ history | less`浏览历史记录(默认最后500条)
* 用 | grep 查找`[me@linuxbox ~]$ history | grep /usr/bin`
* 历史命令展开`[root@localhost ~]# !90`
    > !90展开成历史记录88行的内容
* ctrl+r 增量查找
    > 输入查找的字后,继续Ctrl-r,来找到下一个匹配项
* 清空: `history -c`
# 更改身份
## `su`
`su [-[l]] [user]`
* `su username` 登录账户,exit退出(不写为root)
* `su -l username`登录其他账户,加-l则加载此用户的shell环境进入其家目录(root可省略l只写-(root也可省略`su -`))
* `su username -c 'command'`用username执行单个命令(不写为root)
## `sudo`
sudo由管理员配置,从而允许普通用户以不同身份执行命令
不要求root密码,只需user密码
`sudo -l`列出所有sudo授予的权限
## `passwd` 更改密码
root可以passwd username设置其他账户的密码

# `chown` 更改文件所有者和用户组
`chown [owner][:[group]] file...`
`[root@localhost test]# chown rose:root test` 将文件所有者改为rose,用户组改为root
    > 都可省略 `rose:` 则默认将组改为rose所属的用户组; `:root`就只改用户组
> 还有`adduser useradd groupadd`可以用来创建和维护用户和用户组

# 进程
系统启动,内核将一些活动初始化为进程,运行init程序,然后运行称为init脚本的shell脚本(/etc),由其启动所有的系统服务.许多系统服务以守护进程实现(daemon)(仅在后台运行,没有任何用户接口)

## `ps` 查看进程
ps(process status)
* `ps` 只是列出与当前终端会话相关的进程
    ```
    [root@localhost ~]# ps
        PID TTY          TIME CMD
    7536 pts/0    00:00:00 bash
    7564 pts/0    00:00:00 ps
    ```
    > TTY 是 “Teletype”(直译电传打字机)的简写,是指进程的控制终端
    TIME字段表示进程所消耗的CPU时间数量
* `ps x` 展示所有进程
    ```
    [root@localhost ~]# ps x
        PID TTY      STAT   TIME COMMAND
        1 ?        Ss     0:04 /usr/lib/systemd/systemd --switched-root --system -
        2 ?        S      0:00 [kthreadd]
        ...
    ```
    > TTY为 ? 表示没有控制终端
    STAT 是 “state” 的简写,它揭示了进程当前状态
* `ps aux` 显示属于每个用户的详细进程信息
    ```
    root@localhost ~]# ps aux
    USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
    root           1  0.0  0.3 245632 14252 ?        Ss   17:00   0:04 /usr/lib/syst
    root           2  0.0  0.0      0     0 ?        S    17:00   0:00 [kthreadd]
    ```
    标题	|含义
    -|-
    USER	|用户 ID. 进程的所有者。
    %CPU	|以百分比表示的 CPU 使用率
    %MEM	|以百分比表示的内存使用率
    VSZ	|虚拟内存大小
    RSS	|进程占用的物理内存的大小,以千字节为单位。
    START	|进程启动的时间。若它的值超过24小时,则用天表示。
* `top` 以进程活动顺序动态显示的系统进程列表
    ```
    top - 22:38:08 up  5:37,  1 user,  load average: 0.09, 0.05, 0.00
    Tasks: 307 total,   1 running, 306 sleeping,   0 stopped,   0 zombie
    %Cpu(s):  0.3 us,  0.3 sy,  0.0 ni, 99.2 id,  0.0 wa,  0.1 hi,  0.0 si,  0.0 st
    MiB Mem :   3709.4 total,   1078.0 free,   1669.2 used,    962.1 buff/cache
    MiB Swap:   9096.0 total,   9096.0 free,      0.0 used.   1776.4 avail Mem 

        PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND  
    2448 root      20   0 4833012 306240 124716 S   7.0   8.1   1:35.02 gnome-s+ 
    7531 root      20   0  605008  54044  39560 S   1.0   1.4   0:03.47 gnome-t+ 
    7764 root      20   0   65736   5088   4212 R   0.7   0.1   0:00.24 top 
    ... 
    ```
    最上面是系统概要,下面是进程列表,以CPU的使用率排序
    系统概要说明
    行号	|字段	|意义
    -|-|-
    1	|top	|程序名。
    1| 14:59:20	|当前时间。
    1 | up 6:30	|这是正常运行时间。它是计算机从上次启动到现在所运行的时间。 在这个例子里,系统已经运行了六个半小时。
    1|2 users|	有两个用户登录系统。
    1|load average:|	加载平均值是指,等待运行的进程数目,也就是说,处于可以运行状态并共享 CPU 的进程个数。 这里展示了三个数值,每个数值对应不同的时间段。第一个是最后60秒的平均值, 下一个是前5分钟的平均值,最后一个是前15分钟的平均值。若平均值低于1.0,则指示计算机 工作不忙碌。
    2|	Tasks:|	总结了进程数目和这些进程的各种状态。
    3	|Cpu(s):|	这一行描述了 CPU 正在进行的活动的特性。
    3|0.7%us	|0.7% 的 CPU 被用于用户进程。这意味着进程在内核之外。
    3|1.0%sy	|1.0%的 CPU 时间被用于系统（内核）进程。
    3|0.0%ni	|0.0%的 CPU 时间被用于低优先级进程。
    3|98.3%id	|98.3%的 CPU 时间是空闲的。
    3|0.0%wa	|0.0%的 CPU 时间来等待 I/O。
    4|	Mem:	|展示物理内存的使用情况。
    5|	Swap:	|展示交换分区（虚拟内存）的使用情况。

## 操作进程
* 直接在命令行运行
`[root@localhost ~]# gedit` ctrl+c中断
* `&` 后台运行(让 shell提示符返回,却不终止程序)
    ```
    [root@localhost ~]# gedit & 
    [1] 7980
    ```
    >  打印任务控制(job control):
    已经启动了任务号(job number)为1,PID为7980的程序
* `jobs` 列出终端中启动了的任务
    ```
    [root@localhost ~]# jobs
    [1]+  运行中               gedit &
    ```
* `fg %?` 让后台进程返回前台
    ```
    [root@localhost ~]# fg %1
    gedit
    ```
* `bg %?` 返回后台
* `ctrl+z` 停止进程(不是终止)
    使用fg或bg可以将程序恢复到前台或后台运行
* `ctrl+c` 终止进程
* `kill [-signal] PID...` 终止进程
    用`kill PID或%?`都行
    默认发送TERM(Terminate,终止)信号
    `kill -l`查看所有信号
    常用信号
    编号	|名字	|含义
    -|-|-
    1	|HUP	|挂起（Hangup）。名字来源于很久以前,那时候终端机通过电话线和调制解调器连接到 远端的计算机。这个信号被用来告诉程序,控制的终端机已经“挂断”。 通过关闭一个终端会话,可以展示这个信号的作用。在当前终端运行的前台程序将会收到这个信号并终止。许多守护进程也使用这个信号,来重新初始化。这意味着,当一个守护进程收到这个信号后, 这个进程会重新启动,并且重新读取它的配置文件。Apache 网络服务器守护进程就是一个例子。
    2	|INT	|中断。实现和 Ctrl-c 一样的功能,由终端发送。通常,它会终止一个程序。
    9	|KILL	|杀死。这个信号很特别。尽管程序可能会选择不同的方式来处理发送给它的 信号,其中也包含忽略信号,但是 KILL 信号从不被发送到目标程序。而是内核立即终止 这个进程。当一个进程以这种方式终止的时候,它没有机会去做些“清理”工作,或者是保存工作。 因为这个原因,把 KILL 信号看作最后一招,当其它终止信号失败后,再使用它。
    15	|TERM	|终止。这是 kill 命令发送的默认信号。如果程序仍然“活着”,可以接受信号,那么 这个它会终止。
    18	|CONT	|继续。在一个停止信号后,这个信号会恢复进程的运行。
    19	|STOP	|停止。这个信号导致进程停止运行,而不是终止。像 KILL 信号,它不被 发送到目标进程,因此它不能被忽略。
* `killall [-u user] [-signal] name...` 给匹配特定程序或用户名的多个进程发送信号
    ``` 
    [root@localhost ~]# killall gedit
    [2]+  已完成               gedit
    [1]+  已终止               gedit
    ```
* 更多进程命令
    命令名	|命令描述
    -|-
    pstree	|输出一个树型结构的进程列表(processtree),这个列表展示了进程间父/子关系。
    vmstat	|输出一个系统资源使用快照,包括内存,交换分区和磁盘 I/O。 为了看到连续的显示结果,则在命令名后加上更新操作延时的时间（以秒为单位）。例如,“vmstat 5”。 ,按下 Ctrl-c 组合键, 终止输出。
    xload	|一个图形界面程序,可以画出系统负载随时间变化的图形。
    tload	|terminal load 与 xload 程序相似,但是在终端中画出图形。使用 Ctrl-c,来终止输出。
### 信号
命令给程序发送信号
Ctrl-c 时,会发送一个叫做INT(Interrupt,中断)的信号
Ctrl-z 时,则发送一个叫做TSTP(Terminal Stop,终端停止)的信号
程序监听信号的到来, 接到信号之后做出响应
`kill -l`查看所有信号
其他常用信号
编号	|名字	|含义
-|-|-
3	|QUIT	|退出
11|	SEGV	|段错误(Segmentation Violation)。如果一个程序非法使用内存,就会发送这个信号。也就是说, 程序试图写入内存,而这个内存空间是不允许此程序写入的。
20|	TSTP	|终端停止(Terminal Stop)。当按下 Ctrl-z 组合键后,终端发送这个信号。不像 STOP 信号, TSTP 信号由目标进程接收,且可能被忽略。
28	|WINCH	|改变窗口大小(Window Change)。当改变窗口大小时,系统会发送这个信号。 一些程序,像 top 和 less 程序会响应这个信号,按照新窗口的尺寸,刷新显示的内容。

# shell环境
shell保存环境变量,大多数程序用配置文件存储设置,一些程序会根据环境变量调整行为
shell在环境中存储2种基本的数据(在bash几乎无法区分),环境变量和shell变量(bash存放的少量数据). 还有一些可编程数据(别名和shell函数)
* `printenv`(显示环境变量)
    > 或直接`pritenv name`查找变量(如`printenv USER`)(或`echo $HOME`也行)
* `set`(显示shell变量,环境变量,shell函数)
## 建立shell环境
登录系统后,bash程序启动并读取配置脚本启动文件(定义了默认的供所有用户共享的shell环境),
然后读取当前家目录的启动文件(定义了用户个人的shell环境)(启动顺序依赖于要运行的shell会话类型(登录shell会话和非登录shell会话))
登录 shell 会话的启动文件|	内容
-|-
/etc/profile	|应用于所有用户的全局配置脚本。
~/.bash_profile	|用户个人的启动文件。可以用来扩展或重写全局配置脚本中的设置。
~/.bash_login	|如果文件 ~/.bash_profile 没有找到,bash 会尝试读取这个脚本。
~/.profile	|如果文件 ~/.bash_profile 或文件 ~/.bash_login 都没有找到,bash 会试图读取这个文件。 这是基于 Debian 发行版的默认设置,比方说 Ubuntu。

非登录 shell 会话的启动文件	|内容
-|-
/etc/bash.bashrc	|应用于所有用户的全局配置文件。
~/.bashrc	|用户个人的启动文件。可以用来扩展或重写全局配置脚本中的设置。
## PATH变量
PATH变量存储了一个目录列表(即搜索命令行命令的目录)
* `echo $PATH` 查看PATH变量
* `PATH=$PATH:$HOME/bin` 添加 $HOME/bin 到目录列表的末尾
* `export PATH` 告诉shell让这个shell的子进程可以使用PATH变量的内容

按照通常的规则,添加目录到你的PATH变量或者是定义额外的环境变量,要把这些更改放置到.bash_profile 文件中(或者其替代文件中,根据不同的发行版。例如,Ubuntu 使用 .profile 文件)。对于其它的更改,要放到.bashrc文件中。
## 其他更改
在.bashrc末尾加新的设定,如
文本行	|含义
-|-
umask 0002	|设置掩码来解决共享目录的问题。
export HISTCONTROL=ignoredups	|使得 shell 的历史记录功能忽略一个命令,如果相同的命令已被记录。
export HISTSIZE=1000	|增加命令历史的大小,从默认的 500 行扩大到 1000 行。
alias l.='ls -d .* --color=auto'	|创建一个新命令,叫做'l.',这个命令会显示所有以点开头的目录项。
alias ll='ls -l --color=auto'	|创建一个叫做'll'的命令,这个命令会显示长格式目录列表
然后要重启新的会话才会使修改生效(.bashrc指挥在启动终端会话时读取)
可以使用`source .bashrc`

# vim
`vim`/`vi` 启动(注意alias)
> 命令要加冒号(如`:q`退出)
按2次ESC回到普通模式
* 创建新文件: `vi filename` (vi不存在的文件)
* 插入模式: 
    按i,添加文本,ESC退出
    按o,在下新建一行
    按O,在上新建一行
    按A,光标到行尾
* 保存: `:w`
* 撤销: 按u
* 剪切:
    > 可以在命令前先加一个数字指定执行次数

    命令	|剪切的文本
    -|-
    x	|当前字符
    dd	|当前行。
    dw	|从光标位置开始到下一个单词的开头。
    d$	|从光标位置开始到当前行的行尾。
    d0(零按键)	|从光标位置开始到当前行的行首。
    d^	|从光标位置开始到文本行的第一个非空字符。
    dG	|从当前行到文件的末尾。
    d20G	|从当前行到文件的第20行。
* 复制粘贴:
    > d命令剪切文本到粘贴缓冲区(剪切板)
    `p`粘贴到光标后,`P`粘贴到光标前
    y为复制,和d剪切差不多

    命令	|复制的内容
    -|-
    yy	|当前行。
    yw	|从当前光标位置到下一个单词的开头。
    y$	|从当前光标位置到当前行的末尾。
    y0	|从当前光标位置到行首。
    y^	|从当前光标位置到文本行的第一个非空字符。
    yG	|从当前行到文件末尾。
    y20G	|从当前行到文件的第20行。
* 连接行:
>vi 对于行的概念相当严格。通常,用户不可能通过删除“行尾结束符”(end-of-line character)来连接当前行和它下面的一行。(测试可以..)
大`J`链接下一行
* 查找:
`/`命令输入查找词,`回车`后按`n`循环
* 查找替换:
`:%s/Line/line/g` 将整个文件中的单词“Line”更改为“line”
    条目	|含义
    -|-
    :	|冒号字符运行一个 ex 命令。
    %	|指定要操作的行数。% 是一个快捷方式,表示从第一行到最后一行。另外,操作范围也可以用 1,5 来代替(因为我们的文件只有5行文本),或者用 1,$ 来代替,意思是 “从第一行到文件的最后一行。” 如果省略了文本行的范围,那么操作只对当前行生效。
    s	|指定操作。在这种情况下是,替换（查找与替代）。
    /Line/line	|查找类型与替代文本。
    g	|这是“全局”的意思,意味着对文本行中所有匹配的字符串执行查找和替换操作。如果省略 g,则 只替换每个文本行中第一个匹配的字符串。
    > 尾部加`c`表示需要确认(y确认n跳过,a后全确认,q/ESC退出,ctrl+e/y向上/下滚动查看)
* 光标移动
    > 可以在命令前先加一个数字指定执行次数,如`5j`将光标下移5行

    按键	|移动光标
    -|-
    l or 右箭头	|向右移动一个字符
    h or 左箭头	|向左移动一个字符
    j or 下箭头	|向下移动一行
    k or 上箭头	|向上移动一行
    0 (零按键)	|移动到当前行的行首。
    ^	|移动到当前行的第一个非空字符。
    $	|移动到当前行的末尾。
    w	|移动到下一个单词或标点符号的开头。
    W	|移动到下一个单词的开头,忽略标点符号。
    b	|移动到上一个单词或标点符号的开头。
    B	|移动到上一个单词的开头,忽略标点符号。
    Ctrl-f or Page Down	|向下翻一页
    Ctrl-b or Page Up	|向上翻一页
    numberG	|移动到第 number 行。例如,1G 移动到文件的第一行。
    G	|移动到文件末尾。

# shell提示符
设立了提示符由环境变量PS1(prompt string one)定义,`echo $PS1`查看
更改提示符时,先把原字符串复制到一个shell变量备份,然后给PS1赋值
```
自用
PS1='\[\033[s\033[0;0H\033[0;40m\033[K\033[1;33m\t \h \s \jworks\033[0m\033[u\]
[\u!\! \w]\$ '
```
写入.bashrc并加 `export PS1`

# 软件包
* 包文件包括: 安装文件,元数据(对软件包的说明),预安装和安装后脚本等
* 软件包维护者从作者得到源码,编译,创建元数据和安装脚本,修改应用到最初源码
* 依赖性: 组件调用的子例程存储在动态链接库中,称作软件包的一个依赖
* 两种工具: 底层工具用来处理这些任务,比方说安装和删除软件包文件,和上层工具，完成元数据搜索和依赖解析
    发行版	|底层工具	|上层工具
    -|-|-
    Debian-Style	|dpkg	|apt-get, aptitude
    Fedora, Red Hat Enterprise Linux, CentOS	|rpm	|yum
* 检查软件包源码树: `tar tzvf tarfile | head ---`
> 大多数软件包通过`./configure`安装(一个shell脚本)
然后会创建Makefile(一个配置文件),指示如何构建程序
然后`make`编译程序
然后`make install`在系统目录中安装程序
## 包操作
操作 |Debian |Red Hat
-|-|-
查找 包仓库中的软件包|apt-get update; apt-cache search search_string|yum search search_string
安装 从仓库|apt-get update; apt-get install package_name|yum install package_name
安装 从软件包文件|dpkg --install package_file|rpm -i package_file
更新 通过包仓库|apt-get update; apt-get upgrade|yum update
更新 通过软件包文件|dpkg --install package_file|rpm -U package_file
卸载 软件|dpkg --install package_file|yum erase package_name
列出安装的软件包|dpkg --list|rpm -qa
确认是否安装了一个软件包|dpkg --status package_name|rpm -q package_name
显示软件包信息|apt-cache show package_name|yum info package_name
查找哪个安装包对某个特殊文件负责|dpkg --search file_name|rpm -qf file_name

<details>
<summary><mark>details</summary>

* 查找 包仓库中的软件包
使用上层工具搜索包仓库元数据
    风格	|命令
    -|-
    Debian	|apt-get update; apt-cache search search_string
    Red Hat	|yum search search_string
* 安装 从仓库
上层工具从仓库下载软件包,经过完全依赖解析来安装
    风格	|命令
    -|-
    Debian	|apt-get update; apt-get install package_name
    Red Hat	|yum install package_name
* 安装 从软件包文件
通过底层工具直接安装安装包文件.rpm
    风格	|命令
    -|-
    Debian	|dpkg --install package_file
    Red Hat	|rpm -i package_file
    > 使用底层工具执行安装任务,没有依赖解析,缺少依赖会直接报错退出
* 更新 通过包仓库
上层工具实现
    风格	|命令
    -|-
    Debian	|apt-get update; apt-get upgrade
    Red Hat	|yum update
* 更新 通过软件包文件
    风格	|命令
    -|-
    Debian	|dpkg --install package_file
    Red Hat	|rpm -U package_file
* 卸载 软件
上层工具和底层工具都行,下列出上层工具
    风格	|上层工具命令
    -|-
    Debian	|apt-get remove package_name
    Red Hat	|yum erase package_name
* 列出安装的软件包
    风格	|命令
    -|-
    Debian	|dpkg --list
    Red Hat	|rpm -qa
* 确认是否安装了一个软件包
底层工具实现
    风格	|命令
    -|-
    Debian	|dpkg --status package_name
    Red Hat	|rpm -q package_name
* 显示软件包信息
    风格	|命令
    -|-
    Debian	|apt-cache show package_name
    Red Hat	|yum info package_name
* 查找哪个安装包对某个特殊文件负责
    风格	|命令
    -|-
    Debian	|dpkg --search file_name
    Red Hat	|rpm -qf file_name
</details>

# 压缩
## `gzip filename...`压缩/`gunzip`解压缩
压缩文件.gz替代原始版本,保留权限和时间戳
<details>
<summary><mark>gzip选项</summary>

选项	|说明
-|-
-c	|把输出写入到标准输出，并且保留原始文件。也有可能用--stdout 和--to-stdout 选项来指定。
-d	|解压缩。正如 gunzip 命令一样。也可以用--decompress 或者--uncompress 选项来指定.
-f	|强制压缩，即使原始文件的压缩文件已经存在了，也要执行。也可以用--force 选项来指定。
-h	|显示用法信息。也可用--help 选项来指定。
-l	|列出每个被压缩文件的压缩数据。也可用--list 选项。
-r	|若命令的一个或多个参数是目录，则递归地压缩目录中的文件。也可用--recursive 选项来指定。
-t	|测试压缩文件的完整性。也可用--test 选项来指定。
-v	|显示压缩过程中的信息。也可用--verbose 选项来指定。
-number	|设置压缩指数。number 是一个在1（最快，最小压缩）到9（最慢，最大压缩）之间的整数。 数值1和9也可以各自用--fast 和--best 选项来表示。默认值是整数6。
</details>

* 可以直接重定向到压缩文件`ls -l /etc | gzip > foo.txt.gz`
* 打印压缩文件`zcat foo.txt.gz`(同gunzip -c)
* `bzip2` 慢速高级别.bz2,使用大致同gzip

## 归档文件.tar
.tar(tape archive磁带存档)
归档就是收集许多文件,并把它们捆绑成一个大文件的过程.常作为系统备份/数据迁移的一部分来使用
`tar mode[options] pathname...`
模式	|说明
-|-
c	|为文件和／或目录列表创建归档文件。
x	|抽取归档文件。
r	|追加具体的路径到归档文件的末尾。
t	|列出归档文件的内容。
```
[me@linuxbox ~]$ tar cf playground.tar playground   #创建归档文件
[me@linuxbox ~]$ tar tvf playground.tar             #列出归档文件详细信息
[me@linuxbox test2]$ tar xf ../playground.tar       #抽取归档文件
```
> 除非你是超级用户,要不然从归档文件中抽取的文件和目录的所有权由执行者拥有,而不属于原始所有者。

* 抽取单个文件 `tar xf archive.tar pathname`相对路径
* 通过--wildcards选项来支持通配符 `[me@linuxbox foo]$ tar xf ../playground2.tar --wildcards 'home/me/playground/dir-\*/file-A'`
* 结合find `[me@linuxbox ~]$ find playground -name 'file-A' -exec tar rf playground.tar '{}' '+'`
    > 使用find命令来匹配playground目录中所有名为file-A的文件,然后使用-exec行为,来唤醒带有追加模式(r)的tar命令,把匹配的文件添加到归档文件 playground.tar 里面
* 检查软件包源码树: `tar tzvf tarfile | head ---`
# 同步`rsync`
可同步远端;同步差异,用最小的复制达成同步
`rsync options source.. destination`
* 可以为本地,远端文件或目录`[user@]host:path`,远端rsync服务器`rsync://[user@]host[:port]/path`
    > 不支持远端到远端
* 本地到本地: `[me@linuxbox ~]$ rsync -av playground foo`
    > -a 递归和保护文件属性 -v 冗余输出 --delete 删除目的设备中独有的文件
* 本地到硬盘(系统备份): `[me@linuxbox ~]$ sudo rsync -av --delete /etc /home /usr/local /media/BigDisk/backup`
* 本地到远程系统: 
    * 远程系统安装了rsync和远程shell(如ssh)`[me@linuxbox ~]$ sudo rsync -av --delete --rsh=ssh /etc /home /usr/local remote-sys:/backup`
        > -rsh==ssh 指示远程shell为ssh
    * rsync服务器.一般进行远程系统的镜像`[me@linuxbox ~]$ rsync -av -delete rsync://rsync.gtlib.gatech.edu/fedora-linux-
 core/development/i386/os fedora-devel`
        > source:协议 远端主机名 软件仓库路径
# 正则
grep(egrep),find(-regex),locate(基本--regexp扩展--regex),less&vim(/正则) 支持正则
* 元字符: `^ $ . [ ] { } - ? * + ( ) | \`,其余都为原义字符
* `.` 匹配任意字符`'.zip'`
* `^` `$` 锚点 `'^zip'`在开头找到 `'zip$'`在末尾找到
    > `'^zip$'`只有zip `'^$'`匹配空行
* `[]` 从字符集中匹配单个字符`'[bg]zip'`
    > `'[^AB]'`表否定  `'[A-Za-z0-9]'`表字符范围

    > 不同的字符集的字典排列顺序可能象`[aAbBcCdDeEfFgGhHiIjJkKlLmMnNoOpPqQrRsStTuUvVwWxXyYzZ]`这样,在`[A-Z]`时会得到不理想的效果`echo $LANG`显示字符集.修改LANG可以使用传统的ASCII排列规则
    `locale`查看locale设置,把LANG设置为POSIX可以使用传统的Unix行为`export LANG=POSIX`写入.bashrc,将使用US英语(ASCII)

以上为基本正则表达式(BRE),以下为扩展的正则表达式(ERE)
BRE中`(){}`\转义后看作元字符,ERE中加\就看作文本字符
`grep -E`支持扩展的正则表达式(即`egrep`)
* `|` 或
* `()`
* `?` 匹配一个或0个元素(前面的元素)
    >`echo "(555) 123-4567" | grep -E ^\(?[0-9][0-9][0-9]\)?  [0-9][0-9][0-9]-[0-9][0-9][0-9][0-9]$'`
* `*` 匹配多个或0个元素
    >`echo "This works." | grep -E '[[:upper:]][[:upper:][:lower:] ]*\.'`
* `+` 匹配1个或多个元素
    > `echo "This that" | grep -E '^([[:alpha:]]+ ?)+$'`匹配那些由一个或多个字母字符组构成的文本行,字母字符之间由单个空格分开
* `{}` 匹配特定个数的元素
    限定符	|意思
    -|-
    {n}	| =n
    {n,m}	|n<=次数<=m
    {n,}	|次数 >=n
    {,m}	|次数 <=m

# 文本处理
`cat` – 连接文件并且打印到标准输出
`sort` – 给文本行排序
`uniq` – 报告或者省略重复行(排好序的)
`cut` – 从每行中删除文本区域
`paste` – 合并文件文本行
`join` – 基于某个共享字段来联合两个文件的文本行
`comm` – 逐行比较两个有序的文件
`diff` – 逐行比较文件
`patch` – 给原始文件打补丁
`tr` – 翻译或删除字符
`sed` – 用于筛选和转换文本的流编辑器
`aspell` – 交互式拼写检查器

# shell脚本
不仅是一个功能强大的命令行接口,也是一个脚本语言解释器
* vim设置
    `:syntax on`/`:set syntax=sh` 语法高亮
    `:set hlsearch` 高亮查找结果
    `:set tabstop=4` 设置tab占格数
    `:set autoindent` 缩进继承
    > 把命令添加到`~/.vimrc`改动永久生效
* 编写
    ```
    #!/bin/bash
    # This is our first script.
    echo 'Hello World!'
    ```
    > 首行 #! 为特殊结构shebang,为此脚本的解释器名
* 修改权限`chmod`
    ```
    [me@linuxbox ~]$ chmod 755 hello_world
    ```
    > 755:权限设置(同文件权限)
* 执行
    ```
    [me@linuxbox ~]$ ./hello_world
    ```
    > 给出脚本文件的明确路径,不然会搜索PATH变量存储的目录来寻找此程序
    或者放到目录列表路径中