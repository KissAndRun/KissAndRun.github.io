# 🐍一些 python 的基础知识


## 参数的传递

对象有两种，“可更改”（mutable）与“不可更改”（immutable）对象。

在 python 中，strings, tuples, 和 numbers 是不可更改的对象，而 list, dict, set 等则是可以修改的对象。

不可变的对象作为参数时，函数不能使其改变。
```python
a = 1
def fun(a):  #创建一个新的引用指向 1
    a = 2   #这里将引用指向了一个不可变对象 2
fun(a)
print a  # 1
```
理解 1 是一个 number 对象，a 是对象的引用（变量）。
```python
a = []
def fun(a):
    a.append(1)  #对象可更改，直接在内存修改
fun(a)
print a  # [1]
```

## @classmethod 和 @staticmethod

Python 其实有 3 个方法，即静态方法 (staticmethod), 类方法 (classmethod) 和实例方法，如下：

```python

def foo(x):
    print "executing foo(%s)"%(x)

class A(object):

    def foo(self,x):
        print "executing foo(%s,%s)"%(self,x)

    @classmethod
    def class_foo(cls,x):
        print "executing class_foo(%s,%s)"%(cls,x)

    @staticmethod
    def static_foo(x):
        print "executing static_foo(%s)"%x

a=A()

```

这里先理解下函数参数里面的 self 和 cls. 这个 self 和 cls 是对类或者实例的绑定，对于一般的函数来说我们可以这么调用`foo(x)`, 这个函数就是最常用的，它的工作跟任何东西（类，实例）无关。对于实例方法，我们知道在类里每次定义方法的时候都需要绑定这个实例，就是`foo(self, x)`, 为什么要这么做呢？因为实例方法的调用离不开实例，我们需要把实例自己传给函数，调用的时候是这样的`a.foo(x)`（其实是`foo(a, x)`). 类方法一样，只不过它传递的是类而不是实例，`A.class_foo(x)`. 注意这里的 self 和 cls 可以替换别的参数，但是 python 的约定是这俩，还是不要改的好。

对于静态方法其实和普通的方法一样，不需要对谁进行绑定，唯一的区别是调用的时候需要使用`a.static_foo(x)`或者`A.static_foo(x)`来调用。

| \\      | 实例方法     | 类方法            | 静态方法            |
| :------ | :------- | :------------- | :-------------- |
| a = A() | a.foo(x) | a.class_foo(x) | a.static_foo(x) |
| A       | 不可用      | A.class_foo(x) | A.static_foo(x) |

## 类变量

是可在类的所有实例之间共享的值（也就是说，它们不是单独分配给每个实例的）。
```python
class people(object):
    name = 'jack'

p1 = people()
p2 = people()

p1.name = 'tom'  #p1.name 指向了'tom'
print(p1.name)  #输出 tom
print(p2.name   #输出 jack
people.name = 'marry'
print(p2.name,p1.name)  #输出 marry tom
```

```python
class people(object):
    name = ['jack']

p1 = people()
p2 = people()

p1.name.append('tom')   #改变的是内存的内容而不是地址
print(p1.name)      #输出 ['jack','tom']
print(p2.name)      #同上
people.name.append('marry') #输出 ['jack','tom','marry']
print(p2.name,p1.name)
```

## python 的自省

` hasattr(object, name)`

判断 object 是否有 name 这个方法。name 应该是个字符串。

`getattr(object, name[,default])`

获得 object 中为 name 的属性或者方法。不存在则打印 default。获取属性则直接打印，获取方法则打印地址（加括号可运行）。

`setattr(object, name, values)`

给对象的属性赋值，若属性不存在，先创建再赋值。

`isinstance（object，type）`

来判断一个 object 是否是一个已知的类型。

## 列表推导式

`[Expression for Variable in  list if (...)]`

举例：
```python
>>> a = [1,2,3,4,5,6,7,8,9,10]
>>> [x for x in a if x % 2 == 0]
[2, 4, 6, 8, 10]
```
### 多重列表推导
```python
a = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
b = [j for i in a for j in i]
print(b)    #[1, 2, 3, 4, 5, 6, 7, 8, 9]
```
把 [] 换为 () 可产生生成器（generator），换为{}可产生集合。

## 字典推导式

```python
d = {key: value for (key, value) in iterable}
```
举例：
```python
mcase = {'a': 10, 'b': 34}
mcase_frequency = {v: k for k, v in mcase.items()}
print mcase_frequency
#  Output: {10: 'a', 34: 'b'}
```

