tag： #UGUI #优化  


## Canvas优化

Canvas优化主要是在大的Canvas中使用Canvas将一些动态的图像包裹起来分离开
这样可以减少mesh合并时的性能消耗，但是会增加DrawCall

	- 如果物体顺序穿插了
		- 图像有穿插就会调DrawCall
		- 无穿插则一次绘制
	- 如果顺序没有穿插则同类一次绘制，不同类在调用一次DrawCall
![[Pasted image 20241014222753.png]]

***

## DrawCall优化

可以通过图集的方式来降低DrawCall，在同一个Canvas下的图片如果在同一个图集中，则只消耗一次drawcall，如果两个图中间穿插了其他图，则会打断连续的drawcall

	- 图集就是将多个外部导入的sprite做成能够一次载入的“种类”，类似Text与Image等

***

## Mask问题

如果在ui中频繁使用mask也会怎加drawcall，所以应该尽量避免drawcall
