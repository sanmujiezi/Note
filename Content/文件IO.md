tag： #CSharp基础 #文件IO 


## 目的以及作用

文件操作模型System.IO这一套类集合都是用于对文件进行格式各样方式的处理的。


***

## 文件

文件中有多种文件类型
文件类型的存储结构可以分为，顺序文件和随机文件

***
#### 文件存取结构类型
##### 顺序文件

循序文件主要使用ASCII码，所有的字符存储的在存储器中，是按照文件按照顺序来存放的

|     | 内容1 | 内容2 | 内容3 |     |
| --- | --- | --- | --- | --- |

##### 随机文件

在存储上，将所有的内容拆散，并添加一个记录号来标识文件内容的顺序。

|     | 1.内容1 |     | 2.内容2 | 3.内容3 |     |
| --- | ----- | --- | ----- | ----- | --- |

***

## System.IO类库

它是一个命名空间，空间中的类主要用于对文件进行一定程度的编辑。

他们中包括但不限于

| 类            |
| ------------ |
| BinaryReader |
| Directory    |
| File         |
| FilStream    |
| ...          |

***

### 文件编码

文件编码也叫字符编码，常用的是Unicode
编码就是将一串Unicode编码转换为字节序列的过程
解码就是将字节序列转换成一串Unicode

***

### 文件流的个人理解

文件流就像你是一个拥有替身的钢链手指（JOJO），以及可对你想要操作的实体上开一个拉链（也就是  流  ），你就可以利用这个”流“做操作，当然使用完之后需要拉上这个拉链，不然文件会出现不可预计的问题。

***

### 文件和文件夹的操作

文件主的操作要通过[File类]()
文件夹的操作主要通过[Directory类](https://learn.microsoft.com/zh-cn/dotnet/api/system.io.directory?view=net-8.0)来进行操作


> [!tip] 提示
> Directory中的Exists只用于判断文件夹是否存在
> 如果需要判断文件那么需要使用File类中的Exists


***
### FileStream类

文件流类，有了这个类便可以对文件进行读写等操作。

在fileStream类中可以对文件进行操作，决定如何打开类，就像你决定打开门的方式一样，通过[FileMode常数](https://learn.microsoft.com/zh-cn/dotnet/api/system.io.filemode?view=net-8.0)可以设置。

[FileStream类](https://learn.microsoft.com/zh-cn/dotnet/api/system.io.filestream?view=net-8.0)就是可以开”拉链“的类，一般与[StreamReader](https://learn.microsoft.com/zh-cn/dotnet/api/system.io.streamreader?view=net-8.0)和[StreamWriter](https://learn.microsoft.com/zh-cn/dotnet/api/system.io.streamwriter?view=net-8.0)同时使用
```
///读取数据
string path = "路径..";
string mystr = "";

FileStream fs = File.OpenRead(path);
StreamReader sr = new StreamReader(fs,Encoding.Default);
fs.Seek(0,SeekOrigin.Begin);   //调整文件流指针位置
while(sr.Peek() > -1){
	mystr = mystr + sr.ReadLine() + "\r\n";
}
sr.Close();
fs.Close();

///写入数据
string writeData = "123";

FileStream fs = File.OpenWrite(path);
StreamWriter sw = New StreamWriter(fs,Encoding.Default);
sw.WriteLine(writeData);
sw.Close();
fs.Close();
```