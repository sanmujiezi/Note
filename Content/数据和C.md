---
tags:
  - C语言
sda:
---



## 知识点
1. 单个字符 `A` 可以直接转换成对应的数字ASCII码

***

## 复习题

1．指出下面各种数据使用的合适数据类型（有些可使用多种数据类型）​：　　
a．East Simpleton的人口　　
b．DVD影碟的价格　　
c．本章出现次数最多的字母　　
d．本章出现次数最多的字母次数

> [!NOTE] Title
> a：unsign int
> b : float
> c : char
> d : unsign long

2．在什么情况下要用long类型的变量代替int类型的变量？


> [!NOTE] Title
> 在需要使用到32位的长整数时


3．使用哪些可移植的数据类型可以获得32位有符号整数？选择的理由是什么？


> [!NOTE] Title
> Int32_t 是2内嵌在标准库中的，在任何平台上都是32位有符号整数


4．指出下列常量的类型和含义（如果有的话）​：

　a．'\b'
　b．1066　　
　c．99.44　　
　d．0XAA　　
　e．2.0e30


> [!NOTE] Title
> a. int类型常量
> b. int 数值类型
> c. double 双精度小数
> d. 十六进制数字
> e. 长浮点数

6．写出下列常量在声明中使用的数据类型和在printf()中对应的转换说明：

> [!NOTE] Title
> 1. int %d
> 2. int %x
> 3. char %c
> 4. double %e
> 5. char %c
> 6. double %f
> 7. long %ld
> 8. float %f
> 9. float %a
> 10. unsigned int %#o
> 11. long double

9．假设ch是char类型的变量。分别使用转义序列、十进制值、八进制字符常量和十六进制字符常量把回车字符赋给ch（假设使用ASCII编码值）​。


> [!NOTE] Title
> `char ch = '\n'`
> `char ch = '\n'`
