
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

### 为什么fork()不和exec绑定?
>这是因为系统内核做的优化，因为copy-on-write机制，实际上fork()之后并不会立马真的为子进程分配物理空间，指挥分配虚拟空间，只有当一方用到修改变量的时候才会去给子进程分配，这样可以达到省内存的目的。

## 1.2 I/O和文件描述符
>&emsp;&emsp;文件描述符是一个小的整数，可以看作一个文件对象被内核管理。文件描述符可以通过<b>打开一个文件、目录、设备、创建管道或者从已经有的文件描述符复制而来</b>。为了方便可以把文件描述符抽象看作一个 <b>文件</b>。  
&emsp;&emsp;上述几个创建文件描述符的方法都抽象实现了接口，使他们使用起来像字节流。
&emsp;&emsp;每一个进程都有自己独立的文件描述符空间，全部0开始。一般情况下 0代表标准输入，1代表标准输出， 2代表标准错误。shell保证始终至少有三个文件描述符处于打开状态，代表着控制台的默认文件描述符。
 &emsp; &emsp;两个文件描述符会共享一个缓冲区，如果其中一个由另外一个dup而来或者fork()而来。否则即使两个文件描述符指向的是同一个文件也不会共享。
 ```bash
 ls existing-file non-existing-file > tmp1 2>&1  
 把这条命令给文件描述符2  2由1复制而来。 existing-file的信息和 non-existing-file的错误信息都会再tmp1中显示。 xv6不支持IO重定向 但是可以通过这个方式来达到想要的效果
 ```

## 1.3 pipes
>pipe是进程之间通信的方式。

## 1.4 File system
&emsp;&emsp;xv6文件系统提供 数据文件，包括未解码的字节型数组和目录。目录包含指向其他目录和数据文件的映射。目录可以看作一棵树，从root目录开始。
&emsp;&emsp;Mknod 会创建一个特殊的文件，这个文件指向一个设备。和设备文件有关系的有两个参数--主要和次要设备文件，这两个参数会唯一的假别一个内核设备。当一个进程打开了一个设备文件之后，内核会将<b>read和write</b>系统调用传给内核设备接口而不是传给文件系统。
&emsp;&emsp;一个文件的名字和本身不同(翻译存疑 A file’s name is distinct from the file)；the same底层文件成为一个inode,这个inode可以由很多名称，这些名称成为links.每一个link对应一个条目，这些条目存放在一个目录中。条目包括文件名、到inode的映射。每个Inode都有一个 <i>metadata</i>描述一个文件，包括文件的类型（普通文件还是dir还是device）,文件长度， 文件在磁盘中的content, 这个文件的links数量。
&emsp;&emsp;fstat系统调用会检索一个文件描述符指向的inode的信息，并把这些信息放在一个 struct stat中。
```c
#define T_DIR 1 // Directory
#define T_FILE 2 // File
#define T_DEVICE 3 // Device
struct stat {
    int dev; // File system’s disk device
    uint ino; // Inode number
    short type; // Type of file
    short nlink; // Number of links to file
    uint64 size; // Size of file in bytes
};
```
&emsp;&emsp;<i>link</i>系统调用对一个已经存在的文件起一个别名，这个别名和源文件一样指向同一个inode.
```c
oepn("a", O_CREATE|O_WRONLY);
link("a", "b");
```
&emsp;&emsp;进行上述操作后，读写a和读写b本质上是一样的，对两个文件调用fstat返回的值也是一样的。

&emsp;&emsp;<i>unlink</i>系统调用会从文件系统取消一个文件的别名。当一个inode的nlink别称0的时候并且没有文件描述指向它的时候这inode的空间就会别释放。
```c
unlink("a");
```
&emsp;&emsp;
```c
fd = open(fd = open("/tmp/xyz", O_CREATE|O_RDWR);
unlink("/tmp/xyz");)
```
&emsp;&emsp;上述方式可以创建一个临时文件，当进程结束或者fd关闭之后就会被释放

## 2.1 普通介绍 掠过

## 2.2 User mode supervisor mode, and system calls  
&emsp;&emsp;我们希望应用和os之间有很好的独立性，防止应用出现问题时导致os崩溃。os应该有清理失败应用和继续运行其他程序的能力，为了达到这个目标，应用程序应该不能修改(甚至读取)os的数据,并且应用不能读取别的应用的内存空间。
&emsp;&emsp;CPU为硬件提供了很好的隔离性。RISC-V有CPU可以执行的模式:machine mode, suppervisor mode, user mode。Machine mode 可以修改计算机配置。Xv6会在machine mode下执行部分代码然后转到supervisor mode.
&emsp;&emsp;在supervisor mode下 CPU可以执行<i>privileged instructions</i>.exp: 准许和取消终端，读写文件地址,etc.如果一个app在user mode尝试去执行一个 priveileged instruction, CPU会拒绝执行。当一个软件在suervisor mode执行时可以执行 privileged instructions并且可以运行在 kernel space.运行在kernel space (or in suppervisor mode)被称为 <i>kernel</i>