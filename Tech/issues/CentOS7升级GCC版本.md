# CentOS7升级GCC版本

最近了解到VScode开源后有团队开发了WEB版的VSCode，叫code-server，于是冒出了在腾讯云或公司服务器上搭建一个code-server方便iPad或其他平板设备只通过浏览器就可以进行企业级工程开发的想法。

解压好code-server在启动时报错
>/usr/lib64/libstdc++.so.6: version `GLIBCXX_3.4.21' not found

出现这个错误是因为CentOS7当前版本默认的GCC的版本太老，里面的动态链接库没有GLIBCXX_3.4.21。

执行以下命令检查动态库:
```bash
$ strings /usr/lib64/libstdc++.so.6 | grep GLIBC
```
输出结果如下:
```bash
GLIBCXX_3.4
GLIBCXX_3.4.1
GLIBCXX_3.4.2
GLIBCXX_3.4.3
GLIBCXX_3.4.4
GLIBCXX_3.4.5
GLIBCXX_3.4.6
GLIBCXX_3.4.7
GLIBCXX_3.4.8
GLIBCXX_3.4.9
GLIBCXX_3.4.10
GLIBCXX_3.4.11
GLIBCXX_3.4.12
GLIBCXX_3.4.13
GLIBCXX_3.4.14
GLIBCXX_3.4.15
GLIBCXX_3.4.16
GLIBCXX_3.4.17
GLIBCXX_3.4.18
GLIBCXX_3.4.19
GLIBC_2.3
GLIBC_2.2.5
GLIBC_2.14
GLIBC_2.4
GLIBC_2.3.2
GLIBCXX_DEBUG_MESSAGE_LENGTH
```
可以看到确实没有*GLIBCXX_3.4.21*

执行以下命令查看当前gcc版本:
```bash
$ gcc -v
```
输出gcc版本:
```bash
gcc version 4.8.5 20150623 (Red Hat 4.8.5-36) (GCC) 
```

执行命令查看libstdc++.so.6的位置:
```bash
$ find / -name libstdc++.so.6*
```
列出与libstdc++.so.6相关的文件:
```bash
/usr/lib64/libstdc++.so.6.0.19
/usr/lib64/libstdc++.so.6
/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.py
/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.pyo
/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.pyc
/data/result/SegParser/libstdc++.so.6
/data/download_record/SegParser/libstdc++.so.6
/data/segdsegy/SegParser/libstdc++.so.6
```

可以看到当前动态连接库版本是libstdc++.so.6.0.19。

进入/usr/lib64目录查看软链接情况:
```bash
$ ls -l libstdc++.so*
```
显示结果:
```bash
lrwxrwxrwx 1 root root     19 Mar 18 11:39 libstdc++.so.6 -> libstdc++.so.6.0.19
-rwxr-xr-x 1 root root 991616 Oct 30 14:39 libstdc++.so.6.0.19
```
软链接就链接到了libstdc++.so.6.0.19这个版本上。

鉴于系统内不存在更高版本的libstdc++.so.6的库，遂计划升级GCC版本。

## 升级GCC版本
安装开发必备环境:
```bash
$ yum groupinstall "Development Tools"
$ yum install glibc-static libstdc++-static
```
GCC源码地址为[http://ftp.gnu.org/gnu/gcc](http://ftp.gnu.org/gnu/gcc),里面有GCC的各个版本，请根据各自的需要下载。这里索性选择了当前最新版本gcc-8.3.0。

找到或创建并进入到一个临时目录，执行命令:
```bash
$ wget http://ftp.gnu.org/gnu/gcc/gcc-8.3.0/gcc-8.3.0.tar.gz
```
下载完成之后，解压文件:
```bash
$ tar -zxvf gcc-8.3.0.tar.gz
```
解压完成，进入文件目录:
```bash
$ cd gcc-8.3.0
```
利用源码包里自带的工具下载所需要的依赖项:
```bash
$ ./contrib/download_prerequisites
```
创建编译输出目录:
```bash
mkdir build
```
进入build目录:
```bash
$ cd build
```
生成Makefile:
```bash
$ ../configure --enable-checking=release --enable-languages=c,c++ --disable-multilib
```
编译:
```bash
$ make
```
make的过程如果遇到如下错误:
```bash
collect2: ld terminated with signal 9 [Killed]
```
有可能是主机内存过低造成的，可通过设置swap文件来解决:
```bash
$ cd ~
$ mkdir swap
$ cd swap
$ dd if=/dev/zero  of=swapfile  count=4096  bs=1024k
$ chmod 600 swapfile
$ mkswap swapfile
$ swapon swapfile
```
通过命令:
```bash
$ swapon -s //查看swap文件的状态
$ free -m   //查看内存当前用量
```
查看swap分区文件的挂载情况。

编译完成之后，安装:
```bash
$ make install
```
完成安装之后，检查是否安装成功:
```bash
$ gcc -v
Using built-in specs.
COLLECT_GCC=gcc
COLLECT_LTO_WRAPPER=/usr/local/libexec/gcc/x86_64-pc-linux-gnu/8.3.0/lto-wrapper
Target: x86_64-pc-linux-gnu
Configured with: ../configure -enable-checking=release -enable-languages=c,c++ -disable-multilib
Thread model: posix
gcc version 8.3.0 (GCC) 
```
gcc版本已变成最新版，查看动态库是否已正常:
```bash
$ strings /usr/lib64/libstdc++.so.6 | grep GLIBC
```
然而发现并没有，查找编译gcc时生成的最新动态库:
```bash
$ find / -name "libstdc++.so*"
```
执行结果如下:
```bash
/usr/local/lib64/libstdc++.so.6.0.25
/usr/local/lib64/libstdc++.so
/usr/local/lib64/libstdc++.so.6.0.25-gdb.py
/usr/local/lib64/libstdc++.so.6
/usr/lib64/libstdc++.so.6.0.19
/usr/lib64/libstdc++.so.6
/usr/lib/gcc/x86_64-redhat-linux/4.8.2/libstdc++.so
/usr/lib/gcc/x86_64-redhat-linux/4.8.2/32/libstdc++.so
/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.py
/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.pyo
/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.pyc
/opt/gcc-8.3.0/build/x86_64-pc-linux-gnu/libstdc++-v3/src/.libs/libstdc++.so.6.0.25
/opt/gcc-8.3.0/build/x86_64-pc-linux-gnu/libstdc++-v3/src/.libs/libstdc++.so
/opt/gcc-8.3.0/build/x86_64-pc-linux-gnu/libstdc++-v3/src/.libs/libstdc++.so.6
/opt/gcc-8.3.0/build/stage1-x86_64-pc-linux-gnu/libstdc++-v3/src/.libs/libstdc++.so.6.0.25
/opt/gcc-8.3.0/build/stage1-x86_64-pc-linux-gnu/libstdc++-v3/src/.libs/libstdc++.so
/opt/gcc-8.3.0/build/stage1-x86_64-pc-linux-gnu/libstdc++-v3/src/.libs/libstdc++.so.6
/opt/gcc-8.3.0/build/prev-x86_64-pc-linux-gnu/libstdc++-v3/src/.libs/libstdc++.so.6.0.25
/opt/gcc-8.3.0/build/prev-x86_64-pc-linux-gnu/libstdc++-v3/src/.libs/libstdc++.so
/opt/gcc-8.3.0/build/prev-x86_64-pc-linux-gnu/libstdc++-v3/src/.libs/libstdc++.so.6
/data/result/SegParser/libstdc++.so.6
/data/download_record/SegParser/libstdc++.so.6
/data/segdsegy/SegParser/libstdc++.so.6
```
可以看到在路径/usr/local/lib64/libstdc++.so.6.0.25下有一个新的高版本的库，于是将此库复制到/usr/lib64/路径下并重新建立软连接:
```bash
$ cp /usr/local/lib64/libstdc++.so.6.0.25 /usr/lib64/
$ rm libstdc++.so.6
$ ln -s libstdc++.so.6.0.25 libstdc++.so.6
```
完成之后通过命令
```bash
strings /usr/lib64/libstdc++.so.6 | grep GLIBC
```
可以看到*GLIBCXX_3.4.21*已经存在。

问题解决。