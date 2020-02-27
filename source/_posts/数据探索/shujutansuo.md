title: 数据探索
tags:
  - python
  - 数据探索
categories: []
author: 杜先森
date: 2020-02-21 00:58:00
---
---
# <center>python与数据挖掘</center>
# <center>之数据探索</center>

![jk](http://cdn.mucjustin.cn/blog/2020-02-21-11.jpg)

<!--more-->

# 一、数据质量分析
## 1.缺失值（略）
## 2.一致性（略）
## 3.异常值（如下）


```python
import pandas as pd
catering_sale = "/Users/justin/Downloads/data_mining/chapter3/demo/data/catering_sale.xls"
data = pd.read_excel(catering_sale, index_col='日期')
print(data.head())
print(data.describe())
```

                    销量
    日期                
    2015-03-01    51.0
    2015-02-28  2618.2
    2015-02-27  2608.4
    2015-02-26  2651.9
    2015-02-25  3442.1
                    销量
    count   200.000000
    mean   2755.214700
    std     751.029772
    min      22.000000
    25%    2451.975000
    50%    2655.850000
    75%    3026.125000
    max    9106.440000


count是非空值数，通过


```python
len(data)
```




    201



知道数据记录为201条，故缺失值数为1.
data.describe()还可以知道平均值（mean）、标准差（std）、最大最小值还有1/4、1/2、3/4 分位数
可以用箱线图直观检测异常值


```python
from matplotlib.font_manager import _rebuild
_rebuild()

import matplotlib.pyplot as plt  # 导入图像库
plt.rcParams['font.sans-serif'] = ['SimHei']  # 用来正常显示中文标签
plt.rcParams['axes.unicode_minus'] = False  # 用来正常显示负号

plt.figure()  # 建立图像
p = data.boxplot(return_type='dict')  # 画箱线图，直接使用DataFrame的方法
x = p['fliers'][0].get_xdata()  #  'flies'即为异常值的标签
y = p['fliers'][0].get_ydata()
y.sort()  # 从小到大排序，该方法直接改变原对象

for i in range(len(x)):
    if i>0:
        plt.annotate(y[i], xy = (x[i],y[i]), xytext=(x[i]+0.05 -0.8/(y[i]-y[i-1]),y[i]))
    else:
        plt.annotate(y[i], xy = (x[i],y[i]), xytext=(x[i]+0.08,y[i]))

plt.show()  # 展示箱线图
```

![](http://cdn.mucjustin.cn/blog/2020-02-21-output_5_0.png)



上图可看出，865.0、4060.3、4065.2归为正常值，22.0、51.0、60.0、6607.4、9106.44归为异常值。



# 二、数据特征分析
## 1.定性数据的分布分析
### 第一步： 求极差
### 第二步： 决定组距与组数
### 第三步： 决定分点
### 第四步： 列出频率分布表
### 第五步： 绘制频率分别直方图

举例说明”捞起生鱼片“的销售情况


```python
import pandas as pd
import numpy as np
catering_sale = '/Users/justin/Downloads/data_mining/chapter3/demo/data/catering_fish_congee.xls'  # 餐饮数据
data = pd.read_excel(catering_sale,names=['date','sale'])  # 读取数据，指定“日期”列为索引
print(data.head())   #概览数据
print(data.describe())
```

            date  sale
    0 2014-04-02   900
    1 2014-04-03  1290
    2 2014-04-04   420
    3 2014-04-05  1710
    4 2014-04-06  1290
                  sale
    count    90.000000
    mean   1241.333333
    std     941.317843
    min      45.000000
    25%     420.000000
    50%     900.000000
    75%    1792.500000
    max    3960.000000


1) 求极差


```python
#极差 = 最大值 - 最小值 = 3960 - 45 = 3915
```

2) 分组


```python
#取组距 = 500 ，组数 = 极差 / 组距 = 3915 / 500 = 7.83 ~ 8
```

3) 决定分点


```python
bins = [0,500,1000,1500,2000,2500,3000,3500,4000]
labels = ['[0,500)','[500,1000)','[1000,1500)','[1500,2000)',
       '[2000,2500)','[2500,3000)','[3000,3500)','[3500,4000)'] 
```

4) 绘制频数分布直方表

5) 绘制频数分布直方图


```python
data['sale分层'] = pd.cut(data.sale, bins, labels=labels)
aggResult = data.groupby(by=['sale分层'])['sale'].agg({'sale': np.size})

pAggResult = round(aggResult/aggResult.sum(), 2, ) * 100

import matplotlib.pyplot as plt
plt.figure(figsize=(10,6))  # 设置图框大小尺寸
pAggResult['sale'].plot(kind='bar',width=0.8,fontsize=10)  # 绘制频率直方图
plt.rcParams['font.sans-serif'] = ['SimHei']  # 用来正常显示中文标签
plt.title('季度销售额频率分布直方图',fontsize=20)
plt.show()
```

    
    
      


![](http://cdn.mucjustin.cn/blog/2020-02-21-output_19_1.png)



## 2.定量数据的分布分析
常常根据变量的分类类型来分组，用饼图或条形图展现


```python
# 代码3-4 不同菜品在某段时间的销售量的分布情况
import pandas as pd
import matplotlib.pyplot as plt
catering_dish_profit = '/Users/justin/Downloads/data_mining/chapter3/demo/data/catering_dish_profit.xls'  # 餐饮数据
data = pd.read_excel(catering_dish_profit)  # 读取数据，指定“日期”列为索引

# 绘制饼图
x = data['盈利']
labels = data['菜品名']
plt.figure(figsize = (8, 6))  # 设置画布大小
plt.pie(x,labels=labels)  # 绘制饼图
plt.rcParams['font.sans-serif'] = 'SimHei'
plt.title('菜品销售量分布（饼图）')  # 设置标题
plt.axis('equal')
plt.show()

# 绘制条形图
x = data['菜品名']
y = data['盈利']
plt.figure(figsize = (8, 4))  # 设置画布大小
plt.bar(x,y)
plt.rcParams['font.sans-serif'] = 'SimHei'
plt.xlabel('菜品')  # 设置x轴标题
plt.ylabel('销量')  # 设置y轴标题
plt.title('菜品销售量分布（条形图）')  # 设置标题
plt.show()  # 展示图片
```

![](http://cdn.mucjustin.cn/blog/2020-02-21-output_21_0.png)



![](http://cdn.mucjustin.cn/blog/2020-02-21-output_21_1.png)



## 3.对比分析 

以各菜品的销售数据为例，从时间维度上分析，看到A B C 三个部门销售金额随时间的变化趋势。
也可以从单一部门分析，了解各年的销售对比情况。


```python
# 部门之间销售金额比较
import pandas as pd
import matplotlib.pyplot as plt
data=pd.read_excel("/Users/justin/Downloads/data_mining/chapter3/demo/data/dish_sale.xls")
print(data.head())

plt.figure(figsize=(8, 4))
plt.plot(data['月份'], data['A部门'], color='green', label='A部门',marker='o')
plt.plot(data['月份'], data['B部门'], color='red', label='B部门',marker='s')
plt.plot(data['月份'], data['C部门'],  color='skyblue', label='C部门',marker='x')
plt.legend() # 显示图例
plt.ylabel('销售额（万元）')
plt.show()
```

       月份   A部门  B部门  C部门
    0  1月  8.00  7.7  5.3
    1  2月  6.00  6.5  5.2
    2  3月  6.89  7.9  5.8
    3  4月  6.10  7.5  6.2
    4  5月  6.05  8.0  5.9




![](http://cdn.mucjustin.cn/blog/2020-02-21-output_24_1.png)


```python
#  B部门各年份之间销售金额的比较
data=pd.read_excel("/Users/justin/Downloads/data_mining/chapter3/demo/data/dish_sale_b.xls")
print(data.head())

plt.figure(figsize=(8, 4))
plt.plot(data['月份'], data['2012年'], color='green', label='2012年',marker='o')
plt.plot(data['月份'], data['2013年'], color='red', label='2013年',marker='s')
plt.plot(data['月份'], data['2014年'],  color='skyblue', label='2014年',marker='x')
plt.legend() # 显示图例
plt.ylabel('销售额（万元）')
plt.show()
```

       月份  2014年  2013年  2012年
    0  1月   7.90    7.7    5.3
    1  2月   6.00    6.5    5.2
    2  3月   6.89    7.9    5.8
    3  4月   7.30    7.5    6.2
    4  5月   7.60    8.0    5.9




![](http://cdn.mucjustin.cn/blog/2020-02-21-output_25_1.png)


总体来看，3个部门的销售额呈递减趋势；
A、C部门的递减趋势较为平稳；
B部门销售额下降明显；
进一步分析原因。。。

## 4.统计量分析

集中趋势：平均水平：均值  中位数  众数
离中趋势：变异程度：标准差（方差） 四分位间距  变异系数


```python
# 餐饮销量数据统计量分析
import pandas as pd

catering_sale = '/Users/justin/Downloads/data_mining/chapter3/demo/data/catering_sale.xls'  # 餐饮数据
data = pd.read_excel(catering_sale, index_col = u'日期')  # 读取数据，指定“日期”列为索引列
data = data[(data[u'销量'] > 400)&(data[u'销量'] < 5000)]  # 过滤异常数据
statistics = data.describe()  # 保存基本统计量

statistics.loc['range'] = statistics.loc['max']-statistics.loc['min']  # 极差
statistics.loc['var'] = statistics.loc['std']/statistics.loc['mean']  # 变异系数
statistics.loc['dis'] = statistics.loc['75%']-statistics.loc['25%']  # 四分位数间距

print(statistics)
```

                    销量
    count   195.000000
    mean   2744.595385
    std     424.739407
    min     865.000000
    25%    2460.600000
    50%    2655.900000
    75%    3023.200000
    max    4065.200000
    range  3200.200000
    var       0.154755
    dis     562.600000


## 5.周期性分析 


```python
# 代码3-7 某单位日用电量预测分析

import pandas as pd
import matplotlib.pyplot as plt

df_normal = pd.read_csv("/Users/justin/Downloads/data_mining/chapter3/demo/data/user.csv")
print(df_normal.head())
plt.figure(figsize=(8,4))
plt.plot(df_normal["Date"],df_normal["Eletricity"])
plt.xlabel("日期")
plt.ylabel("每日电量")
# 设置x轴刻度间隔
x_major_locator = plt.MultipleLocator(7)
ax = plt.gca()
ax.xaxis.set_major_locator(x_major_locator)
plt.title("正常用户电量趋势")
plt.rcParams['font.sans-serif'] = ['SimHei']  # 用来正常显示中文标签
plt.show()  # 展示图片

# 窃电用户用电趋势分析
df_steal = pd.read_csv("/Users/justin/Downloads/data_mining/chapter3/demo/data/Steal user.csv")
plt.figure(figsize=(10, 9))
plt.plot(df_steal["Date"],df_steal["Eletricity"])
plt.xlabel("日期")
plt.ylabel("日期")
# 设置x轴刻度间隔
x_major_locator = plt.MultipleLocator(7)
ax = plt.gca()
ax.xaxis.set_major_locator(x_major_locator)
plt.title("窃电用户电量趋势")
plt.rcParams['font.sans-serif'] = ['SimHei']  # 用来正常显示中文标签
plt.show()  # 展示图片
```

           Date  Eletricity
    0  2012/2/1        6200
    1  2012/2/2        6440
    2  2012/2/3        6440
    3  2012/2/4        4293
    4  2012/2/5        2146


![](http://cdn.mucjustin.cn/blog/2020-02-21-output_31_1.png)



![](http://cdn.mucjustin.cn/blog/2020-02-21-output_31_2.png)



总体来看，正常用户在12年2、3月份用电量呈现周期性，以周为周期，因为周末不上班，所以周末用电量低。
窃电用户用电量呈递减趋势。

## 6.贡献度分析（帕累托分析） 
20/80定律


```python
# 菜品盈利数据 帕累托图
import pandas as pd

# 初始化参数
dish_profit = '/Users/justin/Downloads/data_mining/chapter3/demo/data/catering_dish_profit.xls'  # 餐饮菜品盈利数据
data = pd.read_excel(dish_profit, index_col = u'菜品名')
data = data[u'盈利'].copy()
data.sort_values(ascending = False)
print(data.head())

import matplotlib.pyplot as plt  # 导入图像库
plt.rcParams['font.sans-serif'] = ['SimHei']  # 用来正常显示中文标签
plt.rcParams['axes.unicode_minus'] = False  # 用来正常显示负号

plt.figure()
data.plot(kind='bar')
plt.ylabel(u'盈利（元）')
p = 1.0*data.cumsum()/data.sum()
p.plot(color = 'r', secondary_y = True, style = '-o',linewidth = 2)
plt.annotate(format(p[6], '.4%'), xy = (6, p[6]), xytext=(6*0.9, p[6]*0.9), arrowprops=dict(arrowstyle="->", connectionstyle="arc3,rad=.2"))  # 添加注释，即85%处的标记。这里包括了指定箭头样式。
plt.ylabel(u'盈利（比例）')
plt.show()
```

    菜品名
    A1    9173
    A2    5729
    A3    4811
    A4    3594
    A5    3195
    Name: 盈利, dtype: int64


![](http://cdn.mucjustin.cn/blog/2020-02-21-output_34_1.png)




由图，占总菜品70%的7个菜品A1-A7总盈利额占该月盈利额的85%，根据帕累托法则，应该加大对这七个的成本投入。

## 7.相关性分析

### 1) 直接绘制散点图 

### 2) 绘制散点图矩阵

### 3) 计算相关系数（Pearson相关系数、Spearman秩相关系数、判定系数：相关系数的平方）


```python
# 餐饮销量数据相关性分析
from __future__ import print_function
import pandas as pd

catering_sale = '/Users/justin/Downloads/data_mining/chapter3/demo/data/catering_sale_all.xls'  # 餐饮数据，含有其他属性
data = pd.read_excel(catering_sale, index_col = u'日期')  # 读取数据，指定“日期”列为索引列

print(data.head())

print(data.corr())  # 相关系数矩阵，即给出了任意两款菜式之间的相关系数

print(data.corr()[u'百合酱蒸凤爪'])  # 只显示“百合酱蒸凤爪”与其他菜式的相关系数

# 计算“百合酱蒸凤爪”与“翡翠蒸香茜饺”的相关系数
print(data[u'百合酱蒸凤爪'].corr(data[u'翡翠蒸香茜饺']))
```

                百合酱蒸凤爪  翡翠蒸香茜饺  金银蒜汁蒸排骨  乐膳真味鸡  蜜汁焗餐包  生炒菜心  铁板酸菜豆腐  香煎韭菜饺  香煎罗卜糕  \
    日期                                                                              
    2015-01-01      17       6        8     24   13.0    13      18     10     10   
    2015-01-02      11      15       14     13    9.0    10      19     13     14   
    2015-01-03      10       8       12     13    8.0     3       7     11     10   
    2015-01-04       9       6        6      3   10.0     9       9     13     14   
    2015-01-05       4      10       13      8   12.0    10      17     11     13   
    
                原汁原味菜心  
    日期                  
    2015-01-01      27  
    2015-01-02      13  
    2015-01-03       9  
    2015-01-04      13  
    2015-01-05      14  
               百合酱蒸凤爪    翡翠蒸香茜饺   金银蒜汁蒸排骨     乐膳真味鸡     蜜汁焗餐包      生炒菜心    铁板酸菜豆腐  \
    百合酱蒸凤爪   1.000000  0.009206  0.016799  0.455638  0.098085  0.308496  0.204898   
    翡翠蒸香茜饺   0.009206  1.000000  0.304434 -0.012279  0.058745 -0.180446 -0.026908   
    金银蒜汁蒸排骨  0.016799  0.304434  1.000000  0.035135  0.096218 -0.184290  0.187272   
    乐膳真味鸡    0.455638 -0.012279  0.035135  1.000000  0.016006  0.325462  0.297692   
    蜜汁焗餐包    0.098085  0.058745  0.096218  0.016006  1.000000  0.308454  0.502025   
    生炒菜心     0.308496 -0.180446 -0.184290  0.325462  0.308454  1.000000  0.369787   
    铁板酸菜豆腐   0.204898 -0.026908  0.187272  0.297692  0.502025  0.369787  1.000000   
    香煎韭菜饺    0.127448  0.062344  0.121543 -0.068866  0.155428  0.038233  0.095543   
    香煎罗卜糕   -0.090276  0.270276  0.077808 -0.030222  0.171005  0.049898  0.157958   
    原汁原味菜心   0.428316  0.020462  0.029074  0.421878  0.527844  0.122988  0.567332   
    
                香煎韭菜饺     香煎罗卜糕    原汁原味菜心  
    百合酱蒸凤爪   0.127448 -0.090276  0.428316  
    翡翠蒸香茜饺   0.062344  0.270276  0.020462  
    金银蒜汁蒸排骨  0.121543  0.077808  0.029074  
    乐膳真味鸡   -0.068866 -0.030222  0.421878  
    蜜汁焗餐包    0.155428  0.171005  0.527844  
    生炒菜心     0.038233  0.049898  0.122988  
    铁板酸菜豆腐   0.095543  0.157958  0.567332  
    香煎韭菜饺    1.000000  0.178336  0.049689  
    香煎罗卜糕    0.178336  1.000000  0.088980  
    原汁原味菜心   0.049689  0.088980  1.000000  
    百合酱蒸凤爪     1.000000
    翡翠蒸香茜饺     0.009206
    金银蒜汁蒸排骨    0.016799
    乐膳真味鸡      0.455638
    蜜汁焗餐包      0.098085
    生炒菜心       0.308496
    铁板酸菜豆腐     0.204898
    香煎韭菜饺      0.127448
    香煎罗卜糕     -0.090276
    原汁原味菜心     0.428316
    Name: 百合酱蒸凤爪, dtype: float64
    0.009205803051836475


就先这样~