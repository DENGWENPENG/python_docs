>本文包含以下内容
> 1.不定长参数(*args和**kw)

在我们之前定义一个函数时，可以定义通过位置参数，和关键字参数两种方式来进行传参，比如下面的例子
```python
def func(a,b,c):
    print(a,b,c)

#位置传参
func(1,2,3)  #这样往函数传参是按照位置来进行的，存在对应关系，即1 -> a  2 -> b  3 -> c 
#关键字传参
func(a=1,c=2,b=3)  #这种在传参时，指定了具体的参数名，就是关键字传参
```

在python中还有第三种传参方式————不定长参数
```python
def func(*args,**kw):
    #上面的参数名可以任意起，比如（def func(*a,**b)），但是前面的星号必须要有
    print(type(args),args)
    print(type(kw),kw)
#当我们去调用时
func(1,2,3)  #(1,2,3) 会以tuple的形式按顺序传给args
fun(a=1,b=2,c=3)  #当我们以关键字的形式进行传参时，(a=1,b=2,c=3)会以字典的形式传给kw
```
```python
>>> def func(*args,**kw):
>>>     print(args)
>>>     print(kw)
>>> func(1,2,a=1)
(1,2)
{"a":1}
```
这种不定长参数在我们去使用装饰器的时候格外方便
```python
def log(func):
    def wraps(*args,**kw):
        print("#############")
        func(*args,**kw)
        print("*************")
    return wraps

@log
def func1(a,b,c,d):
    print(a,b,c,d)

@log
def func2(a,b,c):
    print(a,b,c)
```
从上面的例子中可以发现，在我们的装饰器中，`wraps`函数使用了不定长参数这样可以保证我们的装饰器装饰下面的`func1`或者`func2`时，调用`func1`或者`func2`时，传入的参数都可以进入我们的装饰器中