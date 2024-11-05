tag： #打包 #安卓 #苹果 #SDK


[参考教程](https://www.bilibili.com/video/BV1Qt4y1a7aW?spm_id_from=333.788.videopod.episodes&vd_source=ef7a79a9d397bb3255e72c973e38ce01&p=6)
## 安卓打包

安卓打包主要在build Settings中进行参数设置
![[Pasted image 20241019222637.png]]
简单的过程不再赘述

### 遇到的问题
<br/>

**Q：在实际启动导出的安卓工程中遇到了使用android studio更新gradle十分缓慢的问题**

> [!NOTE]  A：
>可以在gradle.properties中设置好代理的地址以及端口，并填写计算机名称与密码可以更加快速的下载资源
> ```
> systemProp.http.proxyHost=127.0.0.1  
> systemProp.http.proxyPassword=200141  
> systemProp.http.proxyPort=7890  
> systemProp.http.proxyUser=TangYuan  
> systemProp.https.proxyHost=127.0.0.1  
> systemProp.https.proxyPassword=200141  
> systemProp.https.proxyPort=7890  
> systemProp.https.proxyUser=TangYuan
> ```
> 
> ***


**Q：如何手动替换gradle包**

> [!note] A：
> 在gradle官网下载好对应gradle plugins 的版本之后（下载all版本），将他解压出来
> 文件名大概叫 gradle-7.5-all
> 然后将它放到`D:\Android\Android Studio\gradle\gradle-7.5.1`中
> 并在android studio->settings->build,excution...->gradle->gradleProjects->distribution->Localinstallation  中找到上面存放的地址并保存即可
> ***

**Q：Android Studio导出Jar包**
> [!note] A：
> **方法1**：新建项目
> 在新建一个无Activety的项目后，将其改为Library类库
> 然后在java包中新建并编辑类
> 在运行中选择make project
> 然后根据以下目录找到jar包即可
> ![[Pasted image 20241019225236.png]]
> 
> **方法2**：
> 通过untiy导出的安卓包，在其中添加模块
> 然后重复上一个方法的操作即可
> ***

**Q：在unity中调用Java的问题**

> [!NOTE] A：
> 首先需要在Assets/Plugins/Android/ 中放入打包好的jar类
> 然后在脚本中通过AndroidJavaObject来调用
> ```
> //类的名称  包名 . 类名
> public AndroidJavaObject javaClass = new AndroidJavaObject("com.example.myapplication.Test")
> ```
> 
> - [AndroidJavaObject](https://docs.unity3d.com/2022.3/Documentation/ScriptReference/AndroidJavaObject.html)用于调用具体的实例
> - [AndroidJava**Class**](https://docs.unity3d.com/2022.3/Documentation/ScriptReference/AndroidJavaClass.html)用于调用static变量或方法
> ***
> 

**Q：MUMU模拟器连接Android Studio Shell输出debug信息**

> [!NOTE] A：
> 在MUMU模拟器的更目录下进入CMD控制台
> 输入 `adb.exe connect 127.0.0.1:7555`
> 连接成功就可以了
> 
> debug信息通过以下方法打印
> ```
> import android.util.Log;  
> Log.d(LOG,"GetLog" +LOG);
> ```


**Q：Java调用untiy中的方法**
> [!note] A：
> 首先需要在untiy导入android支持的文件夹内找到对应打包方式的Jar包
> 例如：
> `D:\UnityHub\UnitySoft\2022.3.49f1\Editor\Data\PlaybackEngines\AndroidPlayer\Variations\il2cpp\Release\Classes\classes.jar`
> 然后将jar包存放到安卓项目文件下的libs中，并设置为类库（选中右键Add as library）
> ![[Pasted image 20241019233158.png]]
> ![[Pasted image 20241019233320.png]]
> ***



***

## 苹果打包
内容2