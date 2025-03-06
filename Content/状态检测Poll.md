---
tags:
  - 计算机网络
  - poll
sda:
---



## 什么是Poll
Poll是为了解决在同步网络通信中的阻塞线程问题，他通过添加了一个条件判断（如果有可读的数据，或者说Socket的收发有动静）才去执行阻塞的函数。
主要的阻塞方法是Receive;

- Poll方法
`public bool Poll ( int microSeconds, SelectMode mode )`
![[Pasted image 20250306011714.png]]

***

## 实现原理
例如Receive方法：
### 客户端
只有在我们有数据可以读的时候才执行Recive
![[Pasted image 20250306011826.png]]![[Pasted image 20250306011833.png]]

### 服务端
注意在死循环中需要使用到Sleep来睡眠线程1毫秒，防止无限循环导致的程序卡死
![[Pasted image 20250306011925.png]]、
![[Pasted image 20250306011931.png]]

## 总结
尽管逻辑清晰，但Poll服务端的弊端也很明显，若没有收到客户端数据，服务端也一直在循环，浪费了CPU。Poll客户端也是同理，没有数据的时候还总在Update中检测数据，同样是一种浪费。从性能角度考虑，还有不小的改进空间。