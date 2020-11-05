### 判断文件是否存在  

>
>#inlcude<unistd.h>
func: acess(const char * pathname, int mode)
pathname: 要判断的文件或者目录名
mode : 要检测的操作模式
return: 成功返回0 否则返回1

mode|含义
-|-
R_OK|测试读许可权
W_OK|测试写许可权
X_OK|测试执行许可权
F_OK|测试文件是否存在



```c++
    //编写一个函数返回这个文件的读写权限 返回的权限是当前用户的对这个文件的拥有权
   #include<stdio.h>
   #include<stdlib.h>
   #include<unistd.h>
   #include<string.h>
   #include<sys/types.h>
   #include<sys/stat.h>
   int func(char *f)  //filename
   {
       int mode=0;
      if(access(f, F_OK)==-1)
              return -1;
      if(access(f, W_OK)!=-1)
              mode+=2;
      if(access(f,R_OK)!=-1)
              mode+=4;
      if(access(f,X_OK)!=-1)
              mode+=1;
      return mode;
   }
   int main()
   {
      char c[]="tmp.txt";
      printf("%d",func(c));
   }    
```