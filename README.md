# Security-wiki

> **欢迎来到Security-wiki。**
>
> 你们青年人朝气蓬勃，正在兴旺时期，好像早晨八九点钟的太阳。希望寄托在你们身上

## **这个wiki里面有什么？**

1. 目前以安全方向为主，含有计算机基础知识，编程语言（php，javascript，python，java等），基本的web安全，渗漏测试，二进制安全，安全工具和基本软件的使用（git，markdown，虚拟机，linux系统等）。
2. 在这里还可以学到任何方向都必备的基础，包括数学，英语，计算机课程，自学能力，编程能力。

## **如何使用这个wiki？**

1.  **下载到本地，Typora打开就行（推荐）**

2. 使用mkdocs搭建成网站查看（暂未优化界面显示）

    - 配置好python3环境和pip(python的包管理工具)和git。
       - python安装教程: docs/编程语言/python.md

    - `git clone https://github.com/robin1577/security.git` 下载库到本地，因为这个内部库是私有库，第一次克隆可能需要输入账号密码，如果之前配置过git就不会要求输入账号密码了。

       ```cmd
       (base) PS D:\> git clone https://github.com/robin1577/security.git
       Cloning into 'security'...
       remote: Enumerating objects: 28, done.
       remote: Counting objects: 100% (28/28), done.
       remote: Compressing objects: 100% (14/14), done.
       remote: Total 28 (delta 1), reused 28 (delta 1), pack-reused 0
       Unpacking objects: 100% (28/28), 2.53 KiB | 27.00 KiB/s, done.
       ```

    - `pip install -r requirements.txt` 安装依赖包如果pip下载依赖包过慢，将pip的下载源换成国内源 [pip换源](https://www.cnblogs.com/bigb/p/12146418.html)

    - 输入mkdocs serve就可以本地搭建起资料库了。
       ```cmd
       (base) PS D:\security> mkdocs serve
       INFO     -  Building documentation...
       INFO     -  Cleaning site directory
       INFO     -  Documentation built in 0.38 seconds
       INFO     -  [12:00:00] Serving on http://127.0.0.1:8000/
       INFO     -  [12:00:01] Browser connected: http://127.0.0.1:8000/
       ```

    - 通过浏览器访问http://127.0.0.1:8000/

## **怎么使得wiki更完善？**

1. 可以查看 `docs/工具/git.md和docs/工具/markdown.md`文件查看怎么通过git提交markdown内容。或者按照上诉命令本地搭建好之后查看网页对应目录。
2. 我们非常欢迎你为 Wiki 编写内容。
3. 非常感谢一起完善 Wiki 的小伙伴们。
