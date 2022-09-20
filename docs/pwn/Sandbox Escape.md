# Sandbox Escape

沙箱(sandbox)：计算机领域的虚拟技术。在安全方向中，表示在隔离环境中，用以测试不受信任的文件或应用程序等行为的工具。即将不信任的软件放入沙箱中，一旦该软件有恶意行为，则禁止该软件进一步运行，不会对真实系统造成伤害

## C沙盒逃逸

在CTF的pwn题中，沙盒一般都会限制execve的系统调用，所以一般拿不到shell，但是对于CTF而言，只需要获取flag即可，故可以使用orw（ open-read-write ）方法来获取flag

### 开启沙盒的方式

#### prctl函数调用

```c
// 函数原型
#include <sys/prctl.h>
int prctl(int option, unsigned long arg2, unsigned long arg3, unsigned long arg4, unsigned long arg5);

// option选项有很多，剩下的参数也由option确定，这里介绍两个主要的option
// PR_SET_NO_NEW_PRIVS(38) 和 PR_SET_SECCOMP(22)

// option为38的情况
// 此时第二个参数设置为1，则禁用execve系统调用且子进程一样受用
prctl(38, 1LL, 0LL, 0LL, 0LL);

// option为22的情况
// 此时第二个参数为1，只允许调用read/write/_exit(not exit_group)/sigreturn这几个syscall
// 第二个参数为2，则为过滤模式，其中对syscall的限制通过参数3的结构体来自定义过滤规则。
prctl(22, 2LL, &v1);

```

详细用法：http://www.kernel.org/doc/man-pages/online/pages/man2/prctl.2.html

#### seccomp库函数

### seccomp-tools

可以使用该工具识别沙箱，地址：https://github.com/david942j/seccomp-tools

安装：先安装ruby环境，再安装该工具

```
sudo apt install gcc ruby-dev
gem install seccomp-tools
```

使用方法：`seccomp-tools dump ./pwn`

具体使用方法可以查看相关的README.md

