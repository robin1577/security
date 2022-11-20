# python 
## 0x01 python安装
1. 直接下载python或者下载miniconda  

    [python安装教程](https://blog.csdn.net/qq_45502336/article/details/109531599?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522163556798216780262557617%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=163556798216780262557617&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-109531599.first_rank_v2_pc_rank_v29&utm_term=python%E5%AE%89%E8%A3%85&spm=1018.2226.3001.4187)：下载python3版本即可，python2官方不维护了。

2. 下载Miniconda：
    - Miniconda是一款小巧的python环境管理工具，安装包大约只有50M多点，其安装程序中包含conda软件包管理器和Python。一旦安装了Miniconda，就可以使用conda命令安装任何其他软件工具包并创建环境等。
    - miniconda可以创建多个互不干扰的python环境，可以同时存在python2和python3环境，还可以存在不同小版本的python环境（python3.5，python3.7等）。
    - Anaconda是miniconda的复杂版本，安装之后有1G多大小。其包含了conda、Python等180多个科学包及其依赖项。
    - **尽管conda和pip都提供了安装python包的功能，但两者的源并不重合。两者各自维护自己的源。**conda源中包含了包含了很多非python的包，比如gcc，nodejs，cuda，都可以用conda来安装和管理。因此很多时候你没得选。
    - **不同环境的下conda命令安装的软件和模块都是隔离的，然后各自环境使用pip下载python第三方模块，pip下载的包也是隔离的**。

3. 检查conda是否安装：

    - 安装好之后默认会有一个base的环境，python版本是3。

    - `conda --version`:返回安装conda软件的版本

    - `conda update conda`:升级conda到最新版本

    - 使用conda命令进行第三方包的安装、卸载和更新。

    - conda换源：

        -  添加中科大镜像

            ```shell
            conda config --add channels https://mirrors.bfsu.edu.cn/anaconda/pkgs/main/
            conda config --add channels https://mirrors.bfsu.edu.cn/anaconda/pkgs/free/ 
            
            conda config --set show_channel_urls yes
            conda config --set ssl_verify yes
            ```

        - 镜像源查看：`conda config --show-sources`

        - 删除源:

            ```shell
            conda config --remove channels https://mirrors.ustc.edu.cn/anaconda/pkgs/main/
            可以删除默认源：conda config --remove channels defaults
            ```

        - 重置源：`conda config --remove-key channels`

4. minicoda环境管理：

    - 环境管理是Python使用中的一大好习惯，如果你不想在一遍遍重装Python和系统中折腾循，那么环境管理是学习Python的过程中非常必要的一环。现在我们用conda进行环境管理。

    - **创建环境**
      
        - 创建一个环境名为py36，指定Python版本是3.6（不用管是3.6.x，conda会为我们自动寻找3.6.x中的最新版本）`conda create --name py36 python=3.6`  
        - 通过创建不同的环境，我们可以使用不同版本的Python。  
        `conda create --name py27 python=2.7`
        
    - **查看全部环境**：`conda env list`

    - **激活/切换环境**：
        - 切换/激活名字为py36的环境  
        - 在windows环境下使用`conda activate py36`  
        - 在Linux & Mac中使用source activate激活`source activate py36`。  
        - 激活后，会发现terminal输入的地方多了(py36)的字样，这表示我们已经进入了py36的环境中。
        
    - 退出环境：一般不用。基本都是切换环境，  

      在windows环境下使用conda deactivate。在Linux & Mac中使用source deactivate

    - **删除环境**：如果你不想要这个名为py36的环境，可以通过
      `conda remove -n py36 --all`删除环境。

5. conda包管理：

  - 能用conda安装就用conda安装

  - 查看当前环境安装的包（包括conda安装的和pip安装的）：`conda list`

  - 利用conda安装包：

      ```shell
      conda search requests   查看requests模块是否可以通过conda安装
      conda install requests   安装requests模块
      如果有多个源有这个模块，我们可以指定从哪个源安装。
      conda install -c channel_name requests
      
      conda install --yes --file requirements.txt
      ```

  - 卸载包：`conda remove requests`

  - 更新包：`conda update xxx`

6. pip一行代码换源:` pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/  `

## 0x02 基础语法

### 控制台输入

 input() 函数接受一个标准输入数据，返回为 **string 类型**。

对于单个变量，接收后强转类型就行。`int(input())`

对于整数数组的输入，`intarr = list(map(int,input().split()))` `map返回的是可迭代对象`

### 进制转换

2，8，16进制的字符串许哟啊这些前缀。

0b / 0B --> 二进制字符前缀

0o / 0O --> 八进制字符前缀

0x / 0X --> 十六进制字符前缀

- 任何字符串形式的2，8，16进制转10进制整数。int(x,r)   r告诉int，该字符串是什么进制的。
- 10进制转2，8，16。bin(x)，oct(x)，hex(x)。转换只会同样会带上前缀

#### 1、二进制转八进制

数字0和英文b：0b10110011111为二进制数据

oct函数将一个整数转变为一个前缀为“0o”的八进制字符串

```python
x = "0b10110011111"
oct(int(x, 2)) # 结果："0o2637"
```

#### 2、二进制数据转十进制

int函数用于数字或字符转换为整型数据，第二个参数可选为2，8，16，可以将0b / 0B，0o / 0O或0x / 0X作为前缀的字符解释为整型数据。

```python
x = '0b10110011111'
int(x, 2)
# 结果
1439
```

#### 3、二进制转十六进制

hex函数将整数转换为以“0x”为前缀的小写十六进制字符串

```python
x = '0b10110011111'
hex(int(x, 2))
# 结果
'0x59f'
```

#### 4、八进制转二进制

bin函数将一个整数转变为一个前缀为“0b”的二进制字符串

```python
x = '0o2637'
bin(int(x, 8))
# 结果
'0b10110011111'
```

#### 5、八进制转十进制

```python
x = '0o2637'
int(x, 8)
# 结果
1439
```

#### 6、八进制转十六进制

```python
x = '0o2637'
hex(int(x, 8))
# 结果
'0x59f'
```

#### 7、十进制转二进制

```python
x = '1439'
bin(int(x, 10))
# 结果
'0b10110011111'
```

#### 8、十进制转八进制

```python
x = '1439'
oct(int(x, 10))
# 结果
'0o2637'
```

#### 9、十进制转十六进制

```python
x = '1439'
hex(int(x, 10))
# 结果
'0x59f'
```

#### 10、十六进制转二进制

```python
x = '0x59f'
bin(int(x, 16))
# 结果
'0b10110011111'
```

#### 11、十六进制转八进制

```python
x = '0x59f'
oct(int(x, 16))
# 结果
'0o2637'
```

#### 12、十六进制转十进制

```python
x = '0x59f'
int(x, 16)
eval("0x" + a[2:])
# 结果
1439
1439
```

#### 13、字符串转十进制

ord函数对表示单个 Unicode 字符的字符串，返回代表它 Unicode 码点的整数。例如 ord('a') 返回整数 97， ord('€') （欧元符号）返回 8364

```python
e = '一'
ord(e)
# 结果
19968
```

#### 14、字符串转二进制

```python
e = '一'
bin(ord(e))
# 结果
'0b100111000000000'
```

#### 15、字符串转八进制

```python
e = '一'
oct(ord(e))
# 结果
'0o47000'
```

#### 16、字符串转十六进制

```python
e = '一'
hex(ord(e))
# 结果
'0x4e00'

# 转为\u或者uni编码
hex(ord(e)).upper().replace('0X', r'\u')
'\\u4E00'
```

#### 17、十进制转字符串

chr(i)函数返回 Unicode 码位为整数 i 的字符的字符串格式。例如，chr(97) 返回字符串 'a'，chr(8364) 返回字符串 '€'

```python
x = 19968
chr(x)
# 结果
'一'
```

#### 18、二进制转字符串

```python
x = '0b100111000000000'
chr(int(x, 2))
# 结果
'一'
```

#### 19、八进制转字符串

```python
x = '0o47000'
chr(int(x, 8))
# 结果
'一'
```

#### 20、十六进制转字符串

```python
x = '0x4e00'
chr(int(x, 16))
x = r'\u4E00'
eval("u'" + x + "'")
# 结果
'一'
```

## 0x03 python打包exe

- ```
    pip install pyinstaller
    pip install pypiwin32
    ```

- 执行下面的命令进行打包

    - ```
        pyinstaller -F -w exp.py
        
        -w 执行的时候黑色控制台会消失
        -i xx.ico   加入图标
        ```
        
    - 打包完成后会在dist目录生成exe文件：

- 打包一个项目

    - ```
        pyinstaller -F [-w] [项目的主文件] –hidden-import [其他的python文件]
        
        
        ```

- 

 

