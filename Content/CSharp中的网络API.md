---
tags:
  - 计算机网络
  - CSharp基础
  - API
sda:
---



## IPAddress 类
这个类是一个用于创建和记录IP地址的类
- 创建IPAddress类
`IPAddress ip1 = IPAddress.Parse("192.168.100.2")`


***

## IPEndPoint 类
用于表示网络的终结点，它包含了IP地址与端口（例如8080端口）
`IPAddress ip1 = IPAddress.Parse("192.168.100.2")`
`IPEndPoint ipend1 = new IPEndPoint(ip1, 8080)`


***
## IPHostEntry 类
用于表示 主机与其IP地址的关系，在通过域名获取到了某网址的IPHostEntry类后，就可以通过它查看IP地址。
```
private async void GetHostEntry()  
{  
    Task<IPHostEntry> task = Dns.GetHostEntryAsync("www.baidu.com");  
    await task;  
    foreach (var VARIABLE in task.Result.AddressList)    
    {  
        Debug.Log(VARIABLE);  
    }  
}
```

### 重要属性

| 属性名         | 返回值         | 作用                                  |
| ----------- | ----------- | ----------------------------------- |
| AddressList | IPAddress[] | 可以得到与这个主机有联系的其他**IP地址**             |
| Aliases     | String[]    | 它返回一个字符串数组，表示与主机名关联的别名，但并不是所有主机都有别名 |
| HostName    | string      | 返回主机名                               |


***

## DNS 类
DNS类可以通过域名解析出对应的`IPHostEntry` 类
- 同步获取
`Dns.GetHostAddresses("www.baidu.com")`
- 异步获取
`Task<IPHostEntry> task = Dns.GetHostEntryAsync("www.baidu.com");`