---
tags:
  - UIToolkit
  - 编辑器拓展
sda:
---



# UIToolkit Builder界面
## 显示编辑器组件
勾选这个选项就可以显示更多的编辑器模式下使用的组件
![[Pasted image 20250401185054.png]]

## VisualElement元素的布局
![[Pasted image 20250401185238.png]]

- Postion选项可以调整布局的方式


		Relative:相对位置 ，会根据父节点的位置来摆放
		Absoulute: 绝对位置，通过调整参数来控制整个矩形的大小与位置。

- Flex 折叠选项


		Shrink：当窗口小于组件时，它是否被缩小 设置为1为启用
		Grow: 当父对象大于子对象时，填满剩余的空间 设置为1为启用
		Direction：方向，指的是容器内子元素的排列方式
		Wrap：换行，可以设置子元素超过长度或者高度时如何换行

***

# Scripts
## 命名空间
UItoolkit的API命名空间为Unity.UIElement

## 手动加载UXML
在手动加载编辑器的主要场景时，如果希望这个容器全覆盖，需要手动设置他的Flex中的Grow值为1

> [!NOTE] 提示
>  root.style.flexGrow = 1; 

~~~
[MenuItem("工具/UI Toolkit/UIToolkitTest", false, 0)]  
public static void ShowExample()  
{  
    EditorMainWindow wnd = GetWindow<EditorMainWindow>();  
    wnd.titleContent = new GUIContent("UIToolkitTest");  
}  
  
public void CreateGUI()  
{  
    var _rootVisualTreeAsset =  
        AssetDatabase.LoadAssetAtPath<VisualTreeAsset>(PathDefine.EditorMainUxml);  
    rootVisualElement.styleSheets.Add(  
        AssetDatabase.LoadAssetAtPath<StyleSheet>(PathDefine.EditorMainUss));  
    root = _rootVisualTreeAsset.Instantiate();  
    rootVisualElement.Add(root);  
    root.style.flexGrow = 1;  
  
    editorViewModel = new EditorViewModel(root,null);  
}
~~~

## 编辑器拓展的节点加载逻辑
- 在上面的代码中，我是通过路径加载的UXML文件和USS，然后把USS挂在到了这个窗口的rootVisualElement上。
- 再通过实例化刚才创建UXML的界面，并把他作为我们真正的根节点，然后把他添加到我们的RootVisualElement中。
- 我们的窗口中的可视化元素本身就是个树形结构，可以自由添加可视化元素。

## ListView的代码控制
~~~
Func<VisualElement> makeItem = () => item.Instantiate();  
Action<VisualElement, int> bindItem = (item, index) => BindingGirlList(item, m_girlLists[index]);
girlList.makeItem = makeItem;  
girlList.bindItem = bindItem;  
girlList.itemsSource = m_girlLists;  
girlList.selectionType = SelectionType.Single;  
  
girlList.selectionChanged += (e) =>  
{  
    foreach (var VARIABLE in e)  
    {  
        if (VARIABLE is GirlInfoModel girlInfo)  
        {  
            //Debug.Log(girlInfo.id);  
            var _parent = parent as ISelectedGirl;  
            if (_parent != null)  
            {  
                _parent.OnSelectedGirl(girlInfo);  
            }  
        }  
    }  
};
~~~

- makeItem 是我们的UI界面组件
- bindItem 是我们把数据传入组件绑定的委托方法
- itemsSource 是我们需要传入的数据列表，它会按照列表的顺序实例化并绑定数据
- selectionType 可以设置选择列表中元素的方式，允许多选或只能单选
- selectionChanged 这个事件需要传入一个委托方法，事件会返回一个之前我们数据源列表中的数据，这样我就可以获得选项切换时，用户选择了哪个数据了。
- listView.itemsChosen 这是个双击触发的事件，使用方法和选择事件一样。

## DropDownField
```
var choises = new List<string>()  
{  
    DetailState.ChatImage.ToString(),  
    DetailState.GirlImage.ToString(),  
    DetailState.EdiorBoard.ToString(),  
    DetailState.CompairBoard.ToString()  
};  
selectedState.choices = choises;  
selectedState.value = choises[0];  
  
selectedState.RegisterCallback<ChangeEvent<string>>((evt) =>  
{  
    DebugLogger.Instance.Log(this, $"Enum change {evt.newValue}");  
    selectedState.value = evt.newValue;  
});  

```

- choices  选项列表，通过直接赋值一个列表来调整参数
- value 当前选择的列表参数
- `RegisterCallback<ChangeEvent<string>>` 这是一个注册事件回调，触发时会返回点击的值，再将值复制给value 实现选项的变更

## ObjectField
```
girlImage.SetEnabled(false);  
boardImage.SetEnabled(false);  
chatImage.SetEnabled(false);  
boardPrefab.SetEnabled(false);
```
- 可以将ObjectField设置为不可用，这样用户就不能更改信息了
- 但是需要在界面刷新后在执行，不然会无法刷新数据