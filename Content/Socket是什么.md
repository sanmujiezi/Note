---
tags:
  - 计算机网络
  - Socket
sda:
---



## Socket概念
Socket是一个套接字，一个双向的客户端与服务器的连接称为一个Socket

***

## Socket类
- 构造函数
`new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);`
	- AddressFamily 
		 - InterNetwork ： IPV4协议
		 - InterNetwork6 ： IPV6协议
	- SoketType
		- Stream : 流式套接字协议需要连接 适用于 TCP
		- Dgram : 无连接协议 适用于 UDP
	- ProtocolType
		- TCP ：TCP协议
		- UDP ：UDP协议

### 服务端
- 绑定终结点 IPEndPoint类
`Bind( IPEndPoint类)`

```
Socket listenfd = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
IPAddress ipAdr = IPAddress.Parse("127.0.0.1");
IPEndPoint ipEp = new IPEndPoint(ipAdr, 8888);

listenfd.Bind(ipEp);
listenfd.Listen(0);
```

- 监听终结点
	- 传入最大限制连接数量，0为不限制
`listenfd.Listen(0);`

- 接受连接
	- 等待客户端连接后返回一个客户端的Socket类
`Accept()`

- 接收信息
	- 等待 收到后返回一个 byte[]
`Receive(二进制数据)`

- 发送信息
`Send(二进制数据)`

#### 服务端全代码
```
 Console.WriteLine("Hello, World!");

 Socket listenfd = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
 IPAddress ipAdr = IPAddress.Parse("127.0.0.1");
 IPEndPoint ipEp = new IPEndPoint(ipAdr, 8888);

 listenfd.Bind(ipEp);
 listenfd.Listen(0);

 Console.WriteLine("[服务器]启动成功");
 while (true)
 {
     Socket connfd = listenfd.Accept();
     Console.WriteLine("[服务器]Accept");
     
     byte[] readBuff = new byte[1024];
     int count = connfd.Receive(readBuff);
     string readStr = System.Text.Encoding.UTF8.GetString(readBuff, 0, count);
     Console.WriteLine($"[服务器接收] {readStr}");

     byte[] sendBytes = System.Text.Encoding.Default.GetBytes(readStr);
     connfd.Send(sendBytes);
 }
```


### 客户端
- 客户端全代码
	- 与服务端类似不在赘述

```
private Socket _socket;  
public TMP_InputField inputField;  
public TextMeshProUGUI Text;  
  
public void Connection()  
{  
    _socket = new Socket(AddressFamily.InterNetwork,SocketType.Stream,ProtocolType.Tcp);  
    _socket.Connect("127.0.0.1", 8888);  
}  
  
public void Send()  
{  
    string sendStr = inputField.text;  
    byte[] sendBytes = System.Text.Encoding.UTF8.GetBytes(sendStr);  
    _socket.Send(sendBytes);  
  
    byte[] readBuff = new byte[1024];  
    int count = _socket.Receive(readBuff);  
    string recvStr = System.Text.Encoding.Default.GetString(readBuff, 0, count);  
    Text.text = recvStr;  
    _socket.Close();  
}
```