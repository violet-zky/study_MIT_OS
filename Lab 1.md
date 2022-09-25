## Lab 1

### Q1

[XV6实验(1) Lab util_hang_AI427的博客-CSDN博客_xv6的primes实验](https://blog.csdn.net/qq_32752467/article/details/117198261)

```shell
cat echo.c
#include "kernel/types.h"
#include "kernel/stat.h"
#include "user/user.h"

int
main(int argc, char *argv[])
{
  int i;

  for(i = 1; i < argc; i++){
    write(1, argv[i], strlen(argv[i]));
    if(i + 1 < argc){
      write(1, " ", 1);
    } else {
      write(1, "\n", 1);
    }
  }
  exit(0);
}
```

```shell
cat grep.c
// Simple grep.  Only supports ^ . * $ operators.

#include "kernel/types.h"
#include "kernel/stat.h"
#include "user/user.h"

char buf[1024];
int match(char*, char*);

void
grep(char *pattern, int fd)
{
  int n, m;
  char *p, *q;

  m = 0;
  while((n = read(fd, buf+m, sizeof(buf)-m-1)) > 0){
    m += n;
    buf[m] = '\0';
    p = buf;
    while((q = strchr(p, '\n')) != 0){
      *q = 0;
      if(match(pattern, p)){
        *q = '\n';
        write(1, p, q+1 - p);
      }
      p = q+1;
    }
    if(m > 0){
      m -= p - buf;
      memmove(buf, p, m);
    }
  }
}

int
main(int argc, char *argv[])
{
  int fd, i;
  char *pattern;

  if(argc <= 1){
    fprintf(2, "usage: grep pattern [file ...]\n");
    exit(1);
  }
  pattern = argv[1];

  if(argc <= 2){
    grep(pattern, 0);
    exit(0);
  }

  for(i = 2; i < argc; i++){
    if((fd = open(argv[i], 0)) < 0){
      printf("grep: cannot open %s\n", argv[i]);
      exit(1);
    }
    grep(pattern, fd);
    close(fd);
  }
  exit(0);
}

// Regexp matcher from Kernighan & Pike,
// The Practice of Programming, Chapter 9.

int matchhere(char*, char*);
int matchstar(int, char*, char*);

int
match(char *re, char *text)
{
  if(re[0] == '^')
    return matchhere(re+1, text);
  do{  // must look at empty string
    if(matchhere(re, text))
      return 1;
  }while(*text++ != '\0');
  return 0;
}

// matchhere: search for re at beginning of text
int matchhere(char *re, char *text)
{
  if(re[0] == '\0')
    return 1;
  if(re[1] == '*')
    return matchstar(re[0], re+2, text);
  if(re[0] == '$' && re[1] == '\0')
    return *text == '\0';
  if(*text!='\0' && (re[0]=='.' || re[0]==*text))
    return matchhere(re+1, text+1);
  return 0;
}

// matchstar: search for c*re at beginning of text
int matchstar(int c, char *re, char *text)
{
  do{  // a * matches zero or more instances
    if(matchhere(re, text))
      return 1;
  }while(*text!='\0' && (*text++==c || c=='.'));
  return 0;
}
```

```shell
cat rm.c
#include "kernel/types.h"
#include "kernel/stat.h"
#include "user/user.h"

int
main(int argc, char *argv[])
{
  int i;

  if(argc < 2){
    fprintf(2, "Usage: rm files...\n");
    exit(1);
  }

  for(i = 1; i < argc; i++){
    if(unlink(argv[i]) < 0){
      fprintf(2, "rm: %s failed to delete\n", argv[i]);
      break;
    }
  }

  exit(0);
}
```

sleep.c

```shell
#include "kernel/types.h"
#include "kernel/stat.h"
#include "user/user.h"

int
main(int argc, char *argv[])
{
  if(argc != 2){
    fprintf(2, "error\n");
    exit(1);
  }
  int time = atoi(argv[1]);
  sleep(time);
  printf("(nothing happens for a little while)\n");
  exit(0);
}
```

初学linux C库，能见到它的原型如下：  
int fprintf(FILE*stream,const char *format,...)  
 

查程序例时，见到如下调用：  
fprint(stderr,"Cannot open output file.\n");

名称        描述              例子

1. stdin     标准输入         键盘
2. stdout     标准输出          屏幕
3. stderr    标准错误          屏幕
4. stdprn     标准打印机       LPT1端口
5. stdaux     标准串行设备     COM1端口

在linux中:

0:与进程的标准输入关联; STDIN_FILENO

1:与标准输出关联; STDUOT_FILENO

2:与标准错误关联; STDERR_FIENO

exit(1)表示进程正常退出. 返回 1;

exit(0)表示进程非正常退出. 返回 0.

//int main( int argc , char *argv[] , char *envp[] ) 

argc是[命令行](https://so.csdn.net/so/search?q=%E5%91%BD%E4%BB%A4%E8%A1%8C&spm=1001.2101.3001.7020 "命令行")总的参数个数 

argv[]是argc个参数，其中第0个参数是程序的全名，以后的参数   
命令行后面跟的用户输入的参数

char *argv[]

这个用来取得你所输入的参数

D:\tc2>test

这个时候，argc的值是1，argv[0]的值是 “test”

D:\tc2>test myarg1 myarg2

这个时候，argc的值是3，argc[0]的值是”test”，argc[1]的值是”myarg1”，argc[2]的值是”myarg2”。

![](C:\Users\zky666\AppData\Roaming\marktext\images\2022-09-24-23-09-23-image.png)

报错并修改

![](C:\Users\zky666\AppData\Roaming\marktext\images\2022-09-24-23-11-50-image.png)

有了

![](C:\Users\zky666\AppData\Roaming\marktext\images\2022-09-24-23-12-29-image.png)

成功

![](C:\Users\zky666\AppData\Roaming\marktext\images\2022-09-24-23-15-29-image.png)

测试成功

![](C:\Users\zky666\AppData\Roaming\marktext\images\2022-09-24-23-17-05-image.png)
