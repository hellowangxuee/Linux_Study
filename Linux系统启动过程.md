### Linux系统启动过程

* **内核引导**

  当计算机打开电源后，首先是BIOS开机自检，按照BIOS中设置的启动设备（通常是硬盘）来启动。

  操作系统接管硬件以后，首先读入 /boot 目录下的内核文件

* **运行init**

  init 进程是系统所有进程的起点

  init 程序首先是需要读取配置文件 /etc/inittab。

  * **运行级别**

    许多程序需要开机启动。它们在Windows叫做"服务"（service），在Linux就叫做"守护进程"（daemon）。

    init进程的一大任务，就是去运行这些开机启动的程序。

    但是，不同的场合需要启动不同的程序，比如用作服务器时，需要启动Apache，用作桌面就不需要。

    Linux允许为不同的场合，分配不同的开机启动程序，这就叫做"运行级别"（runlevel）。也就是说，启动时根据"运行级别"，确定要运行哪些程序

  * **Linux系统有7个运行级别(runlevel)**

    * 运行级别0：系统停机状态，系统默认运行级别不能设为0，否则不能正常启动
    * 运行级别1：单用户工作状态，root权限，用于系统维护，禁止远程登陆
    * 运行级别2：多用户状态(没有NFS)
    * 运行级别3：完全的多用户状态(有NFS)，登陆后进入控制台命令行模式
    * 运行级别4：系统未使用，保留
    * 运行级别5：X11控制台，登陆后进入图形GUI模式
    * 运行级别6：系统正常关闭并重启，默认运行级别不能设为6，否则不能正常启动

* **系统初始化**

  在init的配置文件中有这么一行： si::sysinit:/etc/rc.d/rc.sysinit　它调用执行了/etc/rc.d/rc.sysinit，而rc.sysinit是一个bash shell的脚本，它主要是完成一些系统初始化的工作，rc.sysinit是每一个运行级别都要首先运行的重要脚本。

  它主要完成的工作有：激活交换分区，检查磁盘，加载硬件模块以及其它一些需要优先执行任务

* **建立终端**

  rc执行完毕后，返回init。这时基本系统环境已经设置好了，各种守护进程也已经启动了。

  init接下来会打开6个终端，以便用户登录系统。在inittab中的以下6行就是定义了6个终端

  ```Linux
  1:2345:respawn:/sbin/mingetty tty1
  2:2345:respawn:/sbin/mingetty tty2
  3:2345:respawn:/sbin/mingetty tty3
  4:2345:respawn:/sbin/mingetty tty4
  5:2345:respawn:/sbin/mingetty tty5
  6:2345:respawn:/sbin/mingetty tty6
  ```

  从上面可以看出在2、3、4、5的运行级别中都将以respawn方式运行mingetty程序，mingetty程序能打开终端、设置模式。

  同时它会显示一个文本登录界面，这个界面就是我们经常看到的登录界面，在这个登录界面中会提示用户输入用户名，而用户输入的用户将作为参数传给login程序来验证用户的身份

* **用户登陆系统**

  一般来说，用户的登录方式有三种：

  - （1）命令行登录
  - （2）ssh登录
  - （3）图形界面登录

### Linux关机

在linux领域内大多用在服务器上，很少遇到关机的操作。毕竟服务器上跑一个服务是永无止境的，除非特殊情况下，不得已才会关机。

正确的关机流程为：sync > shutdown > reboot > halt

```Linux
sync 将数据由内存同步到硬盘中。

shutdown 关机指令，你可以man shutdown 来看一下帮助文档。例如你可以运行如下命令关机：

shutdown –h 10 ‘This server will shutdown after 10 mins’ 这个命令告诉大家，计算机将在10分钟后关机，并且会显示在登陆用户的当前屏幕中。

Shutdown –h now 立马关机

Shutdown –h 20:25 系统会在今天20:25关机

Shutdown –h +10 十分钟后关机

Shutdown –r now 系统立马重启

Shutdown –r +10 系统十分钟后重启

reboot 就是重启，等同于 shutdown –r now

halt 关闭系统，等同于shutdown –h now 和 poweroff
```

不管是重启系统还是关闭系统，首先要运行 **sync** 命令，把内存中的数据写到磁盘中。

关机的命令有 **shutdown –h now halt poweroff** 和**init 0** , 重启系统的命令有 **shutdown –r now reboot init 6**

### 参考文章

http://www.ruanyifeng.com/blog/2013/08/linux_boot_process.html