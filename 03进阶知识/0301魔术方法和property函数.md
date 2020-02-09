> 本文包含以下内容
> 1. 魔术方法
> 2. property函数

# 魔术方法
在Python中，所有以“__”双下划线包起来的方法，都统称为“Magic Method”，中文称『魔术方法』,例如类中的初始化方法 `__init__()`
- `__init__`
- `__del__`
- `__call__`
- `__len__`
- `__str__`
- `__getattribute__`
- `__getattr__`
- `__setattr__`
- `__delattr__`

## `__init__`：构造函数
触发时机：实例化对象之后自动触发，在__new__之后执行。实例化对象其实包含两步，1：制作一个对象 2：为对象初始化操作
作用：为对象添加对象的所属成员
参数：self必填，接收当前对象，其他参数可根据实例化的传参决定
返回值：无
注意事项：无
```python
class Human:

    def __init__(self, name):
        print('init method for {}'.format(name))

    def eat(self):
        print('eat method')

    def run(self):
        print('run method')


human = Human('Jack')  # 执行这一步实例化对象human之后, 会触发 __init__, 打印出init method for Jack
```

## `__del__`：析构方法
触发时机：对象被系统回收的时候自动触发（del不一定触发）
作用：回收程序使用过程中的信息和变量等
参数：self必填，接收当前对象
返回值：无
注意事项：如果主动使用del（非__del__），则会在调用del的时候触发，而非程序最后，但也不是调用了del一定会触发__del__
```python
class Human:

    def __init__(self, name):
        print('init method for {}'.format(name))

    def __del__(self):
        print('del method')


    # 方法
    def eat(self):
        print('eat method')

    def run(self):
        print('run method')


human = Human('Jack')  # 执行这一步实例化对象human
human2 = human
del human # 此时不会触发__del__，在程序最后才会触发，如果没有human2 = human，则在此处调用del会触发__del__，程序最后则不会触发
print(1) # 在执行完这一句之后，会触发__del__，打印出del method，__del__总是会在程序最后被触发
```
## `__call__`
触发时机：将实例化对象当做函数调用的时候自动触发（函数其实也是对象，python中一切皆对象）
作用：常用语归结类或对象的操作步骤，方便后期操作
参数：self必填，接收当前对象
返回值：可有可无
注意事项：无
```python
class MakeCake:

    def buy_yuan_liao(self):
        print('购买原料')

    def huo_mian(self):
        print('和面')

    def fa_jiao(self):
        print('发酵')

    def hong_bei(self):
        print('烘焙')

    def __call__(self, *args, **kwargs):
        self.buy_yuan_liao()
        self.huo_mian()
        self.fa_jiao()
        self.hong_bei()
        print('蛋糕制作完成')


maek_cake = MakeCake() # 实例化一个类MakeCake的对象make_cake
maek_cake() # 把对象make_cake当做一个函数调用
```
## `__len__`
触发时机：使用len函数检测对象长度的时候自动触发
作用：使len函数可以直接检测对象中某个数据的长度
参数：self必填，接收当前对象，无其他参数
返回值：必须有，且必须是整形
注意事项：无
```python
class Human:

    def __init__(self, name):
        self.name = name

    def __len__(self):
        # 必须有返回值，而且必须是整形
        return len(self.name)


    # 方法
    def eat(self):
        print('eat method')

    def run(self):
        print('run method')


human = Human('Jack')
print(len(human)) #如果没有实现__len__, 则无法直接使用len(human), 否则报TypeError: object of type 'Human' has no len(), 实现了__len__后，可以根据需要

```
## `__str__`
触发时机：使用print打印对象的时候触发
作用：可以自定义打印对象时输出的内容
参数：self必填，接收当前对象
返回值：必须有，且一定是字符串类型
注意事项：除print之外，只用str()时也会触发该魔术方法
```python
class Human:

    def __init__(self, name):
        self.name = name

    def __str__(self):
        return 'Name: %s' % self.name

    # 方法
    def eat(self):
        print('eat method')

    def run(self):
        print('run method')


human = Human('Python')
print(human)   #如果没有__str__魔术方法，则打印出来的是类似于<__main__.Human object at 0x000001BFE3F39828>，这是继承自object的__str__方法
```

与属性相关的魔术方法
属性的访问顺序
- 调用__getattribute__
- 调用数据描述符 `__get__` `__set__`
- 调用当前对象的所属成员
- 调用类的所属成员
- 调用非数据描述符
- 调用父类的所属成员
- 调用__getattr__
## `__getattribute__`
触发时机：访问对象的成员属性的时候自动触发，无论该成员属性是否存在
作用：可以在用户访问成员属性的时候进行数据处理等操作，例如对敏感信息进行脱敏处理
参数：self接收当前对象，第二个参数接收访问的成员属性名称字符串
返回值：可有可无，没有的话就返回None
注意事项：在该魔术方法中，禁止使用 当前对象.成员 的方式（例如：self.name）访问成员，否则会发生递归次数过大的错误RecursionError: maximum recursion depth exceeded while calling a Python object
```python
class Human:

    def __init__(self):
        self.name = 'Python'
        self.sex = 'male'
        self.age = 18

    def __getattribute__(self, item):
        print('getattribute call by %s' % self)
        # return self.name   如果返回的是self.name，则会报错
        return object.__getattribute__(self,item)

    def eat(self):
        print('eat method is running')


human = Human()
print(human.name)
```
## `__getattr__`
触发时机：访问不存在的对象成员属性的时候自动触发
作用：防止访问不存在的对象成员的时候报错；为不存在的成员定义值
参数：self接收当前对象，第二个参数接收访问的成员属性名称字符串
返回值：可有可无，没有的话就返回None
注意事项：__getattribute__和__getattr__可以同时存在，如果同时存在，那么在访问不存在的值时，__getattribute__和__getattr__都会触发

```python
class Human:

    # 成员属性
    def __init__(self):
        self.name = 'Python'
        self.sex = 'male'
        self.age = 18

    def __getattr__(self, item):
        return 'error item'

    def eat(self):
        print('eat method is running')


human = Human()
print(human.name)
print(human.school)
```
## `__setattr__`
触发时机：添加或者修改对象成员的时候自动触发
作用：可以限制或者管理对象成员的添加或修改操作
参数：self接收当前对象，第二个参数接收设置的成员名称字符串，第三个参数接收设置的值
返回值：无
注意事项：在该魔术方法中，禁止使用 当前对象.成员=值 的方式（例如：self.name = 'Mike'）设置成员，否则会发生递归次数过大的错误RecursionError: maximum recursion depth exceeded while calling a Python object
```python
class Human:

    # 成员属性
    def __init__(self):
        self.name = 'Python'
        self.sex = 'male'
        self.age = 18

    def __setattr__(self, key, value):
        if key == 'school':   # 当设置的成员名称为school时，直接跳过，不设置
            pass
        else:
            object.__setattr__(self,key,value) #注意此处不可以用self.key = value

    def eat(self):
        print('eat method is running')


human = Human()
human.school = 'QH'
human.score = 99
print(human.score) # 输出 99
print(human.school)  # 报错，school不存在


```
## `__delattr__`
触发时机：删除对象成员的时候自动触发
作用：可以限制删除对象成员或者在删除时执行额外的工作
参数：self接收当前对象，第二个参数接收删除的成员名称字符串
返回值：可有可无，无的话则不会执行删除操作
注意事项：在该魔术方法中，禁止使用 del 当前对象.成员 的方式（例如：del self.name）删除成员，否则会发生递归次数过大的错误RecursionError: maximum recursion depth exceeded while calling a Python object
```python
class Human:

    def __init__(self):
        self.name = 'Python'
        self.sex = 'male'
        self.age = 18

    def __delattr__(self, item):
        if item == 'name': # 当删除的对象成员名称为name时，不执行删除操作，即限制了删除name成员
            pass
        else:
            # del self.sex  如果执行此行，则会报错，递归数过大
            return object.__delattr__(self,item)

    def eat(self):
        print('eat method is running')


human = Human()
del human.name  # 不会删除name属性
del human.sex  # 会删除sex属性
print(human.name)
print(human.sex)
```
以上内容来自于：简书
-----
# `property函数`

我们已经学了将类中的方法变为一个属性来进行调用

```python
class Stu:
    def __init__(self,name):
        self.__name = name

    @property
    def name(self):
        return self.__name
```
新的需求
我们既然可以把一个方法来当作一个属性来调用，那怎么来进行重新赋值？

property的高阶用法

```python
class Stu:
    def __init__(self,name):
        self.__name = name

    @property
    def name(self):
        return self.__name.lower()
    
    @name.setter
    def name(self,value):
        self.__name = value.lower()
    
    @name.deleter
    def name(self):
        del self.__name

s = Stu("Jack")
print(s.name)
s.name = "Tom"
print(s.name)
del s.name
print(s.name)
```
上面是使用了装饰器，下面则是使用了property函数
```python
class Stu:
    def __init__(self,name):
        self.__name = name

    def get_name(self):
        return self.__name.lower()
    
    def set_name(self,value):
        self.__name = value.lower()
    
    def del_name(self):
        del self.__name

    name = property(get_name,set_name,del_name,"这是name")

s = Stu("Jack")
print(s.name)
s.name = "Tom"
print(s.name)
del s.name
print(s.name)
```