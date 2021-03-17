# 🐍'*arg 和** kwarg'


当你不确定你的函数里将要传递多少参数时你可以用`*args`, 它可以传递任意数量的参数。

`**kwargs`允许你使用没有事先定义的参数名。

- *arg 会把多出来的位置参数转化为 tuple
- **kwarg 会把关键字参数转化为 dict

`*args`和`**kwargs`可以同时在函数的定义中，但是`*args`必须在`**kwargs`前面。

```python
>>> def print_everything(*args):
        for count, thing in enumerate(args):
...         print ('{0}. {1}'.format(count, thing))
...
>>> print_everything('apple', 'banana', 'cabbage')
    0. apple
    1. banana
    2. cabbage
```

```python
>>> def table_things(**kwargs):
...     for name, value in kwargs.items():
...         print ('{0} = {1}'.format(name, value))
...
>>> table_things(apple = 'fruit', cabbage = 'vegetable')
    cabbage = vegetable
    apple = fruit
```
当调用函数时你也可以用、*和、*\*语法。例如：

```python
>>> def print_three_things(a, b, c):
...     print 'a = {0}, b = {1}, c = {2}'.format(a,b,c)
...
>>> mylist = ['aardvark', 'baboon', 'cat']
>>> print_three_things(*mylist)

a = aardvark, b = baboon, c = cat
```

两个混在一起用要注意顺序
```python
def learnarg(*args, **kwargs):
    for i in args + tuple(kwargs.values()):
        print(i)

    for key, value in kwargs.items():
        print('{}={}'.format(key, value))

learnarg('Wo', 4, 'KissAndRun', b=7, a=5, c='Hi')
```

