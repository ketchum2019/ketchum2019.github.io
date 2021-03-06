# Linux

## 创建进程的系统调用有那些?

**clone(), fork(), vfork();** 系统调用服务例程:sys_clone,sys_fork,sys_vfork;

## 常用命令

三大利器

grep：正则匹配查找

> 单个字符串范围符号 '[a-z]'   取反符号^	grep '[\^a]' file
>
> 边界字符：^root 头尾字符 false\$ 头尾字符 \^$空行
>
> 任意字符串: .*

![image-20200713101847885](https://raw.githubusercontent.com/ketchum2019/ketchum2019.github.io/master/images/image-20200713101847885.png)

sed：行编辑器 流处理编辑器（文本或管道输入）

> 

awk：文本处理工具（统计制表可编程）

> 

## 进程调度方案

先来先去服务：

短作业(进程)优先调度算法SJ(P)F

时间片轮转

最高响应比优先

公平共享调度

## 进程中的区域划分

1. 代码区：（text）主要用于存放功能代码，函数指针指向该区域，比如函数名。
2. 只读常量区：（text）存放字符串常量和用const修饰的，并且已经初始化的全局变量和静态局部变量。
3. 全局区/数据区：（data）存放已经初始化的全局变量和静态局部变量（这个区的数据都有存储空间，生命周期是整个程序）。
4. bss段：存放没有初始化的全局变量和静态局部变量，bss段会在main函数执行前清0。
5. 堆区：主要表示使用malloc / calloc / realloc / free等手动申请的动态内存，需要手动释放。
6. 栈区：主要存放非静态局部变量（包括函数的参数），块变量，有操作系统自动管理。

## 物理地址和逻辑地址的转换

**段地址\*16（十进制）+偏移地址=物理地址**

**逻辑地址**是在有地址变换功能的计算机中,访内指令给出的地址 (操作数) 叫逻辑地址,也叫相对地址，也就是是机器语言指令中，用来指定一个操作数或是一条指令的地址。要经过寻址方式的计算或变换才得到内存储器中的实际有效地址即物理地址。一个逻辑地址由两部份组成，段标识符: 段内偏移量。段标识符是由一个16位长的字段组成，称为段选择符。其中前13位是个索引号，后面3位包含一些硬件细节 。

## 根据端口号查看进程

 netstat -nap | grep 进程pid

## 查看进程是否启动

通过ps -ef | grep xxx 查询xxx进程是否启动

例如ps -ef | grep redis

# Git常用命令

## 一般工作流程如下：

- 克隆 Git 资源作为工作目录。
- 在克隆的资源上添加或修改文件。
- 如果其他人修改了，你可以更新资源。
- 在提交前查看修改。
- 提交修改。
- 在修改完成后，如果发现错误，可以撤回提交并再次修改并提交。

> git init
>
> git clone
>
> git add
>
> git status -s
>
> git diff
>
> git commit
>
> git rm
>
> git branch (branchname) 创建分支
>
> git checkout (branchname) 切换分支
>
> git branch -d (branchname) 删除分支
>
> git log
>
> git tag -a v1.0  git标签
>
> git remote add [shortname] [url]  添加远程库
>
> 