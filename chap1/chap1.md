
## 系统调用
```c
int fork()              创建一个子进程，   返回子进程的PID
int exit(int status)        中止当前进程，status会返回给wait(),没有返回
int wait(int *status)    等待一个子进程退出， 子进程退出的状态会传给 *status.没有返回值
int kill(int pid)             关闭一个进程，成功返回0， 失败返回-1
int getpid()            返回当前进程的PID
int sleep(int n)        暂停n个时钟周期
int exec(char *file, char* argv)
char *sbrk(int n)       给进程再分配n个字节的内存空间，返回新空间的起始地方
char open(char *file, int flags)    打开一个文件，flags代表 read/write  返回 fd(file descriptor)
int write(int fd, char *buf,int n)  从buf读取n个字节写入 文件fd代表的文件中
int read(int fd, char *buf, int n)      读取n个字节放入buf中，返回读取的字节个数， 如果是文件结尾就返回0
int close(int fd)       关闭fd指向的文件
int dup(int fd)          返回一个新的文件描述 ，这个文件描述指向的文件和fd一样
int pipe(int p[])           创建一个管道， read文件描述是p[0], write文件描述是p[1]
int chdir(char *dir)     改变当前目录
int mkdir(char *dir)          创建一个新目录
int mknod(int cahr *file,int ,int)   创建一个设备文件(device file) 
int fstat(int fd, struct stat *st)      把一个打开的文件放入*st
int stat(char *file, struct stat *st)   把一个命名好的文件放入 *st (翻译存疑？Place info about a named file into *st.)
int link(char *file1, char *file2)      给file1创建一个新名字叫做file2
int unlink(char *file)  取消一个文件

//一般规定返回值为0代表没有error, -1代表有error
```
系统调用|含义
-|-
int fork()       |       创建一个子进程，   返回子进程的PID  
int exit(int status)  |      中止当前进程，status会返回给wait(),没有返回
int wait(int *status)  |  等待一个子进程退出， 子进程退出的状态会传给 *status.没有返回值
int kill(int pid)       |      关闭一个进程，成功返回0， 失败返回-1   
int getpid()           | 返回当前进程的PID  
int sleep(int n)        |暂停n个时钟周期   
int exec(char *file, char* argv)   | exec函数簇
char *sbrk(int n)      | 给进程再分配n个字节的内存空间，返回新空间的起始地方  
char open(char *file, int flags)  |  打开一个文件，flags代表 read/write  返回 fd(file descriptor)  
int write(int fd, char *buf,int n) |  从buf读取n个字节写入 文件fd代表的文件中  
int read(int fd, char *buf, int n)  |    读取n个字节放入buf中，返回读取的字节个数， 如果是文件结尾就返回0  
int close(int fd)       |关闭fd指向的文件   
int dup(int fd)          |返回一个新的文件描述 ，这个文件描述指向的文件和fd一样  
int pipe(int p[])         |  创建一个管道， read文件描述是p[0], write文件描述  是p[1]
int chdir(char *dir)    | 改变当前目录  
int mkdir(char *dir)     |     创建一个新目录  
int mknod(int cahr *file,int ,int) |  创建一个设备文件(device file)   
int fstat(int fd, struct stat *st)  |    把一个打开的文件放入*st  
int stat(char *file, struct stat *st) |  把一个命名好的文件放入 *st (翻译存疑？Place info about a named file into *st.)  
int link(char *file1, char *file2)     | 给file1创建一个新名字叫做file2
int unlink(char *file)  |取消一个文件

### exit
>一般 exit(0)代表成功， exit(1)代表程序遇到意外中止。


## 1.2 I/O和文件描述符
>&emsp;&emsp;文件描述符是一个小的整数，可以看作一个文件对象被内核管理。文件描述符可以通过<b>打开一个文件、目录、设备、创建管道或者从已经有的文件描述符复制而来</b>。为了方便可以把文件描述符抽象看作一个 <b>文件</b>。  
&emsp;&emsp;上述几个创建文件描述符的方法都抽象实现了接口，使他们使用起来像字节流。
&emsp;&emsp;每一个进程都有自己独立的文件描述符空间，全部0开始。一般情况下 0代表标准输入，1代表标准输出， 2代表标准错误。shell保证始终至少有三个文件描述符处于打开状态，代表着控制台的默认文件描述符。