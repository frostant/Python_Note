# Detectron2 Note

[TOC]

# 工具

##  [tmux.md](tmux.md) 



# 代码

# import multiprocessing as mp #ship

用于并行部分的模块

```
mp.set_start_method("spawn",force=True)
# 
```



# [import argparse](./argparse.md)

基本总结完成



# import logging #ship

用于显示日志的模块



# checkpoint

checkpoint部分的知识好像被封装到fvcore里面



## Augmentation 数据增强

位置 detectron2/data/transforms/augmentation.py

定义 数据增强是策略来生成 或者说 transform 一般用来数据预处理

##      input_args: Optional[Tuple[str]] = None ???



# def run_on_image

## 分割类别

### thing和stuff的区别

有固定的形状称为things, 比如 人,车,路灯   可数的 objects

没有固定形状成为stuff, 比如天空,草地  相同或相似纹理或材料的不规则区域

### sem_seg 语义分割Semantic Segmentation

对图片的每个像素都分类, 关注的是不同类之间的区别, 把所有相同的things归为一类, 把每种stuff归为一类, 

### instance 实例分割

对于每个个体(object instance) 分割, 关注同一类中不同个体的区别, 而不关注stuff. 需要将不同的个体区分开

### panoptic_seg 全景分割

新的任务场景, 全景分割.  对视野内所有物体进行描述”including everything visible in one view”

图片内的每个像素都必须分配 semantic label 和 instance id. 

相同 label 和相同 id 的像素属于相同 object；忽略 stuff labels 的 instance id. 



![这里写图片描述](detectron2_note.assets/20180518220442248)



# 预测函数



# 显示预测结果的函数

## draw_panoptic_seg_predictions



## draw_sem_seg



## draw_instance_predictions



# python基础知识

## \_\_call\_\_ 调用  class作为函数使用 

```
class DefaultPredictor:
	def __init__(self, cfg):
		pass
	def __call__(self, original_image):
		pass 
		return
        
self.predictor = DefaultPredictor(cfg)
predictions = self.predictor(image)
```

## With 用法

### 文件操作

```
with open (filename,'w') as sh:  
	sh.write("hello world")
	sh.close()
```

使用with 打开文件, 程序结束后会自动帮忙关闭文件

### torch.no_grad()

默认的torch计算是会产生计算图的, 因为我们demo不进行反向传播, 因此不需要产生计算图. 加快运行速度

```
with torch.no_grad():
	pass
```

## 文件\_\_init__

作用, 把这个文件夹里面的文件合起来, 只要调用文件夹就可以使用里面函数了(具体是all的函数,见下文). 

简单理解, 相当于init文件间接执行了文件导入；所以在外部调用时，就没有这一步；本质上是有，只是被隐藏了；  本质上都必须：包名->文件名->对象；之所以能成功包名->对象；是因为`__init__`文件中加入了文件名->对象；直接将对象添加到包的第一个子集中了



## \_\_all\_\_ , globals()和startswith()

`globals()`是函数会以字典类型返回当前位置的全部全局变量

`startswith`判断字符串是否以指定字符或子字符串开头

```
__all__ = [k for k in globals().keys() if not k.startswith("_")]
```

\_\_all\_\_中是目前所有不以"_"开头的变量

而`__all__`在\_\_init\_\_.py中表示 只有all中指定的属性,函数,类会被导入

_fun表示protected , __表示private 直接调用无法使用, 但是使用all则可以调用

[参考资料](https://www.cnblogs.com/wxlog/p/10566628.html)



### ImportError:attempted relative import with no known parent package

接上, skip 

尝试运行得到\_\_all__的结果, 但是非法使用

[参考资料](https://blog.csdn.net/nigelyq/article/details/78930330)



## 元数据 metadata

描述数据的数据, 主要描述数据的属性的信息



## 星号参数

### 一个星号的参数 *arg

传入多余的参数会作为 一个元组传入. 如果传入一个元组, 则元组会作为参数的一个元素

如果需要传入元组, 可以传入*(xxx)

```
singalStar("hello", ("world", 000))
singalStar("hello", *("world", 000)) # 传入元组
```

### 两个星号的参数 **arg

多余的传入参数使用 args = value, 接受作为字典. 同理 使用**{x:3, y:5}就可以传入字典了

```
doubleStar("hello", **{"name": "Test", "age": 24})
```

在有些情况下，单星号函数参数和双星号函数参数是一起使用的

### 函数中独立星号

```
def f(x,*,y):
	pass

f(3,4) #error
f(3,y=4) #OK
```

*后面的所有参数必须带名字才能调用, 星号本身不是参数



## 元组

(1,2) 和 (1,2,)

如果是`a=(1)` 表示一个整数, `a=(1,)` 表示一个元组. 如果有几个元素, 就没有影响了 

`(1,2,)==(1,2)`



## hasattr

系统函数 判断是否有这个属性

```
class test:
	x = 10
    y = 12
print(hasattr(point1, 'x'))
```



```
super().__init__()  
```

继承父类的构造函数中的内容. 就相当于调用这个类的父亲类的init()



# CV基础知识

## 图片格式

cv2的图片格式

shape[1]长, shape[2]宽, shape[3]通道 

**区别** CV2的颜色是 BGR, 而matplotlib的颜色的RGB

使用`image = image[:, :, ::-1]` 将cv2换为matplotlib



## torch计算图

torch会把运行中的计算存储到一个计算图中(其实就是树)

然后通过计算图进行反向传播 (tips 默认梯度是不清空的)

使用 : `with torch.no_grad():`



## fvcore (skip)

一个轻量级的核心库, 提供了计算机视觉框架中共享的最常见和最基本的功能.

