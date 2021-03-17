# 🐍python 的动态语言特性


动态编程语言是高级程序设计语言的一个类别，在计算机科学领域已被广泛应用。它是一类在运行时可以改变其结构的语言：例如新的函数、对象、甚至代码可以被引进，已有的函数可以被删除或是其他结构上的变化。ECMAScript（JavaScript）便是一个动态语言，除此之外如 PHP、Ruby、Python 等也都属于动态语言，而 C、C++ 等语言则不属于动态语言。

当定义了一个 class 的实例之后，我们能动态的绑定任何属性和方法。涨知识了。..

```python
class Student(object):
    pass

han = Student()
han.name = 'han' #绑定 name 属性
print(han.name)
```
给实例绑定方法需要用到 MethodType
```python
from types import MethodType

def set_age(self, age):
    self.age = age

class Student(object):
    pass

s_one = Student()
s_one.set_age = MethodType(set_age,s_one)
```
但是，以上两种都是给 class 的某一实例单独绑定，对 class 的另外实例是不起作用的。

当然我们可以直接给 class 绑定。

```python
def set_name(self,name)
    self.name = name

Student.set_name = set_name
```

## 使用、__slots__限制实例的属性
使用、__slots__能够限制添加到实例的属性。
```python
class Student(object):
    __slots__ = ('name', 'age') # 用 tuple 定义允许绑定的属性名称
```
但是`__slots__`只对当前类起作用，对它的子类不起作用。除非在子类中也定义`__slots__`，这样，子类实例允许定义的属性就是自身的`__slots__`加上父类的`__slots__`。

那要是父类没有`__slots__`呢？那就不生效。指的是就算子类指定了`__slots__`也不会生效

```python
class Person(object):
    pass

class Student(Person):
    __slots__ = ('name', 'age')  # 用 tuple 定义允许绑定的属性名称

s = Student()
s.name = "tom"
s.age = 23
s.gg = 99
print(s.name, s.age, s.gg)
```
则不会报错。继承链中如果哪一级少了`__slots__`属性实现，子类中的`__slots__`失效。

## @property 的使用
Python 内置的 @property 装饰器能把一个方法变成属性调用

```python
class Student(object):

    @property
    def score(self):
        return self._score

    @score.setter
        def score(self, value):
            if not isinstance(value, int):
                raise ValueError('score must be an integer!')
            if value < 0 or value > 100:
                raise ValueError('score must between 0 ~ 100!')
            self._score = value
```
如果不定义 setter 属性，只定义 getter 属性，那么它就是一个只读的属性。

