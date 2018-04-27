
# 如何实现Python脚本在Linux中的快速执行
---
## 需求

- 环境：Linux
- 运行Python脚本时不用输入脚本完整路径名
  - 修改Linux系统路径可解决

- 脚本需要接收四个参数：

    ```Python 3
    # Filename: psub.py
    from sys import argv

    for n in range(4):
        print(argv[n])
    ```
    为此我不能用命令行运行`python3 psub.py a1 a2 a3 a4`来实现，因为我的四个参数都是被`Shell`传给了`python3`而不是传给`psub`。
- 我当时还不清楚Python脚本可以直接在文件头部加上一句话来选择解释器，考虑过新建一个Shell脚本来代替手动输入文件路径：
    ```Shell Script
    #!/bin/bash
    # Filename: psub
    #!/bin/bash
    python3 psub.py
    ```
  给可执行权限之后就可以运行了，但是这种方式不能传递参数。

## 解决

我使用Linux系统没几天，很多不懂的地方。提问之后得到了几个回答，大家的回答都非常有帮助。

1. [@西门靖](https://www.zhihu.com/people/fangjzh/activities)指出，修改`~/.bashrc`文件

    ```shell script
    alias psub='python3 /xxx/xxx/psub.py'
    ```

    随后重启`Shell`便可使改动生效，命令`psub a1 a2 a3 a4`即可。


2. [@贺安妮](https://www.zhihu.com/people/he-a-ni/activities)指出，shell脚本可以通过`$+数字`的方式将参数间接传递给`psub.py`文件：
    ```Shell Script
    #!/bin/bash
    # Filename: psub
    #argv :  $1 $2 $3 $4 
    python3 psub.py $1 $2 $3 $4
    ```
    这样一来我就能通过命令`psub a1 a2 a3 a4`来用四个参数运行我的脚本文件`psub.py`了。

3. 我在提问之后不久就找到了最符合自己需要(可能在问题里没能完整传达)的方案:在Python脚本第一行加入一行
    ```Shell Script
    #!/usr/bin/env python3
    ```
    如此之后所有在系统搜索目录下的脚本都可以通过一行命令行来运行了。

4. 此外，**匿名用户**指出，
    >用软连接换掉shell脚本;
    >```Shell Script
    >cd /usr/bin #进环境变量的目录
    >sudo ln -s /xxx/xxx/psub.py psub  #创建软链接
    >#软链接你就理解成快捷方式好了
    >```

## 源问题链接
[Ubuntu里面如何让chmod +x 之后的文件能够读入命令行参数？](https://www.zhihu.com/question/268778527)