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

    

