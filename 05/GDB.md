# GDB
## Quivk Start
1. 指令
- gcc -g./a.out
- run r(简写)运行指令
- quit 退出gdb模式
- list 查看源代码
- break b 打断点
    函数的地方 打断点
    在第几行 打断点
- info b 查看断电的情况
- man gdb 查看gdb的目录

```
#include <stdio.h>
int main (){
    int arr[4]={1,2,3,4};
    for (int i=0;i<4;i++){
        printf("%d\n",arr [i]);
        }
        return 0;
}
```
- run [args]：运行程序，可带参数。
- continue：从断点继续运行程序。
- next：单步执行，跳过函数内部。
- step：进入函数内部单步执行。
- finish：执行完当前函数，返回到调用处。
- until：运行到当前循环的下一次迭代。
- kill：终止正在运行的程序。

2. 断点设置与管理
- break <location>：在指定位置设置断点，如`break main`或`break file.c:10`。
- break <function>：在函数入口处设置断点。
- break *<address>：在指定地址设置断点。
- delete <breakpoint>：删除指定编号的断点。
- delete：删除所有断点。
- info breakpoints：查看所有断点信息。

3. 查看程序状态
- list：查看当前代码位置附近的源代码。
- where 或 backtrace：查看调用栈信息。
- info locals：查看当前函数中的局部变量。
- info args：查看当前函数的参数。
- print <variable>：打印变量的值。
- watch <variable>：设置变量监视，当变量值改变时暂停程序。