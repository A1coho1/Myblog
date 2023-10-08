---
title: python迭代器与生成器
top: false
cover: true
toc: true
mathjax: false
summary: 个人对于python中迭代器与生成器的理解
tags: python
categories: python
abbrlink: 6883
date: 2023-10-08 23:40:42
updated: 2023-10-08 23:40:42
author:
img: https://pic.imgdb.cn/item/6522ddadc458853aef1101fb.webp
coverImg:
password:
---
## 1. 迭代器(Iterator)

### 1.1 问题引入

在python中，我们知道如果想获取一个列表中的每个元素，可以使用for循环去遍历：

```python
lst = [1, 3, 5]
for i in lst:
    print(i)
```

这件事其实是比较符合直觉的，一切看起来都是那么顺理成章。但是如果你了解过较为底层的编程语言(c语言)，你就会发现遍历数组得这么写：

```c
int arr[] = {1, 3, 5};
for (int i = 0; i < sizeof(arr)/sizeof(int); i ++){
    printf("%d\n", arr[i]);
}
```

上述c语言代码的目的就是去**访问容器中的每个元素**，而对于不同的数据类型比如单链表，其遍历方式又有所不同。

但我们知道在python中for循环是不仅仅只能用于列表的，它可以作用于以下两类：

- 集合数据类型，如 `list`, `dict`, `tuple`, `str`等等
- 生成器(generator), 以及带 `yield`的生成器函数 (生成器就是一类特殊的迭代器，具体见后文)

那为什么在python中一个简单的for循环就能搞定如此多的数据类型呢？背后的秘密就是**迭代器**。

### 1.2 可迭代对象(iterable)

> An object capable of returning its members one at a time. Examples of iterables include all sequence types (such as [`list`](https://docs.python.org/3/library/stdtypes.html#list), [`str`](https://docs.python.org/3/library/stdtypes.html#str), and [`tuple`](https://docs.python.org/3/library/stdtypes.html#tuple)) and some non-sequence types like [`dict`](https://docs.python.org/3/library/stdtypes.html#dict), [file objects](https://docs.python.org/3/glossary.html#term-file-object), and objects of any classes you define with an `__iter__()` method or with a `__getitem__()` method that implements [sequence](https://docs.python.org/3/glossary.html#term-sequence) semantics.
>
> Iterables can be used in a [`for`](https://docs.python.org/3/reference/compound_stmts.html#for) loop and in many other places where a sequence is needed ([`zip()`](https://docs.python.org/3/library/functions.html#zip), [`map()`](https://docs.python.org/3/library/functions.html#map), …). When an iterable object is passed as an argument to the built-in function [`iter()`](https://docs.python.org/3/library/functions.html#iter), it returns an iterator for the object. This iterator is good for one pass over the set of values. When using iterables, it is usually not necessary to call [`iter()`](https://docs.python.org/3/library/functions.html#iter) or deal with iterator objects yourself. The `for` statement does that automatically for you, creating a temporary unnamed variable to hold the iterator for the duration of the loop. See also [iterator](https://docs.python.org/3/glossary.html#term-iterator), [sequence](https://docs.python.org/3/glossary.html#term-sequence), and [generator](https://docs.python.org/3/glossary.html#term-generator).

以上内容摘自：[官方文档](https://docs.python.org/3/glossary.html#term-iterable)

我们可以通过 `isinstance()`方法来判断一个对象是否为可迭代对象：

```python
from collections.abc import Iterable
arr = [1, 3, 5]
print(isinstance(2, Iterable))
print(isinstance(arr, Iterable))
```

以上代码的运行结果如下：

```shell
>>> False
>>> True
```

也就是说，int类型的2并不是可迭代对象，而列表是可迭代对象。

简单地讲，可以直接用于for循环的对象都叫可迭代对象，也就是说for _ in `x`中的 `x`必须是一个可迭代对象。根据官方文档，iterable必须有一个 `__iter__()`方法或者 `__getitem__()`方法，而这二者都是为了**保证** `iterable`在 `iter()`函数(python内置用来返回迭代器的函数)的作用下可以**返回一个迭代器**(iterator)。

```python
lst = [1, 3, 5]
it = iter(lst)
print(it)
```

以上代码返回 `<list_iterator object at 0x000002D6CB9BFDF0>`，也就是说 `iter()`函数返回了 `lst`列表这个**可迭代对象**的**迭代器**。

### 1.3 迭代器

> An object representing a stream of data. Repeated calls to the iterator’s [`__next__()`](https://docs.python.org/3/library/stdtypes.html#iterator.__next__) method (or passing it to the built-in function [`next()`](https://docs.python.org/3/library/functions.html#next)) return successive items in the stream. When no more data are available a [`StopIteration`](https://docs.python.org/3/library/exceptions.html#StopIteration) exception is raised instead. At this point, the iterator object is exhausted and any further calls to its `__next__()` method just raise [`StopIteration`](https://docs.python.org/3/library/exceptions.html#StopIteration) again. Iterators are required to have an `__iter__()` method that returns the iterator object itself so every iterator is also iterable and may be used in most places where other iterables are accepted. One notable exception is code which attempts multiple iteration passes. A container object (such as a [`list`](https://docs.python.org/3/library/stdtypes.html#list)) produces a fresh new iterator each time you pass it to the [`iter()`](https://docs.python.org/3/library/functions.html#iter) function or use it in a [`for`](https://docs.python.org/3/reference/compound_stmts.html#for) loop. Attempting this with an iterator will just return the same exhausted iterator object used in the previous iteration pass, making it appear like an empty container.
>
> More information can be found in [Iterator Types](https://docs.python.org/3/library/stdtypes.html#typeiter).

以上内容摘自：[官方文档](https://docs.python.org/3/glossary.html#term-iterable)

根据官方文档，迭代器代表的是数据流，它必须要有 `__next__()`以及 `__iter__()`两个方法。可以用 `__next__()`方法或者python内置的 `next()`函数对iterator对象进行调用，它会返回数据流中的下一个数据，直到没有数据后抛出 `StopIteration`异常。

你可以把这里的数据流想象为有序序列，只是我们并不清楚它的长度，只能不断地通过 `next()`函数去访问下一个数据，**只有需要返回下一个数据时才会进行计算**。而迭代器的 `__iter__()`方法保证了**迭代器是一个可迭代对象**，这就让迭代器也可以被用于 `for`循环中，至于为什么这样要求，在下文介绍生成器时将会说明。当然在代码实现上你只需要让iterator的 `__iter__()`方法返回自身即可，也就是如果求这个迭代器的迭代器，那么就是其自身。

### 1.4 小结

**迭代器一定是可迭代对象，可迭代对象不一定是迭代器，但可迭代对象可以通过iter()函数获得一个迭代器**

我们可以将iterator看作指针，可以一直被 `next()`函数调用不断返回下一个数据直到没有数据后抛出异常。而iterable就是一个容器，只负责保存数据，但是它可以返回一个iterator，他也并不需要知道这个iterator数到了哪里。

因此，以 `lst = [1, 3, 5]`为例，`lst`是一个可迭代对象，但并不是一个迭代器。我们用 `it = iter(lst)`返回了 `lst`对象的迭代器，那么就可以用 `next(it)`去访问 `lst`中的下一个值直到抛出异常。

让我们再次回到我们所熟悉 `for循环`:

```python
lst = [1, 3, 5]
for i in lst:
    print(i)
```

其实它的本质是这样的:

```python
lst = [1, 3, 5]
it = iter(lst)
while True:
    try:
        x = next(it)
        print(x)
    except StopIteration:
        break
```

**综上，在我们看似顺理成章的coding中其实隐藏着前人们复杂的逻辑实现。**

## 2. 生成器(Generator)

### 2.1 问题引入

试想我们需要创建一个以一定的规则运算出来的列表，例如每个自然数的平方和，你可能会知道使用**列表生成式**，即`lst = [x * x for x in range(10)]`。对于较小的数据尚可，但如果用列表去储存千万乃至上亿的元素，将会极大地占用内存。

那么如果列表中的值依照一定的规则，我们是否可以在循环中不断推算出后续的元素呢？在python中，这种一边循环一边计算的机制就称为**生成器**。

### 2.2 生成器

我们只需要将上述代码改为`lst = (x * x for x in range(10))`即可得到一个生成器对象`<generator object <genexpr> at 0x000001F7DD509A10>`。我们可以直接使用`next()`函数获得generator的下一个值，上文我们提到过`next()`函数用于返回**迭代器**的下一个数据，因此我们说**生成器是一种特殊的迭代器**。

所以如果我们想遍历生成器中的所有元素，可以直接使用`for循环`。这也解释了为什么我们要求**迭代器本身也必须是可迭代对象，否则我们将不能使用for循环去遍历生成器**。

```python
lst = (x * x for x in range(10))
for x in lst:
    print(x)
```

这样我们就以最简单的方式创建了一个generator并用for循环遍历了其中所有元素。

- 特别注意：生成器和迭代器一样是**惰性的**，也就是说如果不调用next()函数，是不会对下一个值进行计算的，这种按需计算能极大地节省时间与空间。

### 2.3 yield关键字

如果一个函数中包含`yield`关键字，那么它就不再是一个普通函数，而是一个**生成器函数**，调用**生成器函数**将返回一个**生成器对象**。下面通过一个例子来进行说明：

```python
def fun(num):
    while num > 0:
        yield num
        num -= 1
    return

f = fun(5)

first = next(f)
print(first)

for i in f:
    print(i)
```

这里的`fun`函数由于具有`yield`关键字，所以是一个生成器函数，生成器函数与普通的函数不同。普通函数顺序执行，遇到return语句则进行返回，但生成器函数在每次调用`next()`函数时执行，遇到`yield`语句就返回，下次执行时再从`yield`语句处继续执行。所以上述代码中的`return`写或不写效果是完全一样的。这里的`fun`就是一个**生成器函数**，而`f`则是一个**生成器对象**，我们对`f`调用`next()`函数，首先判断5>0, 然后直接`yield`出`num=5`，函数在此暂停。所以我们打印出`first`的值为5，接着再使用`for`循环对`f`不断执行`next()`函数(上文讲过for循环本质就是调用迭代器的next函数)得到后续的`yield`返回值。

- 注意调用**生成器函数**会创建一个**生成器对象**，多次调用生成器函数所创建的生成器对象是**相互独立**的，也就是说如果直接执行`print(next(fun(5)))`，那么不管执行多少次，打印出的值都是5。

### 2.4 小结

生成器是一种特殊的迭代器，使用`yield`关键字的函数称为**生成器函数**，调用生成器函数返回**生成器对象**，生成器函数遇到`yield`则进行返回，直到被`next()`函数调用再从上一次`yield`的地方继续执行。