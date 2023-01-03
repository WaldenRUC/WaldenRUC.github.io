---
title: 'Python Q&A'
date: 2020-11-06
permalink: /posts/2020/11/python/
tags:
  - Python
---




## Matplotlib.pyplot中文显示乱码的解决方法（内核代码修改）

首先打开终端，输入python进入shell

```shell
>>> import matplotlib
>>> print(matplotlib.matplotlib_fname())
/Users/walden/opt/anaconda3/lib/python3.8/site-packages/matplotlib/mpl-data/matplotlibrc
```

随意打开一个文件夹，输入shift+cmd+G，将上文的输出拷贝并粘贴于此，进入mpl-data文件夹

![image-20201106130834436](/images/matplotlib/image-20201106130834436.png)

这里有两个需要更改的文件与文件夹：fonts文件夹与matplotlibrc配置文件。

在对fonts文件夹修改前，打开【字体册】应用程序，找到【黑体-简】的字体，并在Finder中显示。

系统打开以下文件夹：

![image-20201106131031290](/images/matplotlib/image-20201106131031290.png)

随意拷贝其中的一份文件，然后打开刚才的fonts文件夹。

在fonts文件夹下打开fonts文件夹，将刚才的字体拷贝进来。

打开matplotlibrc文件，这里记载着所有的配置。每一项的冒号后，按顺序排列应选择的![image-20201106131206243](/images/matplotlib/image-20201106131206243.png)

在font.serif后，添加刚刚添加进来的字体名称（**这个名称可能和文件名不一样！因此要到网上查一下**）

然后保存退出，matplotlib就可以正常调用中文了。

#### 备选方案

```python
plt.rcParams['font.sans-serif']=['Songti SC'] #用来正常显示中文标签
```

在smoothNLP的github主页上也有相应解决方案

## 设置Matplotlib.pyplot支持中文

```python
import matplotlib.pyplot as plt
x = [1,2,3,4,5]
y = [1,4,9,16,25]
#调用绘制的plot方法
plt.plot(x, y, linewidth=5)
plt.xlabel('x')
plt.ylabel('y=x^2')
#设置支持中文
plt.rcParams['font.sans-serif']=['Songti SC'] #用来正常显示中文标签
#添加标题
plt.title('多个点绘制折线图') #会出现乱码
 
#显示绘制的图
plt.show()
```