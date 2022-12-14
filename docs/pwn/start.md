# Pwn-简介

Pwn是二进制安全的方向之一，在进入Pwn的世界前需要许多前置知识门槛较高，难入门，这里介绍一下如何CTF-Pwn入门以及IOT安全入门

### CTF-Pwn

CTF-Pwn 要面对的东西没那么多，大多数情况只有一个可执行文件（或者再加上一个libc文件），然后再拖进IDA中F5反汇编，生成伪代码，查找漏洞，验证漏洞，漏洞利用，攻击靶机泄露flag或拿到shell，这是常规的做题方式，但是在开始做题前有下面的基础会进行的更顺利，当然啥都不会也能对着入门题对其中涉及到的知识点一点一点啃下去（这就需要亿点点的毅力和热爱才能坚持下去~~虽然搞安全就是这样的~~)

* C/C++编程基础

​		在分析初级Pwn文件时逻辑简单，一眼就明了，但是在往后的二进制文件分析时经常会参杂许多算法，涉及到加密解密或者故意把代码写的复杂难以分析，这时候如果没有扎实的编程基础便会走走停停，觉得代码过于抽象难以理解，导致连漏洞点也找不出来，倒在了逻辑分析这一块。建议把栈，链表，各种排序算法，二叉树等基本算法都去了解了解，适当的刷点编程题，都对这些数据结构有个概念并能独立编程实现的时候就差不多了。

* Linux命令

​		CTF-Pwn的环境大多都是在Linux下进行的，有必要了解Linux的各种命令，有些题目会把某些命令给过滤掉，让你用其他命令拼接的方式来读取flag文件，或者对输入做过滤，使得构造的shellcode不能含有sh等字符，让你以其他的方式获得shell。即使不是在CTF中Linux也是经常使用的，对其中的命令越熟悉越好。

* 可执行文件结构及动态静态链接知识

​		既然是对可执行文件进行分析那就得了解这个可执行文件是个啥，Linux上的可执行文件格式为ELF，windows上的可执行文件格式为PE，Pwn手需要侧重了解的是ELF文件结构，了解其中各种表结构和段信息，了解动态链接的过程。这些都在CTF题目中都有所体现，比如ret2-dl-reslove利用方式就是通过伪造符号表来劫持重定位过程来调用我们需要的函数，而动态链接中的plt表和got表也是重点关注的对象，泄露地址或对其进行改写也是常规操作。这里推荐《程序员的自我修养》和《深入理解计算机系统》两本书，前者对链接的知识讲的比较清楚，后者涵盖的范围非常广，对理解二进制有很大的帮助。

* 汇编语言

​		汇编语言有多种格式，32位-x86\64位-x64，AT&T语法以及arm、mips等。x86/x64就是经常见到的汇编语言，AT&T在Linux上比较常见，arm，mips等嵌入式汇编语言比较少见。建议先了解x86/64汇编语言，能看懂一类其他通过百度谷歌搜索语法也能快速看懂。在学习的过程中要重点了解的是调用函数的过程中参数传递的方式，比如x86中参数按照一般的函数调用约定是从右到左依次压入栈上的，因为参数在栈上就可能引起栈溢出等漏洞，而x64中前五个参数是放在rdi, rsi, rdx, r8, r9中的，放在寄存器中的话进行rop就需要找gadget来布置参数，这样就提升了利用难度。

* 动态调试

​		在进行漏洞利用时想知道自己的payload有没有达到效果就需要进行动态调试查看内存中的数据来验证。普通的gdb调试功能一般，需要安装peda或pwndbg等插件来调试目标文件，安装这两个插件后在调试过程中会显示寄存器信息、汇编代码以及栈上数据，非常直观。通过下断点等方式运行到某行代码查看内存数据是通常都会进行的操作。

有了以上基础基本上就可以开始刷题去提升自己的实力了，建议去ctf-wiki根据上面提到的漏洞逐个了解，拓宽知识点，多参加比赛当赛棍锻炼自己，刷题到一定程度就会有自己的理解根据自己的兴趣去学些有趣的东西了。

### IOT安全入门

先推荐一本书《揭秘家用路由器0day漏洞挖掘技术》，这本书对没接触过IOT安全的人来说比较友好，一是设备，搞IOT比较头疼的一点是设备，毕竟不是所有IOT设备都能通过仿真模拟来进行测试，而对学生党来说设备也不是想买就能随便买的，这本书上面涉及的例子都是很老的路由器，在咸鱼淘宝上的价格都比较便宜，适合入手做个测试。二是难度，上面涉及的漏洞大多都是栈溢出漏洞，都不难理解，难的是搭建环境，有了环境才能进行漏洞测试和攻击利用。IOT设备的架构多是arm/mips，拿到的固件里面的服务程序不能直接在Linux虚拟机中直接运行，需要借助qemu来模拟环境才能运行，这个踩的坑多了就自然轻车熟路了。

在这本书中分析步骤一般如下：

* 获取目标设备固件

​		一般固件可以在设备的官网中进行下载，不过也有一些是找不到或者不支持下载的，这时候需要拿到设备然后拆卸，在硬件层面进行固件提取。

* 对固件中的服务程序进行分析，找出漏洞点

​		这个比较简单，只需要把书中提到的漏洞程序找到放进IDA中分析逻辑就行

* 对漏洞点需要进行测试，找出交互的方式，找出通过在哪里输入payload可以触发这个漏洞

​		固件中的服务程序不像CTF中的题目一样会有提示输入，可能是通过发送网络数据包进行交互的，需要通过python来进行交互，在这本书上的例子中的交互方式都好理解，如果研究当下的一些路由器摄像头会比较头疼，没经验的话甚至都找不到交互点在哪里

* 若无设备就搭建仿真环境，通过qemu和gdb联合调试目标程序，进行poc验证

​		仿真环境搭建起来后就可以直接运行arm/mips架构的程序，配合gdbserver可以在arm/mips虚拟机中开一个调试端口，通主机连接调试

* 编写EXP进行漏洞利用，一般目标是开启telnet端口以供远程登录

把这本书从头到尾看一遍对IOT安全就差不多有一个初步的认识了













​	

