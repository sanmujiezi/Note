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
- `isDone`  判断异步操作是否已经完成
- `progress` 返回一个加载进度的浮点值
- `priority` 返回一个整数值的进度（0-1）
- `allowSceneActivation`  如果加载的是场景，判断是否允许场景被激活
- `Action completed` 一个事件，可以通过它来完成异步操作后的自定义方法

## Resources加载与卸载资源
- 加载过的资源不被被重复加载
- 但是第二次加载时发现缓存存在资源，直接取出，这个过程浪费性能。
- `Resources.UnloadAsset(rq)`可以释放资源
- `Resources.UnloadUnusedAssets()` 可以释放未使用的资源
- `GC.Collect()` 执行GC清理内存垃圾，但是需要先将资源指向null
> [!NOTE] Prefab资源无法被卸载
> 加载的Prefab资源无法被卸载

