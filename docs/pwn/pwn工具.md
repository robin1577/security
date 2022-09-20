[TOC]

# pwn工具

## gdb

### 介绍

GDB是 **GNU**开源组织发布的一个强大的Unix/Linux下的程序调试工具，主要可以用来调试C/C++的可执行程序

### 安装

### 判断可执行文件是否可调试

对于C程序来讲，在编译的时候记得gcc记得加上-g参数，保留调试信息，否则不能进行调试

```
gcc -g hello.c -o hello
```

对于不是自己编译的可执行文件，可以通过以下两种方式来判断：

+ gdb 可执行文件

  ```
  $ gdb hello
  Reading symbols from hello...(no debugging symbols found)...done.
  ```

  表示没有调试信息

  ```
  $ gdb hello
  Reading symbols from hello...done.
  ```

  此情况可以调试

+ file查看

  ```
  $ file hello
  hello:......stripped
  ```

  此情况一定没有调试信息，不能调试

  ```
  $ file hello
  hello: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=64a9b458fc2f722ba694d0e884abd43e8cb51cb9, not stripped
  ```

  如果后面是not stripped，这种情况不一定

+ readelf查看段信息

  ```
  $ readelf -S hello | grep debug
    [28] .debug_aranges    PROGBITS         0000000000000000  0000106d
    [29] .debug_info       PROGBITS         0000000000000000  0000109d
    [30] .debug_abbrev     PROGBITS         0000000000000000  0000112e
    [31] .debug_line       PROGBITS         0000000000000000  00001170
    [32] .debug_str        PROGBITS         0000000000000000  000011ac
  ```

  出现dubug信息，表示可以调试，否则查询不到

### 启动调试

#### 运行与调试程序

##### 启动无参数程序

```
$ gdb hello
(gdb) run
Starting program: /home/dililearngent/Desktop/pwn/hello 
Hello,world!
[Inferior 1 (process 55921) exited normally]
```

##### 启动带参数程序

代码如下：

```C
#include<stdio.h>
int main(int argc,char *argv[])
{
    if(1 >= argc)
    {
        printf("usage:hello name\n");
        return 0;
    }
    printf("Hello World %s!\n",argv[1]);
    return 0 ;
}
```

编译加调试：

```
$ gcc -g -o hello1 hello1.c
$ gdb hello1

(gdb) run lsy
Starting program: /home/dililearngent/Desktop/pwn/hello1 lsy
Hello World lsy!
[Inferior 1 (process 56013) exited normally]
```

运行是时直接**run+参数**即可

或者使用**set args**

```
gdb hello
(gdb) set args lsy
(gdb) run
```

##### 启动core

概念：程序由于各种异常或者bug导致在运行过程中异常退出或者中止，在满足一定条件下，会产生一个core文件

core文件会包含了程序运行时的内存，寄存器状态，堆栈指针，内存管理信息还有各种函数调用堆栈信息等

当程序core dump时，可能会生成core文件，查看系统是否限制core文件产生的命令：**ulimit -c**

```
$ ulimit -c
0               //表示即使程序core dump，系统也不会留下core文件
```

修改或设置：

```
$ ulimit -c unlimied  #表示不限制core文件大小
$ ulimit -c 10  #表示最大的大小，以块为单位，一块为512字节，至少需要4才能生成core文件
```

调试core文件：

```
$gdb 程序文件名 core文件名
```

coredump产生的原因：

+ 内存访问越界
+ 多线程读写的数据未加锁
+ 多线程程序使用了线程不安全函数
+ 非法指针
+ 堆栈溢出

例子分析：

#### 调试已运行程序

如果程序已经在运行了，可以通过查找进程号并依附进程来进行调试

```
ps -ef|grep 进程名        #查找已运行程序的进程ID
#两种方式：
#方式一：
$ gdb
(gdb) attach 2222
#方式二：
gdb hello 2222
或者
gdb hello --pid 2222
```

如果出现Could not attach to process...等错误，表示现有的权限不能够调试当前的程序，需要切换至root用户，具体方法：将/etc/sysctl.d/10-ptrace.conf中的kernel.yama.ptrace_scope = 1修改为kernel.yama.ptrace_scope = 0

通常对于一些已运行的程序一般没有调试信息，故可以编译出一个带调试的版本，再利用上面的步骤进行调试

```
$ gdb
(gdb) file hello
Reading symbols from hello...done.
(gdb)attach 2222
```

### 断点

#### 查看已设置的断点

```
info breakpoints
```

#### 断点设置

示例程序：

```c
//test.c
#include<stdio.h>
int PrintNum(int a)
{
    printf("printNum\n");
    while(a > 0)
    {
        printf("%d\n",a);
        a--;
    }
}
int Sum(int a,int b)
{
    printf("a=%d,b=%d\n",a,b);
    int temp=a+b;
    return temp;
}
int Div(int a,int b)
{
    printf("a=%d,b=%d\n",a,b);
    int temp=a/b;
    return temp;
}
int main(int argc,char *argv[])
{
    int a;
    PrintNum(5);
    a = Sum(2,3);
    printf("sum=%d\n",a);
    Div(5,0);
    return 0;
}
```

编译：

```
gcc -g -o test test.c
```

##### 一、根据行号设置断点

```
b 9
b test.c:9       #break可以简写为b
```

##### 二、根据函数名设置断点

```
b PrintNum
```

##### 三、根据条件设置断点

```
break test.c:20 if b==0
```

假设这个断点是1号断点，则可以通过condition来修改条件或者添加条件

```
condition 1 b==0
```

##### 四、设置临时断点

```
tbreak test.c:5
```

只生效一次

##### 五、根据规则设置断点

```
rbreak printNum*      //对所有调用printNum函数的地方设置断点
rbreak .              //对所有函数设置断点
rbreak test.c:.       //对test.c中的所有函数设置断点
rbreak tets.c:^print  //对test.c中以print开头的函数设置断点

规则：
rbreak file:regex
```

##### 六、跳过断点

```
ingorn 1 20    //其中1表示断点的序号，20表示需要跳过该断点的次数
```

##### 七、根据表达式的值变化设置断点

```
watch a       //程序继续运行，如果a的值发生变化会打印相关的内容
```

rwatch表示当变量被读的时候会断，awatch表示变量被读或者被写的时候产生断点

#### 断点清除

```
clear                       #删除当前行所有breakpoints
clear function              #删除函数名为function处的断点
clear filename:function     #删除文件filename中函数function处的断点
clear lineNum               #删除行号为lineNum处的断点
clear filename：lineNum     #删除文件filename中行号为lineNum处的断点
delete                      #删除所有breakpoints,watchpoints和catchpoints
delete bnum                 #删除断点号为bnum的断点
```

#### 禁用或者启用断点

```
disable                     #禁用所有断点
disable bnum                #禁用标号为bnum的断点
enable                      #启用所有断点
enable bnum                 #启用标号为bnum的断点
enable delete bnum          #启动标号为bnum的断点，并且在此之后删除该断点
```

### 单步调试

示例程序：

```c
//test1.c
#include<stdio.h>
int add(int a,int b)
{
    printf("a=%d,b=%d\n",a,b);
    int temp=a+b;
    return temp;
}
void count(int a)
{
    printf("count=%d\n",a);
    while(a>0){
        printf("%d\n",a);
        a--;
    }
}
int main(int argc,char *argv[])
{
    count(5);
    int temp;
    temp=add(2,7);
    printf("sum=%d",temp);
    return 0;
}
```

编译：

```
gcc -g -o test1 test1.c
```

#### 显示源码-list

```
(gdb) l
5	    printf("a=%d,b=%d\n",a,b);
6	    int temp=a+b;
7	    return temp;
8	}
9	void count(int a)
10	{
11	    printf("count=%d\n",a);
12	    while(a>0){
13	        printf("%d\n",a);
14	        a--;
(gdb) list
15	    }
16	}
17	int main(int argc,char *argv[])
18	{
19	    count(5);
20	    int temp;
21	    temp=add(2,7);
22	    printf("sum=%d",temp);
23	    return 0;
24	}
```

#### 单步执行-next

```
(gdb) b 20                                                            //在第20行设置断点
Breakpoint 1 at 0x4005b6: file test1.c, line 20.
(gdb) info breakpoints                                                //查看设置的断点
Num     Type           Disp Enb Address            What
1       breakpoint     keep y   0x00000000004005b6 in main at test1.c:20
(gdb) r                                                               //执行程序
Starting program: /home/dililearngent/Desktop/pwn/test1 
count=5
5
4
3
2
1

Breakpoint 1, main (argc=1, argv=0x7fffffffddc8) at test1.c:21
21	    temp=add(2,7);                                                //在21行断住
(gdb) n                                                               //单步执行，即执行21行程序
a=2,b=7
22	    printf("sum=%d",temp);                       
(gdb) n 2                                                            //单步执行两次程序，接下来需要执行的是24行
24	}
```

由上例可以发现，在第21行断住之后，单步执行之后，直接跳到下一步将要执行22行，没有进入add函数。故此命令单步执行是直接跳过函数执行

#### 单步进入-step

```
(gdb) b 20                                                          //在20行设置断点
Breakpoint 1 at 0x4005b6: file test1.c, line 20.
(gdb) info breakpoints                                              //查看设置的断点
Num     Type           Disp Enb Address            What
1       breakpoint     keep y   0x00000000004005b6 in main at test1.c:20
(gdb) r                                                             //执行
Starting program: /home/dililearngent/Desktop/pwn/test1 
count=5
5
4
3
2
1

Breakpoint 1, main (argc=1, argv=0x7fffffffddc8) at test1.c:21
21	    temp=add(2,7);                                              //在21行断住
(gdb) s                                                             //单步进入add函数
add (a=2, b=7) at test1.c:5
5	    printf("a=%d,b=%d\n",a,b);                                  //下一步需要执行第5行
(gdb) s                                                           //执行printf，如果该函数没有源码，则显示不了
__printf (format=0x400674 "a=%d,b=%d\n") at printf.c:28
28	printf.c: No such file or directory.
(gdb) finish                                                        //使用finish跳过该函数
Run till exit from #0  __printf (format=0x400674 "a=%d,b=%d\n") at printf.c:28
a=2,b=7                                                             //输出结果
add (a=2, b=7) at test1.c:6                                         //下一步需要执行的代码
6	    int temp=a+b;
Value returned is $1 = 8
(gdb) finish                                                        //跳过add函数
Run till exit from #0  add (a=2, b=7) at test1.c:6      
0x00000000004005c5 in main (argc=1, argv=0x7fffffffddc8) at test1.c:21
21	    temp=add(2,7);                     
Value returned is $2 = 9
(gdb) s
22	    printf("sum=%d",temp);
```

如果没有函数调用，s和n的作用是一致的

设置：当遇到一个没有调试信息的函数时，s命令是否跳过，默认会跳过,默认为off

```
(gdb) show step-mode 
Mode of the step operation is off.
(gdb) set step-mode on
(gdb) set step-mode off
```

**stepi**，简写为si，每次只执行一条指令

#### 继续执行到下一个断点continue

```
(gdb) b 14                                                               //在14行设置断点
Breakpoint 2 at 0x400590: file test1.c, line 14.
(gdb) r                                                                  //执行
Starting program: /home/dililearngent/Desktop/pwn/test1 
count=5
5

Breakpoint 2, count (a=5) at test1.c:14
14	        a--;
(gdb) c                                                                  //继续执行，遇到下一个断点停住
Continuing.
4

Breakpoint 2, count (a=4) at test1.c:14
14	        a--;
(gdb) c 2                                                                //两次跳过断点
Will ignore next crossing of breakpoint 2.  Continuing.
3
2

Breakpoint 2, count (a=2) at test1.c:14
14	        a--;
(gdb) c
Continuing.
1

Breakpoint 2, count (a=1) at test1.c:14
14	        a--;
(gdb) c
Continuing.
a=2,b=7
sum=9[Inferior 1 (process 57629) exited normally]
```

#### 继续运行到指定位置-until

```
(gdb) b 19                                                                  //在19行下断点
Breakpoint 3 at 0x4005ac: file test1.c, line 19.
(gdb) r                                                                     //执行
Starting program: /home/dililearngent/Desktop/pwn/test1 

Breakpoint 3, main (argc=1, argv=0x7fffffffddc8) at test1.c:19
19	    count(5);                                                           //在19行断住
(gdb) u 23                                                                  //运行至23行
count=5
5
4
3
2
1
a=2,b=7
main (argc=1, argv=0x7fffffffddc8) at test1.c:23
23	    return 0;
(gdb) c
Continuing.
sum=9[Inferior 1 (process 57647) exited normally]
```

#### 跳过执行-skip

```
(gdb) skip function add    #step时跳过add函数
Function add will be skipped when stepping.
(gdb) info skip   #查看step情况
Num     Type           Enb What
1       function       y   add
```

在使用step时不会进入函数add

```
skip file test1.c
```

不会进入test1.c文件

```
skip delete [num] 删除skip
skip enable [num] 使能skip
skip disable [num] 去使能skip
```

### 查看变量

示例程序：

```c
//test2.c
#include<stdio.h>
#include<stdlib.h>
int main(void)
{
    int a = 10; //整型
	int b[] = {1,2,3,5};  //数组
    char c[] = "hello,shouwang";//字符数组
	/*申请内存，失败时退出*/    
	int *d = (int*)malloc(a*sizeof(int));
	if(NULL == d)
	{
		printf("malloc error\n");
		return -1;
	}
	/*赋值*/
    for(int i=0; i < 10;i++)
	{
		d[i] = i;
	}
	free(d);
	d = NULL;
	float e = 8.5f;
	return 0;
}
```

编译：

```
gcc -g -o test2 test2.c
```

#### 将变量类型打印

```
(gdb) p a                             //可打印字符数组和字符串数组

(gdb) p 'testGdb.h'::a               //当多个文件有相同的变量名时，可以借助此种方式
$1 = 11
(gdb) p 'main'::b
$2 = {1, 2, 3, 5}
```

#### 打印指针

```
(gdb) p d                           //打印出该指针的地址
#打印指针的内容
(gdb) p *d                          //打印出指针的第一个元素
(gdb) p *d@10                       //打印出该指针的前10个元素
```

#### 按照特定格式打印变量

```
p/x c
p/t e
```

- x 按十六进制格式显示变量。
- d 按十进制格式显示变量。
- u 按十六进制格式显示无符号整型。
- o 按八进制格式显示变量。
- t 按二进制格式显示变量。
- a 按十六进制格式显示变量。
- c 按字符格式显示变量。
- f 按浮点数格式显示变量

#### 查看内存中的内容-examine

```
x/[n][f][u] addr
n 表示要显示的内存单元数，默认值为1
f 表示要打印的格式，前面已经提到了格式控制字符
u 要打印的单元长度
addr 内存地址
```

单元类型：

- b 字节
- h 半字，即双字节
- w 字，即四字节
- g 八字节

#### 设置自动显示某个变量

```
display a                           //当程序每次遇到断点后，会自动显示该变量
info display                        //查看该类型的所有设置
delete display num                  //num为前面变量前的编号,不带num时清除所有
disable display num                 //num为前面变量前的编号，不带num时去使能所有
```

#### 查看寄存器的内容

```
info registers
```

### 查看源码

#### 列出源码

```
l                                  //(list简写)列出源码，可以使用+或者-列出前后的源码
l 9                                //列出第9行附近的源码
l printNum                         //列出指定函数的源码
l 3,15                             //列出3-15行之间的源码
set listsize 20                    //设置源码一次列出的行数
show listsize                      //查询该值
l location                          //列出指定文件的源码
l test.c:1
l test.c:printNum
l test.c:1,test.c:3
```

#### 源码和程序不再同一目录

比如源码被移到当前的temp目录下

```
(gdb) l
1	main.c: No such file or directory.
(gdb) dir ./temp
```

更换源码路径：

```
readelf main -p .debug_str                         //查找源码路径，main为调试的程序
set substitute-path from to                        //更换路径
show substitute-path							   //显示路径
unset substitute-path                              //取消路径设置
```

#### 编辑源码

在调试过程中需要编辑源码，不退出程序，默认编辑程序是/bin/ex，可以重新设置编辑器

```
$ EDITOR=/usr/bin/vim
$ export EDITOR
#注：可以使用下面命令查询编辑器的位置
whereis vim
或
which vim
```

gdb模式下编辑：

```
(gdb)edit 3                                 //编辑第三行
(gdb)edit printNum                          //编辑printNum函数
(gdb)edit test.c:5                          //编辑test.c第五行
```

gdb模式下重新编译，需要加shell,其他的与正常编译一致

```
(gdb)shell gcc -g -o main main.c test.c
```

### 其他

#### -tui

```
gdb test -tui                //出现多窗口
```

#### 列出所有函数

```
info functions
```

## peda

### 安装

安装 peda 需要的软件包：

```
$ sudo apt-get install nasm micro-inetd
$ sudo apt-get install libc6-dbg vim ssh
```

安装 peda：

```
$ git clone https://github.com/longld/peda.git ~/peda
$ echo "source ~/peda/peda.py" >> ~/.gdbinit
$ echo "DONE! debug your program with gdb and enjoy"
```

如果git出现错误，则换成

```
$ git clone git://github.com/longld/peda.git ~/peda
或者先使用此命令
git config --global url."https://".insteadOf git://
```



### 使用

#### 显示/设定GDB的ASLR(地址空间配置随机加载)设置-ASLR

```
$ aslr
$ aslr on
$ aslr off
```

#### 检查二进制文件的各种安全选项-checksec

```
gdb-peda$ checksec
CANARY    : disabled
FORTIFY   : disabled
NX        : disabled
PIE       : disabled
RELRO     : Partial
```

CANARY:栈溢出保护是一种缓冲区溢出攻击缓解手段，当启用栈保护后，函数开始执行的时候会先往栈里插入类似cookie的信息，当函数真正返回的时候会验证cookie信息是否合法，如果不合法就停止程序运行。攻击者在覆盖返回地址的时候往往也会将cookie信息给覆盖掉，导致栈保护检查失败而阻止shellcode的执行。

```
gcc -fno-stack-protector -o hello test.c    //禁用栈保护
gcc -fstack-protector -o hello test.c    //启用堆栈保护，不过只为局部变量中含有 char 数组的函数插入保护代码
gcc -fstack-protector-all -o hello test.c  //启用堆栈保护，为所有函数插入保护代码
```

FORTIFY:由GCC实现的源码级别的保护机制，其功能是在编译的时候检查源码以避免潜在的缓冲区溢出等错误。比如使用了read等敏感函数时会替换成__read_chk等

NX:NX enabled如果这个保护开启就是意味着栈中数据没有执行权限,当攻击者在堆栈上部署自己的 shellcode 并触发时, 只会直接造成程序的崩溃，但是可以利用rop这种方法绕过

```
gcc -o  hello test.c // 默认情况下，开启NX保护
gcc -z execstack -o  hello test.c // 禁用NX保护
gcc -z noexecstack -o  hello test.c // 开启NX保护
```

PIE:PIE(Position-Independent Executable, 位置无关可执行文件)技术,PIE 技术在编译时将程序编译为位置无关, 即程序运行时各个段（如代码段等）加载的虚拟地址也是在装载时才确定。

ASLR 将程序运行时的堆栈以及共享库的加载地址随机化,在 PIE 和 ASLR 同时开启的情况下, 攻击者将对程序的内存布局一无所知, 传统的改写GOT 表项的方法也难以进行, 因为攻击者不能获得程序的.got 段的虚地址

```
gcc -o hello test.c  // 默认情况下，不开启PIE
gcc -fpie -pie -o hello test.c  // 开启PIE，此时强度为1
gcc -fPIE -pie -o hello test.c  // 开启PIE，此时为最高强度2
(还与运行时系统ALSR设置有关）
```

RELRO:Relocation Read-Only (RELRO) 此项技术主要针对 GOT 改写的攻击方式。它分为两种，Partial RELRO 和 Full RELRO。

```
gcc -o hello test.c // 默认情况下，是Partial RELRO
gcc -z norelro -o hello test.c // 关闭，即No RELRO
gcc -z lazy -o hello test.c // 部分开启，即Partial RELRO
gcc -z now -o hello test.c // 全部开启，即Full RELRO
```

#### 生成shellcode-shellcode

```
gdb-peda$ shellcode generate x86/linux exec
# x86/linux/exec: 24 bytes
shellcode = (
    "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x31"
    "\xc9\x89\xca\x6a\x0b\x58\xcd\x80"
)
```

#### 生成字符串模板-pattern

写入内存 用于定位溢出点

```
gdb-peda$ pattern create 100
'AAA%AAsAABAA$AAnAACAA-AA(AADAA;AA)AAEAAaAA0AAFAAbAA1AAGAAcAA2AAHAAdAA3AAIAAeAA4AAJAAfAA5AAKAAgAA6AAL'
gdb-peda$ pattern offset A6
A6 found at offset: 95
```



## nm

检查中间目标文件时（扩展名为.o的文件），默认输出结果是在这个文件中声明的如何函数和全局变量

```
gcc -c test1.c
nm test1.o
0000000000000000 T add
0000000000000035 T count
0000000000000077 T main
                 U printf
```

大写字母表示全局变量，小写字母表示局部变量

+ U，未定义符号，通常为外部符号引用。
+ T，在文本部分定义的符号，通常为函数名称。
+ t，在文本部分定义的局部符号。在C程序中，这个符号通常等同于一个静态函数。
+ D，已初始化的数据值。
+ c，未初始化的数据值。

查看链接后的二进制文件：

```
nm test1
0000000000400526 T add
0000000000601038 B __bss_start
0000000000601038 b completed.7594
000000000040055b T count
0000000000601028 D __data_start
0000000000601028 W data_start
0000000000400460 t deregister_tm_clones
00000000004004e0 t __do_global_dtors_aux
0000000000600e18 t __do_global_dtors_aux_fini_array_entry
0000000000601030 D __dso_handle
0000000000600e28 d _DYNAMIC
0000000000601038 D _edata
0000000000601040 B _end
0000000000400664 T _fini
0000000000400500 t frame_dummy
0000000000600e10 t __frame_dummy_init_array_entry
0000000000400808 r __FRAME_END__
0000000000601000 d _GLOBAL_OFFSET_TABLE_
                 w __gmon_start__
0000000000400694 r __GNU_EH_FRAME_HDR
00000000004003c8 T _init
0000000000600e18 t __init_array_end
0000000000600e10 t __init_array_start
0000000000400670 R _IO_stdin_used
                 w _ITM_deregisterTMCloneTable
                 w _ITM_registerTMCloneTable
0000000000600e20 d __JCR_END__
0000000000600e20 d __JCR_LIST__
                 w _Jv_RegisterClasses
0000000000400660 T __libc_csu_fini
00000000004005f0 T __libc_csu_init
                 U __libc_start_main@@GLIBC_2.2.5
000000000040059d T main
                 U printf@@GLIBC_2.2.5
00000000004004a0 t register_tm_clones
0000000000400430 T _start
0000000000601038 D __TMC_END__
```

## ldd

动态链接与静态链接：

```
$ gcc -o test1_dynamic test1.c
$ gcc -o test1_static test1.c --static
$ ls -l test1_*
-rwxrwxr-x 1 dililearngent dililearngent   8664 10月 22 14:58 test1_dynamic
-rwxrwxr-x 1 dililearngent dililearngent 912808 10月 22 14:59 test1_static
$ file test1_dynamic
test1_dynamic: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=7cc2711ebb111994fa0f75a7a0217456759fff26, not stripped

$ file test1_static
test1_static: ELF 64-bit LSB executable, x86-64, version 1 (GNU/Linux), statically linked, for GNU/Linux 2.6.32, BuildID[sha1]=ee941f147454b084b05415f27c7a30ffe64a2084, not stripped
```

使用 ldd 命令打印所依赖的共享库：

```
$ ldd test1_dynamic
	linux-vdso.so.1 =>  (0x00007ffe1d1fc000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f913d75c000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f913db26000)
$ ldd test1_static
	not a dynamic executable

```

## objdump

显示与目标文件有关的信息

### 显示目标文件头-f

```
$ objdump -f test1
test1:     file format elf64-x86-64
architecture: i386:x86-64, flags 0x00000112:
EXEC_P, HAS_SYMS, D_PAGED
start address 0x0000000000400430
```

### 显示段表-h

```
$ objdump -h test1

test1:     file format elf64-x86-64

Sections:
Idx Name          Size      VMA               LMA               File off  Algn
  0 .interp       0000001c  0000000000400238  0000000000400238  00000238  2**0
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  1 .note.ABI-tag 00000020  0000000000400254  0000000000400254  00000254  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  2 .note.gnu.build-id 00000024  0000000000400274  0000000000400274  00000274  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  3 .gnu.hash     0000001c  0000000000400298  0000000000400298  00000298  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  4 .dynsym       00000060  00000000004002b8  00000000004002b8  000002b8  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  5 .dynstr       0000003f  0000000000400318  0000000000400318  00000318  2**0
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
.....
```

### 对包含机器指令的段进行反汇编-d

```
$ objdump -d test1
...
000000000040059d <main>:
  40059d:	55                   	push   %rbp
  40059e:	48 89 e5             	mov    %rsp,%rbp
  4005a1:	48 83 ec 20          	sub    $0x20,%rsp
  4005a5:	89 7d ec             	mov    %edi,-0x14(%rbp)
  4005a8:	48 89 75 e0          	mov    %rsi,-0x20(%rbp)
  4005ac:	bf 05 00 00 00       	mov    $0x5,%edi
  4005b1:	e8 a5 ff ff ff       	callq  40055b <count>
  4005b6:	be 07 00 00 00       	mov    $0x7,%esi
  4005bb:	bf 02 00 00 00       	mov    $0x2,%edi
  4005c0:	e8 61 ff ff ff       	callq  400526 <add>
  4005c5:	89 45 fc             	mov    %eax,-0x4(%rbp)
  4005c8:	8b 45 fc             	mov    -0x4(%rbp),%eax
  4005cb:	89 c6                	mov    %eax,%esi
  4005cd:	bf 8d 06 40 00       	mov    $0x40068d,%edi
  4005d2:	b8 00 00 00 00       	mov    $0x0,%eax
  4005d7:	e8 24 fe ff ff       	callq  400400 <printf@plt>
  4005dc:	b8 00 00 00 00       	mov    $0x0,%eax
  4005e1:	c9                   	leaveq 
  4005e2:	c3                   	retq   
  4005e3:	66 2e 0f 1f 84 00 00 	nopw   %cs:0x0(%rax,%rax,1)
  4005ea:	00 00 00 
  4005ed:	0f 1f 00             	nopl   (%rax)
  ...
```

### 其他参数

+ -a:列举.a文件中所有的目标文件。
+ -b bfdname:指定BFD名。
+ -C:对于C++符号名进行反修饰（Demangle)。
+ -g:显示调试信息。
+ -d:对包含机器指令的段进行反汇编。
+ -D:对所有的段进行反汇编。
+ -f:显示目标文件文件头。
+ -h:显示段表。
+ -l:显示行号信息。
+ -p:显示专有头部信息，具体内容取决于文件格式。
+ -r:显示重定位信息。
+ -R:显示动态链接重定位信息。
+ -s:显示文件所有内容。
+ -S:显示源代码和反汇编代码（包含-d参数)。
+ -W:显示文件中包含有DWARF调试信息格式的段。
+ -t:显示文件中的符号表。
+ -T:显示动态链接符号表。
+ -x:显示文件的所有文件头。

## readelf

用于查看elf文件格式，常用文件是linux上的可执行文件、动态库(*.so)、静态库(\*.a)

### elf文件开始的文件头信息-h

显示elf文件开始的文件头信息

```
$ readelf -h test2
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00 
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              EXEC (Executable file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x400530
  Start of program headers:          64 (bytes into file)
  Start of section headers:          7896 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         9
  Size of section headers:           64 (bytes)
  Number of section headers:         37
  Section header string table index: 34
```

### 显示程序头（段头）信息-l

```
$ readelf -l test2

Elf file type is EXEC (Executable file)
Entry point 0x400530
There are 9 program headers, starting at offset 64

Program Headers:
  Type           Offset             VirtAddr           PhysAddr
                 FileSiz            MemSiz              Flags  Align
  PHDR           0x0000000000000040 0x0000000000400040 0x0000000000400040
                 0x00000000000001f8 0x00000000000001f8  R E    8
  INTERP         0x0000000000000238 0x0000000000400238 0x0000000000400238
                 0x000000000000001c 0x000000000000001c  R      1
      [Requesting program interpreter: /lib64/ld-linux-x86-64.so.2]
  LOAD           0x0000000000000000 0x0000000000400000 0x0000000000400000
                 0x00000000000008e4 0x00000000000008e4  R E    200000
  LOAD           0x0000000000000e10 0x0000000000600e10 0x0000000000600e10
                 0x0000000000000240 0x0000000000000248  RW     200000
  DYNAMIC        0x0000000000000e28 0x0000000000600e28 0x0000000000600e28
                 0x00000000000001d0 0x00000000000001d0  RW     8
  NOTE           0x0000000000000254 0x0000000000400254 0x0000000000400254
                 0x0000000000000044 0x0000000000000044  R      4
  GNU_EH_FRAME   0x00000000000007b8 0x00000000004007b8 0x00000000004007b8
                 0x0000000000000034 0x0000000000000034  R      4
  GNU_STACK      0x0000000000000000 0x0000000000000000 0x0000000000000000
                 0x0000000000000000 0x0000000000000000  RW     10
  GNU_RELRO      0x0000000000000e10 0x0000000000600e10 0x0000000000600e10
                 0x00000000000001f0 0x00000000000001f0  R      1
.......
```

### 显示节头信息-S

```
$ readelf -S test2
There are 37 section headers, starting at offset 0x1ed8:

Section Headers:
  [Nr] Name              Type             Address           Offset
       Size              EntSize          Flags  Link  Info  Align
  [ 0]                   NULL             0000000000000000  00000000
       0000000000000000  0000000000000000           0     0     0
  [ 1] .interp           PROGBITS         0000000000400238  00000238
       000000000000001c  0000000000000000   A       0     0     1
  [ 2] .note.ABI-tag     NOTE             0000000000400254  00000254
       0000000000000020  0000000000000000   A       0     0     4
  [ 3] .note.gnu.build-i NOTE             0000000000400274  00000274
       0000000000000024  0000000000000000   A       0     0     4
  [ 4] .gnu.hash         GNU_HASH         0000000000400298  00000298
       000000000000001c  0000000000000000   A       5     0     8
  [ 5] .dynsym           DYNSYM           00000000004002b8  000002b8
       00000000000000a8  0000000000000018   A       6     1     8
  [ 6] .dynstr           STRTAB           0000000000400360  00000360
       0000000000000064  0000000000000000   A       0     0     1
```

### 其他

+ -h：文件头

+ -S：段表

+ -s：符号表

+ -d: 查看依赖库

+ -p：查看某个段内容

+ -x：选项 -x,hex-dump=<number or name> 以16进制方式显示指定段内内容。number指定段表中段的索引,或字符串指定文件中的段名

  ```
  readelf -x 1 test1
  ```

+ -H：help 显示readelf所支持的命令行选项

## strings

专门用于提取文件中字符串内容

```
$ strings test1
/lib64/ld-linux-x86-64.so.2
libc.so.6
printf
__libc_start_main
__gmon_start__
GLIBC_2.2.5
UH-8
AWAVA
AUATL
[]A\A]A^A_
a=%d,b=%d
count=%d
sum=%d
;*3$"
GCC: (Ubuntu 5.4.0-6ubuntu1~16.04.12) 5.4.0 20160609
test1.c
=Hj>
........
```

## pwntools

Pwntools 是一个 CTF 框架和漏洞利用开发库，用 Python 开发，由 rapid 设计，旨在让使用者简单快速的编写 exp 脚本。包含了本地执行、远程连接读写、shellcode 生成、ROP 链的构建、ELF 解析、符号泄露众多强大功能。

### 安装

方法1：

1、安装binutils

```
git clone https://github.com/Gallopsled/pwntools-binutils
sudo apt-get install software-properties-common
sudo apt-add-repository ppa:pwntools/binutils
sudo apt-get update
sudo apt-get install binutils-arm-linux-gnu
```

2、安装capstone

```
git clone https://github.com/aquynh/capstone
cd capstone
make
sudo make install
```

3、安装pwntools

```
sudo apt-get install libssl-dev
sudo pip install pwntools
```

方法2:以上方法可能会碰到很多问题

```
$ apt-get update
$ apt-get install python python-pip python-dev git libssl-dev libffi-dev build-essential
$ python2 -m pip install --upgrade pip==20.3.4
$ python2 -m pip install --upgrade pwntools
```

参考链接：https://docs.pwntools.com/en/stable/install.html

### 介绍

Pwntools 分为两个模块，一个是 `pwn`，简单地使用 `from pwn import *` 即可将所有子模块和一些常用的系统库导入到当前命名空间中，是专门针对 CTF 比赛的；而另一个模块是 `pwnlib`，它更推荐你仅仅导入需要的子模块，常用于基于 pwntools 的开发。

### 模块

#### tubes

#### shellcraft

#### asm

用于汇编和反汇编

汇编(pwnlib.asm.asm):

```

```

反汇编(pwnlib.asm.disasm)

#### elf

该模块用于 ELF 二进制文件的操作，包括符号查找、虚拟内存、文件偏移，以及修改和保存二进制文件等功能。(pwnlib.elf.elf.ELF)

```
>>> e = ELF('./ret2libc3')                                     
[*] '/home/dililearngent/Desktop/pwn1/ret2libc3'
    Arch:     i386-32-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x8048000)
>>> print hex(e.address)                                           //ELF 文件装载的基地址
0x8048000

>>> print hex(e.symbols['main'])                                  //main函数地址
0x8048618

>>> print hex(e.plt['puts'])                                      //PLT 表地址
0x8048460

>>> print hex(e.got['__libc_start_main'])                        //got表地址
0x804a024
```

- `asm(address, assembly)`：汇编指定指令并插入到 ELF 的指定地址处，需要使用 ELF.save() 保存
- `bss(offset)`：返回 `.bss` 段加上 `offset` 后的地址
- `checksec()`：打印出文件使用的安全保护
- `disable_nx()`：关闭 NX
- `disasm(address, n_bytes)`：返回对指定虚拟地址进行反汇编后的字符串
- `offset_to_vaddr(offset)`：将指定偏移转换为虚拟地址
- `vaddr_to_offset(address)`：将指定虚拟地址转换为文件偏移
- `read(address, count)`：从指定虚拟地址读取 `count` 个字节的数据
- `write(address, data)`：在指定虚拟地址处写入 `data`
- `section(name)`：获取 `name` 段的数据
- `debug()`：使用 `gdb.debug()` 进行调试

#### gdb

