# 关键知识点
自定义函数在C语言中使用时是需要声明的。
例如
```
#include <stdio.h>
void Start();

int main(void)
{
    int feet, fathoms;

    fathoms = 2;
    feet = 6 * fathoms;
    printf("There are %d feet in %d fathoms!\n", feet, fathoms);
    printf("Yes, I said %d feet!\n", 6 * fathoms);
    Start();

    return 0;
}

void Start() {
    printf("打印1");
    printf("Yes, I said feet");
}
```
# 提问
- C语言的基本模块是什么？
>[!note] 回答
>函数，c语言在mian函数内执行自定义的函数，将不同的功能拆分成不同的函数组

- 什么是语法错误？写出一个英语例子和C语言例子。

>[!note] 回答
> 语法错误就是类似中文中的 ： 我吃想饭
> C语言中 ： int n ,int b;

- 什么是语义错误？写出一个英语例子和C语言例子。

>[!note] 回答
> 比如我想表达 我很冷  但是我却说：我好热，这是语义的表达错误
> C语言中 ： `int cubed = n*n` 我想表达n的立方，但是内容确实平方

- Indiana Sloth编写了下面的程序，并征求你的意见。请帮助他评定。
~~~
include studio.h 
int main{void} /* 该程序打印一年有多少周 /* 
(
int s s := 56; printf(There are s weeks in a year.);
return 0;
~~~

纠正后：
~~~
#include <studio.h> 
int main() /* 该程序打印一年有多少周 /* 
{
int s = 56; 
printf("There are %d weeks in a year.",s);
return 0;
}
~~~

- 在main、int、function、char、=中，哪些是C语言的关键字？

>[!note] 回答
> int char

- 如何以下面的格式输出变量words和lines的值（这里，3020和350代表两个变量的值）​？

>[!note] 回答
>`printf("There were %d words and %d lines",3020,350);`

