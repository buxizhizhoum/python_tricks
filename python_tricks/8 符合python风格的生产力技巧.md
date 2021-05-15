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


## 8.2 使用Virtualenv隔离项目依赖项

Python包含一个功能强大的打包系统，用于管理包依赖。
你可能已经使用pip包管理命令安装了第三方软件包。

使用pip安装包的一个问题是它默认会将它们安装到全局的Python环境中。

当然，这可以使你安装的所有新软件包全局可用，这非常方便。
但是，如果你要处理的多个项目需要使用同一软件包的不同版本，
那么它也会很快变成一场噩梦。

例如，如果你的一个项目需要一个库的1.3版本，
而另一个项目需要同一个库的1.4版本，该怎么办？

在全局安装软件包时，所有程序中只能有一个Python库版本。
这意味着你将很快会遇到版本冲突，就像高地人[^1]一样。
[^1]: 这个梗来自经典电视剧“Highlander: The Series”(《挑战者》)，
      因为苏格兰北部为高地(Highland)，
      而剧中的苏格兰裔男主角每次都只能战斗到剩下一个人

更糟的是，你可能还需要在不同的程序中使用不同版本的Python。
例如，某些程序可能仍在Python 2上运行，而大多数新开发的程序都在Python 3上运行。
或者，如果你的一个项目需要Python 3.3，而其他所有项目都在Python 3.6上运行，该怎么办？

除此之外，在全局安装Python软件包还可能带来安全风险。
修改全局环境通常需要你使用超级用户（root/admin）运行pip install命令。
因为在安装新软件包时pip会从Internet下载并执行代码，
所以通常不建议这样做。
希望代码是值得信赖的，但是谁知道它真正会做什么呢？

## 使用虚拟环境

解决这些问题的方法是用虚拟环境将你的Python环境进行隔离。
它们使你可以按项目分隔Python依赖项，
并使你能够选择不同版本的Python解释器。

虚拟环境是一个隔离的Python环境。
从物理上讲，它位于一个文件夹内，
这个文件夹包含Python项目所需的所有包和其他依赖项，
例如代码库和解释器运行时。（在底层，为了节约存储空间，这些文件可能不是真实的副本，而是只是链接。）

为了演示虚拟环境的工作原理，
我将向你快速介绍如何设置一个新环境（简称为virtualenv），
然后在其中安装第三方程序包。

首先，让我们检查一下全局Python环境所在的位置。
在Linux或macOS上，我们可以使用which命令行工具来查找pip的路径：

    $ which pip3
    /usr/local/bin/pip3

为了保持美观和独立，我通常将虚拟环境放在项目文件夹中。
但是，你也可以在某个地方有一个专用的python环境目录，
用来容纳跨项目的所有环境。
这是你的选择。

让我们创建一个新的Python虚拟环境：

    $ python3 -m venv ./venv

这需要一点时间，它会在当前目录中创建一个venv文件夹，
文件夹中会包含一个基准的Python 3环境：

    $ ls venv/
    bin include lib pyvenv.cfg

如果你检查pip的活动版本（使用which命令），
你会看到它仍指向全局环境，在我的环境下是`/usr/local/bin/pip3`：

    (venv) $ which pip3
    /usr/local/bin/pip3

这意味着，如果你现在安装软件包，它们仍然会在全局Python环境中运行。
创建虚拟环境文件夹是不够的，你需要显式激活新的虚拟环境，
以便以后运行pip命令时引用该文件夹：

    $ source ./venv/bin/activate
    (venv) $

运行activate命令会将当前的Shell会话配置为使用虚拟环境中的Python和pip命令。

请注意，这会更改你的Shell提示符，在括号内会包含活动的虚拟环境的名称：(venv)。
让我们检查一下哪个pip现在处于活动状态：

    (venv) $ which pip3
    /Users/dan/my-project/venv/bin/pip3

如你所见，执行pip3命令现在会运行在虚拟环境中的pip而不是全局环境中的。
Python解释器也是如此。
现在，从命令行运行python也会从venv文件夹中加载解释器：

    (venv) $ which python
    /Users/dan/my-project/venv/bin/python

请注意，这仍然是一片空白，一个完全干净的Python环境。
执行pip list将显示几乎没有已安装的包，
仅仅包括支持pip本身所需的基准模块：

    (venv) $ pip list
    pip (9.0.1)
    setuptools (28.8.0)

让我们继续，现在将Python软件包安装到虚拟环境中。
你想使用熟悉的pip install命令：

    (venv) $ pip install schedule
    Collecting schedule
    Downloading schedule-0.4.2-py2.py3-none-any.whl
    Installing collected packages: schedule
    Successfully installed schedule-0.4.2

你会注意到两个重要的变化。
首先，你将不再需要管理员权限才能執行此命令。
其次，使用激活的虚拟环境安装或更新包意味着所有文件都将最终存储在虚拟环境目录的子文件夹中。

因此，你的项目依赖项将与系统上的所有其他Python环境（包括全局环境）在物理上隔離。
实际上，你会为每个项目获得一个单独的Python运行时的克隆。

通过再次运行pip list，你可以看到schedule库已成功安装到新环境中：

    (venv) $ pip list
    pip (9.0.1)
    schedule (0.4.2)
    setuptools (28.8.0)

如果我们使用python命令启动一个Python解释器会话，
或者使用它运行一个独立的`.py`文件，
它将使用安装在虚拟环境中的Python解释器和依赖项——只要该环境仍处于激活状态。

但是，如何再次停用或“离开”虚拟环境呢？
与`activate`命令类似，有一个`deactivate`命令可以让你回到全局环境：

    (venv) $ deactivate
    $ which pip3
    /usr/local/bin

使用虚拟环境将有助于使你的系统整洁，并使Python依赖项井井有条。
最佳做法是，所有Python项目均应使用虚拟环境来隔离依赖项并避免版本冲突。

了解和使用虚拟环境还可以使你走上正确的道路，你可以使用更高级的依赖关系管理方法，
例如使用requirements.txt文件指定项目依赖。

如果你想深入了解这个主题以获得其他提高效率的提示，
请务必看看我的[课程](https://realpython.com/products/managing-python-dependencies/) 。

## 重点

- 虚拟环境可以使项目依赖相互隔离。
  它们帮助你避免Python包与运行时的版本冲突。
- 最佳做法是，所有Python项目均使用虚拟环境来存储其依赖项。这将有助于避免一些头痛的问题。


## 字节码







