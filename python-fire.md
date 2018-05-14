[TOC]

# Python Fire

参考：

> https://github.com/google/python-fire
>
> https://blog.csdn.net/u010099080/article/details/70332074



## 简介

Python Fire是一个能为所有python对象自动创建命令行接口（CLI）的库。

1. Python Fire能方便地在python中创建CLI。


2. Python Fire是开发和调试python代码过程中有用的工具。


3. Python Fire能帮助探索现有的代码，或者是将别人的代码转为CLI。


4. Python Fire让Bash和python的过渡变得简单。


5. Python Fire将可能用到的模块和变量预先导入和创建好，以简化python REPL的使用。



## 如何使用

### 安装

```pip install fire```

### 使用

```python
import fire

fire.Fire() #此处可以不传参数，表示将当前模块转为CLI，也可以传入任意组件（component），将其转为CLI
```



## 示例

### 单个函数

> single_func.py

```python
# -*- coding: utf-8 -*-
import fire
import datetime

def cal_days(date_str1, date_str2):
    '''计算两个日期之间的天数'''

    date_str1 = str(date_str1)
    date_str2 = str(date_str2)
    d1 = datetime.datetime.strptime(date_str1, '%Y%m%d')
    d2 = datetime.datetime.strptime(date_str2, '%Y%m%d')
    delta = d2 - d1
    return abs(delta.days)


if __name__ == '__main__':
    fire.Fire(cal_days)
```

#### 查看用法

```shell
$ python single_func.py
Fire trace:
1. Initial component
2. ('The function received no value for the required argument:', 'date_str1')

Type:        function
String form: <function cal_days at 0x7f624a75f9b0>
File:        single_func.py
Line:        5
Docstring:   计算两个日期之间的天数

Usage:       single_func.py DATE_STR1 DATE_STR2
             single_func.py --date-str1 DATE_STR1 --date-str2 DATE_STR2
```

#### 使用

```shell
$ python single_func.py 20180101 20180228
58
```



### 多个函数

> multi_func.py

```python
# -*- coding: utf-8 -*-
import fire
import datetime

def cal_days(date_str1, date_str2):
    '''计算两个日期之间的天数'''

    date_str1 = str(date_str1)
    date_str2 = str(date_str2)
    d1 = datetime.datetime.strptime(date_str1, '%Y%m%d')
    d2 = datetime.datetime.strptime(date_str2, '%Y%m%d')
    delta = d2 - d1
    return abs(delta.days)

def days2today(date_str):
    '''计算某天距离今天的天数'''

    date_str = str(date_str)
    d = datetime.datetime.strptime(date_str, '%Y%m%d')
    delta = datetime.datetime.now() - d
    return abs(delta.days)

if __name__ == '__main__':
    fire.Fire()
```

#### 查看用法

概要信息：

```shell
$ python multi_func.py
fire:       <module 'fire' from '/usr/lib/python2.7/site-packages/fire/__init__.pyc'>
cal_days:   <function cal_days at 0x7f25f2ce89b0>
days2today: <function days2today at 0x20a6ed8>
datetime:   <module 'datetime' from '/usr/lib64/python2.7/lib-dynload/datetime.so'>
```

以上信息说明该文件引入了两个模块（fire、datetime），有两个方法（cal_days、days2today）
具体方法：

```shell
$ python multi_func.py days2today
Fire trace:
1. Initial component
2. Accessed property "days2today"
3. ('The function received no value for the required argument:', 'date_str')

Type:        function
String form: <function days2today at 0x2797ed8>
File:        multi_func.py
Line:        15
Docstring:   计算某天距离今天的天数

Usage:       multi_func.py days2today DATE_STR
             multi_func.py days2today --date-str DATE_STR
```

#### 使用

```shell
$ python multi_func.py days2today 20180101
107
```



### 类

> class_obj.py

```python
# -*- coding: utf-8 -*-
import fire
import datetime


class DateStr(object):

    def cal_days(self, date_str1, date_str2):
        '''计算两个日期之间的天数'''
        date_str1 = str(date_str1)
        date_str2 = str(date_str2)
        d1 = datetime.datetime.strptime(date_str1, '%Y%m%d')
        d2 = datetime.datetime.strptime(date_str2, '%Y%m%d')
        delta = d1 - d2
        return abs(delta.days)

    def days2today(self, date_str):
        '''计算某天距离今天的天数'''
        date_str = str(date_str)
        d = datetime.datetime.strptime(date_str, '%Y%m%d')
        delta = datetime.datetime.now() - d
        return abs(delta.days)


if __name__ == '__main__':
    fire.Fire(DateStr)
```

#### 查看用法

概要信息：

```shell
$ python class_obj.py 
Type:        DateStr
String form: <__main__.DateStr object at 0x1add250>

Usage:       class_obj.py 
             class_obj.py cal-days
             class_obj.py days2today
```

以上信息说明fire为DateStr对象创建了CLI。

具体用法：

```shell
$ python class_obj.py cal_days
Fire trace:
1. Initial component
2. Instantiated class "DateStr" (class_obj.py:6)
3. Accessed property "cal_days" (class_obj.py:8)
4. ('The function received no value for the required argument:', 'date_str1')

Type:        instancemethod
String form: <bound method DateStr.cal_days of <__main__.DateStr object at 0x1323250>>
File:        class_obj.py
Line:        8
Docstring:   计算两个日期之间的天数

Usage:       class_obj.py cal_days DATE_STR1 DATE_STR2
             class_obj.py cal_days --date-str1 DATE_STR1 --date-str2 DATE_STR2
```

#### 使用

```shell
$ python class_obj.py cal_days 20180101 20180202
32
```



## 注意事项

- `fire` 默认使用 `-` 作为参数分隔符，所以如果要在命令行传入类似 `2017-04-22` 的参数时，那么程序接收到的参数就肯定不是 `2017-04-22` 了，需要使用 `--separator` 来改变分隔符。参考 [Changing the Separator](https://github.com/google/python-fire/blob/master/doc/using-cli.md#separator-flag)

  ​

- `fire` 会自动区分在命令行传入的参数的类型，如 `20170422` 会自动识别成 `int`。但是你如果想要传入一个字符串类型的 `20170422` 怎么办？那就需要这样写：`'"20170422"'` 或者 `"'20170422'"` 或者 `\"20170422\"`，总之需要加一个转义，因为命令行默认会吃掉引号。参考 [Argument Parsing](https://github.com/google/python-fire/blob/master/doc/guide.md#user-content-argument-parsing)



## 集成到项目

> square_helper.py

```python
# -*- coding: utf-8 -*-
import math
import fire

EARTH_RADIUS = 6378137.0


def calculate_square(olong=None, olat=None, dis=40):
    """
    以(olong, olat)为中心点，dis为辐射范围，生成1km*1km的栅格数据
    :param olong: 中心点经度
    :param olat: 中心点纬度
    :param dis: 辐射范围
    :return: json，形如{‘polygon’:[[x1,y1],[x2,y2],[x3,y3],[x4,y4],[x1,y1]],‘center’:[x0,y0]}
    """
    if not float(dis).is_integer():
        raise ValueError("辐射范围必须为整数")
    if olong is None and olat is None:
        olong = 121.4539
        olat = 31.27405
    elif olong is not None and olat is not None:
        pass
    else:
        raise ValueError("经度和纬度需要同时填写或同时不填写")

    result = {}
    result.__setitem__("center", [olong, olat])
    lng_list = []
    lat_list = []
    polygon = []

    # -dis到0所产生的经纬度
    for r in range(dis):
        dis_meter = (dis - r) * 1000
        dlng = 2 * math.asin(math.sin(dis_meter / (2 * EARTH_RADIUS)) / math.cos(olat * math.pi / 180))
        dlng = degrees(dlng)
        dlat = degrees(dis_meter / EARTH_RADIUS)
        lng_list.append(round(olong - dlng, 6))
        lat_list.append(round(olat - dlat, 6))

    # 加入中心点
    lng_list.append(olong)
    lat_list.append(olat)

    # 0到+dis所产生的经纬度
    for r in range(dis):
        dis_meter = (r + 1) * 1000
        dlng = 2 * math.asin(math.sin(dis_meter / (2 * EARTH_RADIUS)) / math.cos(olat * math.pi / 180))
        dlng = degrees(dlng)
        dlat = degrees(dis_meter / EARTH_RADIUS)
        lng_list.append(round(olong + dlng, 6))
        lat_list.append(round(olat + dlat, 6))

    for lng_index, _ in enumerate(lng_list):
        if lng_index == len(lng_list) - 1:
            continue
        for lat_index, _ in enumerate(lat_list):
            if lat_index == len(lat_list) - 1:
                continue
            polygon.append(
                generate_rect_from_two_point(
                    [lng_list[lng_index], lat_list[lat_index]],
                    [lng_list[lng_index + 1], lat_list[lat_index + 1]]
                )
            )

    result.__setitem__("polygon", polygon)
    return result


def generate_rect_from_two_point(p1, p2):
    """
    根据两个顶点生成矩形
    :param p1: 点1
    :param p2: 点2
    :return: 矩形
    """
    return [[p1[0], p1[1]], [p2[0], p1[1]], [p2[0], p2[1]], [p1[0], p2[1]], [p1[0], p1[1]]]


# def radians(d):
#     """
#     角度转弧度
#     :param d: 角度
#     :return: 弧度
#     """
#     return d * math.pi / 180


def degrees(r):
    """
    弧度转角度
    :param r: 弧度
    :return: 角度
    """
    return r * (180 / math.pi)


fire.Fire(calculate_square)

```

### 用法

由于函数`calculate_square`接收三个参数，且三个参数均有默认值，因此`python square_helper.py`会直接执行函数，而不会展示用法。

该函数有如下三个参数：

* olong: 中心点经度
* olat: 中心点纬度
* dis: 辐射范围

使用：

```shell
$ python square_helper.py 121.533997 31.234454 10
```

指定某个参数：

```shell
$ python square_helper.py --dis=10
```

