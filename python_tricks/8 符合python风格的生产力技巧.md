# 第八章　符合Python风格的生产力技术

## 8.1 探索Python的模块和对象

你可以直接在Python解释器以交互式的方式浏览模块和对象。
这是一个被低估的功能，它很容易被忽视，尤其是当你从另一种语言切换到Python时。

许多编程语言在不查阅联机文档或不认真学习接口定义的情况下很难检查包或类。

Python是不同的——高效的开发人员会在交互式会话中花费大量时间与Python解释器进行交互。
例如，我经常这样做是为了弄清小段的代码和逻辑，然后将其复制粘贴到编辑器中正在处理的Python文件中。

在本节中，你会学到两种简单的技术，可用于在Python解释器中交互式地探索Python的类和方法。

这些技术适用于任何Python——只需要在命令行使用`python`命令启动Python解释器，然后开始即可。
在你无法访问高级编辑器或IDE的系统上调试会话时，这会非常有用，比如你正在通过网络在终端会话中进行工作。

准备好了？开始！假设你正在编写一个使用Python标准库中datetime模块的程序。
你如何找到这个模块包含的函数或类，以及在这些类上有哪些方法和属性？

使用搜索引擎或在网络上查找Python的官方文档是实现此目标的一种方法。
但是，Python的内置dir()函数使你可以直接从Python的交互式会话中访问这些信息：

    >>> import datetime
    >>> dir(datetime)
    ['MAXYEAR', 'MINYEAR', '__builtins__', '__cached__',
    '__doc__', '__file__', '__loader__', '__name__',
    '__package__', '__spec__', '_divide_and_round',
    'date', 'datetime', 'datetime_CAPI', 'time',
    'timedelta', 'timezone', 'tzinfo']


