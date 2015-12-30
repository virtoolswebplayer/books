### 在终端里打开 open命令详解
```sh
# 打开当前目录
open . 
# 打开个人根目录
open ~
# 打开指定目录
open /
# 打开网址
open http://www.baidu.com
# 打开应用程序
open /Application/Webstorm
```

### 创建文件
```sh
# 创建filename.txt
touch filename.txt
```

### .bashrc 和 .bash_profile的区别

`.bash_profile`是在登陆的时候才会执行的，它也可以叫做`.bash_login`,也就是说你在命令行再运行bash命令的时候是不会执行这个文件里面的命令的。而`.bashrc`恰好相反，是在执行子shell(sub-shell)的时候才会执行里面的命令。

> 现在默认的.bash_profile大多都有这么一句：

```
# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi
```
所以登陆的时候也会执行到.bashrc里面的命令。
