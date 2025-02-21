---
tags:
  - unity基础
  - Resources资源加载
  - 同步加载
  - 异步加载
sda: 2025-02-21
---



## Resources是unity中特殊的文件夹
主要用于存储：
- Prefab
- texture
- audio
- video

***

## Resources中的API
同步加载
`Resources.Load()`  
`Resources.Load<T>()`  
`Resources.LoadAll()`  
`Resources.LoadAll<T>()`  
异步加载
`Resources.LoadAsync()`
`Resources.LoadAsync<T>()`


***

## 异步加载

当使用`Resources.LoadAsync`时，一般需要配合时间、委托与协程使用。
主要是为了在等待加载时不堵塞线程。
### 异步加载传入委托的方式
-  通过`ResourceRequest`中completed事件实现
```
ResourceRequest operation = Resources.LoadAsync<GameObject>("Prefab/Cube");  
operation.completed += LoadCube;
```
-  通过携程实现 
```
//在 main中调用
ResourcesAsyncManager.Instance.LoadAsync<GameObject>("Prefab/Cube", (obj) =>  
{  
    Debug.Log($"{obj.gameObject.name} 加载完成了");  
});

//实现
public void LoadAsync<T>(string path, UnityAction<T> callback) where T : Object  
{  
    IEnumerator ie = IELoadAsync<T>(path, callback);  
    StartCoroutine(ie);  
}  
  
private IEnumerator IELoadAsync<T>(string path, UnityAction<T> callback) where T: Object  
{  
    ResourceRequest rq = Resources.LoadAsync<T>(path);  
    yield return rq;  
    callback?.Invoke(rq.asset as T);  
}
```
- 在携程中也有多种方式可以判断 `ResourceRequest` 是否已经被执行完成了
	1. `yield return rq;`   rq执行完后会直接被抛回，然后执行后面的内容
	2.  `while(!rq.isDone){ yield return null; }`  在携程中使用循环判断，如果没有完成则在下一帧中继续判断
### ResourceRequest类
- 在`ResourceRequest.asset` 可以获取`Resources`异步加载出的结果
- 同时 它 继承 于 `AsyncOperation`

### AsyncOperation 类
常用的属性与方法
- `isDone` 
- `progress`
- `priority`
- `allowSceneActivation`
- 