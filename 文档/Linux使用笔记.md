# Linux使用笔记



## 一、& 最经常被用到

```shell
$ pct &
```

这个用在一个命令的最后，可以把这个命令放到后台执行。

fg、bg、jobs、&、ctrl + z都是跟系统任务有关的，虽然现在基本上不怎么需要用到这些命令，但学会了也是很实用的。

ps 列出系统中正在运行的进程；

　　kill 发送信号给一个或多个进程（经常用来杀死一个进程）；

　　jobs 列出当前shell环境中已启动的任务状态，若未指定jobsid，则显示所有活动的任务状态信息；

如果报告了一个任务的终止(即任务的状态被标记为Terminated)，shell 从当前的shell环境已知的列表中删除任务的进程标识；

　　bg 将进程搬到后台运行（Background）；

​	`$ bg %JobsID`

　　fg 将进程搬到前台运行（Foreground）；

​	`$ fg %JobsID`

---

-  防止断开终端后停止

  上方提到的放到后台执行的进程,其父进程还是当前终端shell的进程,而一旦父进程退出,则会发送hangup信号给所有子进程,子进程收到hangup以后也会退出。如果我们要在退出shell的时候继续运行进程,则需要使用nohup忽略hangup信号,或者setsid将将父进程设为init进程(进程号为1)

  ```shell
  $ echo $$ 
  21734 
  $ nohup ./test.sh &; 
  [1] 29016 
  $ ps -ef | grep test 
  51529710 217340 11:47 pts/12 00:00:00 /bin/sh ./test.sh 
  51529713 217340 11:47 pts/12 00:00:00 grep test 
  $ setsid ./test.sh &; 
  [1] 409 
  $ ps -ef | grep test 
  515 410 10 11:49 ? 00:00:00 /bin/sh ./test.sh 
  515 413 217340 11:49 pts/12 00:00:00 grep test 
  ```

  上面的试验演示了使用nohup/setsid加上&;使进程在后台运行,同时不受当前shell退出的影响。

- 同样,对于已经前端启动的任务, 我们也有办法:使用disown命令 

  ```shell
  $ ./test.sh &; 
  [1] 2539 
  $ jobs -l 
  [1]+2539 Running ./test.sh &; 
  $ disown -h %1 
  $ ps -ef | grep test 
  515 410 10 11:49 ? 00:00:00 /bin/sh ./test.sh 
  5152542 217340 11:52 pts/12 00:00:00 grep test 
  ```

  ​

## 输出重定向

　'<' and '>'分别用来支持linux中的输入输出重定向，其中'<'支持输入重定向，'>'支持输出重定向。

　　1.  '<'：重定向输入

　　　　sh test.sh < hadoop-hadoop-jobtracker-brix-00.out，将hadoop-hadoop-jobtracker-brix-00.out的内容作为test.sh的输入

​     	2.  '>'：将内容全局覆盖式的加入文件，相当于删除该文件并重新建立该文件，再写入的效果

​      　　ls * > test.txt ，将ls * 的所有信息输出到文件test.txt中

　　3. '>!'：如果存在则覆盖

　　4. '>&'：执行时屏幕上所产生的任何信息写入指定的文件中

　　5. '>>'：追加到文件中

　　6. '>>&'：屏幕上的信息追加到文件中