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

在上面的示例中，我首先从标准库中导入了datetime模块，然后使用dir()函数对其进行了检查。
在模块上调用dir()会提供按字母顺序排列的名称及属性列表。

因为Python中一切皆对象，所以相同的技术不仅适用于模块本身，而且适用于模块导出的类和数据结构。

实际上，你可以通过再次对感兴趣的单个对象调用dir()来继续深入探索。
例如，以下是检查datetime.date类的方法：

    >>> dir(datetime.date)
    ['__add__', '__class__', ..., 'day', 'fromordinal',
    'isocalendar', 'isoformat', 'isoweekday', 'max',
    'min', 'month', 'replace', 'resolution', 'strftime',
    'timetuple', 'today', 'toordinal', 'weekday', 'year']

如你所见，dir()提供了一个模块或类中可用内容的快速概述。
如果你不记得特定类或函数的确切拼写，它可以在不中断你编码流程的情况下给予你恰当的帮助。

有时在对象上调用dir()会返回过多的信息——在复杂的模块或类上，
你会得到很长的输出，这会让人难以快速进行阅读。
你可以使用以下技巧来对属性列表进行过滤，只保留你感兴趣的属性：

    >>> [_ for _ in dir(datetime) if 'date' in _.lower()]
    ['date', 'datetime', 'datetime_CAPI']

在这里，为了输出仅包含单词“date”的名称，我使用列表解析来过滤`dir(datetime)`的结果。
注意我如何在每个名称上调用`lower()`方法，以确保过滤不区分大小写。

仅仅获取对象属性的原始列表并不足以解决当前的问题。
那么，如何获得有关datetime模块的更多信息以及从中导出的函数和类的详细信息？

Python内置的help()函数可以助你一臂之力。
借助它，你可以调用Python的交互式帮助系统来浏览任何对象的文档：

    >>> help(datetime)

如果你在Python解释器会话中运行上述示例，则终端会显示datetime模块的文本帮助信息：

    Help on module datetime:
    NAME
        datetime - Fast implementation of the datetime type.
    CLASSES
        builtins.object
            date
                datetime
            time

你可以使用上下光标键在文档中滚动。或者，你也可以敲空格键一次向下滚动几行。
按q键可以退出交互帮助模式。这将带你返回解释器提示。
不错的功能，对不对？

顺便说一句，你可以在任意Python对象（包括其他内置函数和你自己的Python类）上调用help()。
Python解释器会根据对象的属性及其文档字符串（如果有）自动生成这个文档。
以下示例是help函数的所有有效用法：

    >>> help(datetime.date)
    >>> help(datetime.date.fromtimestamp)
    >>> help(dir)

当然，dir()和help()不会取代格式精美的HTML文档、搜索引擎或Stack Overflow搜索。
但是，它们是无需离开Python解释器快速查找信息的好工具。
它们还可以脱机使用，无需连接互联网即可工作，这在紧要关头非常有用。

## 重点
- 使用内置的dir()函数可以在解释器会话中交互式地探索Python模块和类。
- 内置的help()可让你直接从解释器中浏览文档（按q退出）。