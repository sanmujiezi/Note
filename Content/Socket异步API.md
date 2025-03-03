---
tags:
  - CSharp基础
  - 计算机网络
  - Socket
  - 异步加载
sda: 2025-03-02
---


## 异步API
### Connect
- IAsyncResult BeginConnect()
```
public IAsyncResult BeginConnect(
string host,    //主机名或者ip地址
int port,       //端口号
AsyncCallback requestCallback,  //回调函数
object state   //执行回调时传入的参数  IAsyncResult类
)
```
- void EndConnect()
```
public void EndConnect( IAsyncResult asyncResult )
```

- 使用
一般在使用BeginConnect的时候，会将EndConnect嵌入到前者的回调函数中
示例
```
public void ConnectCallback(IAsyncResult ar)  
{  
    try  
    {  
        Socket socket = (Socket)ar.AsyncState;  
        socket.EndConnect(ar);     //这里结束的
        Debug.Log("Socket Connect Success");  
        socket.BeginReceive(readBuff, 0, 1024, 0, ReceiveCallback, socket);  
    }  
    catch (SocketException e)  
    {  
        Debug.Log(e.Message);  
    }  
}  
```

### Receive
- IAsyncResult BeginReceive()
```
public IAsyncResult BeginReceive (
byte[] buffer,     //缓存容器
int offset,         //从index多少开始
int size,           //数据的长度
SocketFlags socketFlags,    //多数据的按位组合，这里填 0
AsyncCallback callback,   //回调函数
object state      //回调传入的参数
)
```
- int EndReceive()  
他会返回受到的消息的字节长度
```
public int EndReceive( IAsyncResult asyncResult )
```

- 使用
```
Socket socket = (Socket)ar.AsyncState;  
int count = socket.EndReceive(ar);  
recvStr = System.Text.Encoding.Default.GetString(readBuff, 0, count);  
socket.BeginReceive(readBuff, 0, 1024, 0, ReceiveCallback, socket); 
```

### Send
- IAsyncResult BeginSend
发送信息的参数与接收信息相同
```
public IAsyncResult BeginSend( byte[] buffer, 
int offset,
int size,
SocketFlags socketFlags,
AsyncCallback callback,
object state 
)
```

- int EndSend()
会返回发送消息的字节长度
```
public int EndSend ( IAsyncResult asyncResult )
```
- 使用
```
_socket.BeginSend(sendBytes, 0, sendBytes.Length, 0, SendCallback, _socket);


private void SendCallback(IAsyncResult ar)  
{  
    try  
    {  
        Socket socket = (Socket)ar.AsyncState;  
        int count =  socket.EndSend(ar);  
        Debug.Log("Socket Send Success" + count);  
    }  
    catch (SocketException e)  
    {  
        Debug.Log("Socket Send fail" + e.Message);  
    }  
}
```

### Accept
- IAsyncResult BeginAccept
```
public IAsyncResult BeginAccept(
AsyncCallback callback,         //回调函数
object state                    //回调参数
)
```
- Socket EndAccept
他会返回回调连接到服务器端口的Socket
```
public Socket EndAccept( IAsyncResult asyncResult )
```


## 客户端完整实例代码

```
private Socket _socket;  
public TMP_InputField inputField;  
public TextMeshProUGUI Text;  
  
private byte[] readBuff = new byte[1024];  
private string recvStr = "";
  
  
public void Connection()  
{  
    _socket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);  
    //_socket.Connect("127.0.0.1", 8888);  
    _socket.BeginConnect("127.0.0.1", 8888, ConnectCallback, _socket);  
    Debug.Log("正在连接");  
}  
  
public void ConnectCallback(IAsyncResult ar)  
{  
    try  
    {  
        Socket socket = (Socket)ar.AsyncState;  
        socket.EndConnect(ar);  
        Debug.Log("Socket Connect Success");  
        socket.BeginReceive(readBuff, 0, 1024, 0, ReceiveCallback, socket);  
    }  
    catch (SocketException e)  
    {  
        Debug.Log(e.Message);  
    }  
}  
  
private void ReceiveCallback(IAsyncResult ar)  
{  
    try  
    {  
        Socket socket = (Socket)ar.AsyncState;  
        int count = socket.EndReceive(ar);  
        recvStr = System.Text.Encoding.Default.GetString(readBuff, 0, count);  
  
        socket.BeginReceive(readBuff, 0, 1024, 0, ReceiveCallback, socket);  
    }  
    catch (SocketException e)  
    {  
        Debug.Log(e.Message);  
    }  
}  
  
public void Send()  
{  
    string sendStr = inputField.text;  
    byte[] sendBytes = System.Text.Encoding.UTF8.GetBytes(sendStr);  
    _socket.BeginSend(sendBytes, 0, sendBytes.Length, 0, SendCallback, _socket);  
}  
  
private void SendCallback(IAsyncResult ar)  
{  
    try  
    {  
        Socket socket = (Socket)ar.AsyncState;  
        int count =  socket.EndSend(ar);  
        Debug.Log("Socket Send Success" + count);  
    }  
    catch (SocketException e)  
    {  
        Debug.Log("Socket Send fail" + e.Message);  
    }  
}  
  
private void Update()  
{  
    Text.text = recvStr;  
}

```

## 服务端完整实例代码
```
using System.Net;
using System.Net.Sockets;

namespace SocketSeverTest
{

    class ClientState
    {
        public Socket socket;
        public byte[] readBuff = new byte[1024];
    }
    internal class Program
    {
        static Socket listenfd;
        static Dictionary<Socket, ClientState> clients = new Dictionary<Socket, ClientState>();

        static void Main(string[] args)
        {

            Console.WriteLine("Hello, World!");

            listenfd = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);

            IPAddress ipAdr = IPAddress.Parse("127.0.0.1");
            IPEndPoint ipEp = new IPEndPoint(ipAdr, 8888);

            listenfd.Bind(ipEp);
            listenfd.Listen(0);

            Console.WriteLine("[服务器]启动成功");

            listenfd.BeginAccept(AcceptCallback, listenfd);
            Console.ReadLine();
        }

        public static void AcceptCallback(IAsyncResult ar)
        {
            try
            {
                Console.WriteLine($"[服务器]Accpet");
                Socket listenfd = (Socket)ar.AsyncState;
                Socket clientfd = listenfd.EndAccept(ar);

                ClientState state = new ClientState();
                state.socket = clientfd;
                clients.Add(clientfd, state);
                clientfd.BeginReceive(state.readBuff,0,state.readBuff.Length,0,ReceiveCallback,state);

            }
            catch (SocketException e)
            {
                Console.WriteLine($"客户端适配异常 {e.Message}");
            }
        }

        public static void ReceiveCallback(IAsyncResult ar)
        {
            try
            {
                ClientState state = (ClientState) ar.AsyncState;
                Socket clientfd = state.socket;
                int count = clientfd.EndReceive(ar);
                if (count == 0)
                {
                    clientfd.Close();
                    clients.Remove(clientfd);
                    Console.WriteLine("Socket close");
                    return;
                }

                string recvStr = System.Text.Encoding.Default.GetString(state.readBuff,0,count);
                byte[] sendBytes = System.Text.Encoding.UTF8.GetBytes(recvStr);
                clientfd.Send(sendBytes);
                clientfd.BeginReceive(state.readBuff,0,state.readBuff.Length,0,ReceiveCallback,state);
            }
            catch (SocketException e)
            {
                Console.WriteLine($"Socket 服务回调异常 {e.Message}");
            }
        }
    }
}

```
