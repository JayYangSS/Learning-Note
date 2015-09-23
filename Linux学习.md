###while循环注意的事项
这个`while`循环比较蛋疼的是空格。使用的括号为中括号，括号和变量之间必须有空格，变量和比较符号之间也必须有空格，格式如下：
```sh
#!/bin/bash
Passwd=666666
echo 猜一下密码！
echo -n 输入密码:
read guss_passwd
while [ "$Passwd" != "$guss_passwd" ]
do
    echo 错误密码！
    echo -n 输入错误，请重新输入：
    read guss_passwd
done
echo 恭喜你，输入正确！
exit 0
```

###Linux C程序设计中的几个关键问题

1. 头文件：几乎所有的C语言的头文件都放在/usr/include及其子目录下，如`stdio.h`,`stdlib.h`等文件；
2. 静态函数库：以`.a`结尾，如`/usr/lib/libc.a`是标准的C语言函数库
3. 使用静态函数库有个问题，就是当很多程序都要用到某一个函数库的函数时，必须为每个程序都要复制一份同样的函数，浪费空间。因此，**共享函数库**解决了这一问题，Linux系统中，共享函数库存放在`/lib`中，典型的Linux中可以找到`/lib/libc.so.N`,N为主版本号。它是在程序运行时动态加载的，有点像`.dll`文件 