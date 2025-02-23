---
tags:
  - CSharp基础
  - Task
  - 多线程
  - 线程池
sda:
---



## Task
Task是C#中的一个集合了线程池的库，他默认使用了线程池的结构来完成线程的管理
### 线程池
线程池和对象池的概念类似，都是为了避免不必要的内存和性能浪费，将用过的线程统一管理起来，当前一个任务使用完之后放回池子，等待下一个任务。

#### 启动线程池
##### 方法1
直接通过Run启动
```
Task.Run(() => 
{
    Console.WriteLine("任务在线程池线程中执行");
});
```

##### 方法2
通过工程类来创建，这会开启一个新的线程，这个线程不在Task的线程池内
```
Task.Factory.StartNew(() => 
{
    Console.WriteLine("任务启动");
}, TaskCreationOptions.LongRunning); // 指定长时间运行的任务
```

#### 方法3

通过 `Task` 构造函数创建任务后，需要手动调用 `Start` 方法启动任务。
```
var task = new Task(() => 
{
    Console.WriteLine("任务启动");
});
task.Start();
```

***

### 等待任务完成
#### 使用Wait()
```
var task = new Task(()=>
{
	Thread.Sleep(1000);
	Console.WriteLine("任务完成");
});
task.Wait();  //这个方法对阻塞执行它的线程
Console.WriteLine("主线程继续执行");

```

#### 使用 **`aysnc / await`**
在外部调用 方法 `DoWorkAsync` 时并不会阻塞调用线程
在await 后的 Task 任务完成后会自动执行后面的代码
```
aysnc Task DoWorkAsync(){
	await Task.Run(() => 
    {
        Thread.Sleep(1000);
        Console.WriteLine("任务完成");
    });
    Console.WriteLine("异步方法继续执行");
}
```


> [!Warning] 提示
> `await` 只能在 异步方法`aysnc`中使用
> `await` 后只能跟一个异步的Task任务

***

### 任务组合
#### **使用 `Task.WhenAll`**

等待多个任务全部完成。
```
Task task1 = Task.Run(() => Thread.Sleep(1000));
Task task2 = Task.Run(() => Thread.Sleep(2000));

await Task.WhenAll(task1, task2);
Console.WriteLine("所有任务完成");
```
#### **使用 `Task.WhenAny`**

等待多个任务中的任意一个完成。
```
Task task1 = Task.Run(() => Thread.Sleep(1000));
Task task2 = Task.Run(() => Thread.Sleep(2000));

Task completedTask = await Task.WhenAny(task1, task2);
Console.WriteLine("有一个任务完成了");
```
***

### 任务延续
#### **使用 `ContinueWith`**

在一个任务完成后，执行另一个任务。
```
Task task = Task.Run(() => 
{
    Console.WriteLine("第一个任务");
});
task.ContinueWith(t => 
{
    Console.WriteLine("第一个任务完成，执行延续任务");
});
```

***

### **异常处理**

#### **捕获任务异常**

使用 `try-catch` 捕获任务抛出的异常。
```
Task task = Task.Run(() => 
{
    throw new Exception("任务出错");
});

try
{
    task.Wait();
}
catch (AggregateException ex)
{
    Console.WriteLine($"捕获异常：{ex.InnerException.Message}");
}
```

***

### **实际需求案例**

#### **案例 1：并行下载多个文件**

```
async Task DownloadFilesAsync(string[] urls)
{
    var tasks = urls.Select(url => DownloadFileAsync(url)).ToArray();
    await Task.WhenAll(tasks);
    Console.WriteLine("所有文件下载完成");
}

async Task DownloadFileAsync(string url)
{
    using (var client = new HttpClient())
    {
        string content = await client.GetStringAsync(url);
        Console.WriteLine($"下载完成：{url}，长度：{content.Length}");
    }
}
```

#### **案例 2：计算密集型任务的并行执行**

```

Task<int> CalculateAsync(int x, int y)
{
    return Task.Run(() => 
    {
        Thread.Sleep(1000); // 模拟计算
        return x + y;
    });
}

async Task RunCalculationsAsync()
{
    Task<int> task1 = CalculateAsync(1, 2);
    Task<int> task2 = CalculateAsync(3, 4);

    int[] results = await Task.WhenAll(task1, task2);
    Console.WriteLine($"计算结果：{results[0]}, {results[1]}");
}
```
#### **案例 3：超时控制**
```
async Task<string> FetchDataWithTimeoutAsync(string url, int timeoutMilliseconds)
{
    var task = new HttpClient().GetStringAsync(url);
    var timeoutTask = Task.Delay(timeoutMilliseconds);

    var completedTask = await Task.WhenAny(task, timeoutTask);
    if (completedTask == timeoutTask)
    {
        throw new TimeoutException("请求超时");
    }
    return await task;
}
```
