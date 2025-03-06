---
tags:
  - 计算机网络
  - 线程阻塞解决
  - 多路复用Select
sda:
---



## 什么是多路复用Select
Select是通过Poll优化而来的方法，我们主要解决了Pol只对一个端口进行循环遍历的低效方法，采用的同时遍历检测所有端口的方法，将输入的端口做排查，返回一个有反馈的端口Socket数组。
这样我们就能快速的对做出了反应的客户端进行处理。
- Select 方法
```
public static void Select( 
IList checkRead,
IList check Write,
IList checkError, int microSeconds 
)
```

![[Pasted image 20250306012545.png]]

- 对输入的Socket组的处理流程
![[Pasted image 20250306012628.png]]

***

## Select服务端
主要通过监听Socket（Listenfd）和哭护短Socket（遍历clients列表）添加到待检测Socket可读状态的列表checkList中。
![[Pasted image 20250306012935.png]]![[Pasted image 20250306012948.png]]
- ReadListenfd方法 是用来回应发出链接请求的客户端的方法
- ReadClientfd方法 是用来将Client客户端发送的小时广播给其他客户端的方法

### 客户端代码
只要Select这个不阻塞的方法返回了一个可读的数据，就会执行foreach中的判断，之前不执行是因为checkRead中没有返回任何可读的数据
![[Pasted image 20250306013356.png]]

## 总结
由于程序在Update中不停地检测数据，性能较差。商业上为了做到性能上的极致，大多使用异步（或使用多线程模拟异步程序）​。本书将会使用异步客户端、Select服务端演示程序。
