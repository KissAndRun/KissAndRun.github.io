# 🐍python 中的装饰器

装饰器 (Decorator) 就是为了增强某个函数的功能，但又不想改变这个函数本来的定义而引进的。

'@' 用做函数的修饰符，可以在模块或者类的定义层内对函数进行修饰，出现在函数定义的前一行，不允许和函数定义在同一行。
```python
def log(func):
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper

@log     #注意这里没有 ()
def now():
    print('2015-3-25')

>>> now()
call now():
2015-3-25
```

把 @log 放到 now() 函数的定义处，相当于执行了语句：

> now = log(now)

这样，当其他函数也需要这个装饰的时候，也在函数前加 @... 就可以避免大量重复的代码。

### 带参数的装饰器

带参数的装饰器其实是对原装饰器的封装。

```python
def use_logging(level):
    def decorator(func):
        def wrapper(*args, **kwargs):
            if level == "warn":
                logging.warn("%s is running" % func.__name__)
            return func(*args)
        return wrapper

    return decorator

@use_logging(level="warn")
def foo(name='foo'):
    print("i am %s" % name)

foo()
```

相当于执行了：
>foo=using_logging(level="warm")(foo)
使用装饰器极大地复用了代码，但是他有一个缺点就是原函数的元信息不见了。

Python 内置的 functools.wraps 就是解决这个问题的。

```python
import functools

def log(text):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator
```

