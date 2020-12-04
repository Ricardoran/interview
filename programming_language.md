# Python
https://github.com/taizilongxu/interview_python

## Python 初始化
constructor 可以用 "__new__"

## Python 封装
```python
class SecretString:
       '''A not-at-all secure way to store a secret string.'''
       def __init__(self, plain_string, pass_phrase):
           self.__plain_string = plain_string
           self.__pass_phrase = pass_phrase
       def decrypt(self, pass_phrase):
           '''Only show the string if the pass_phrase is correct.'''
           if pass_phrase == self.__pass_phrase:
               return self.__plain_string
           else:
 return ''

# 当我们给变量加 __前缀的时候，说明是私有的，不可访问，除非符合要求
>>> secret_string = SecretString("ACME: Top Secret", "antwerp")
>>> print(secret_string.decrypt("antwerp"))
ACME: Top Secret

>>> print(secret_string.__plain_text)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'SecretString' object has no attribute
'__plain_text'


# 但可以轻松破解，因为 _<classname>代表内部的访问，外部访问会被阻止
>>> print(secret_string._SecretString__plain_string)
ACME: Top Secret

 ```


## Python 继承
* Basic inheritance
* Inheriting from built-ins 可以从Python本身的类去继承，比list
* Multiple inheritance
* Polymorphism and duck typing

### Overriding and super
任何方法都可以被重写，且默认会被重写，因此重点是如何保证一些super class方法不被重写
```python
# override
class Friend(Contact):
    def __init__(self, name, email, phone):
        self.name = name
        self.email = email
        self.phone = phone

# not override
# it returns the object as an instance of the parent class, allowing us to call the parent method directly:
class Friend(Contact):
       def __init__(self, name, email, phone):
           super().__init__(name, email)
           self.phone = phone
```

```python
class Animal(object):
    def run(self):
        print('Animal is running...')

class Dog(Animal):
    pass

class Cat(Animal):
    pass

dog = Dog()
dog.run()

cat = Cat()
cat.run()
# 继承有什么好处？最大的好处是子类获得了父类的全部功能。由于Animial实现了run()方法，因此，Dog和Cat作为它的子类，什么事也没干，就自动拥有了run()方法
```

# 多继承
```python
class Animal(object):
    pass

# 大类:
class Mammal(Animal):
    pass

class Bird(Animal):
    pass

# 各种动物:
class Dog(Mammal):
    pass

class Bat(Mammal):
    pass

class Parrot(Bird):
    pass

class Ostrich(Bird):
    pass

# 现在，我们要给动物再加上Runnable和Flyable的功能，只需要先定义好Runnable和Flyable的类：

class Runnable(object):
    def run(self):
        print('Running...')

class Flyable(object):
    def fly(self):
        print('Flying...')

对于需要Runnable功能的动物，就多继承一个Runnable，例如Dog：
class Dog(Mammal, Runnable):
    pass

class Bat(Mammal, Flyable):
    pass
```

```python
'''
Python自带的很多库也使用了MixIn。举个例子，Python自带了TCPServer和UDPServer这两类网络服务，而要同时服务多个用户就必须使用多进程或多线程模型，这两种模型由ForkingMixIn和ThreadingMixIn提供。通过组合，我们就可以创造出合适的服务来。
这样一来，我们不需要复杂而庞大的继承链，只要选择组合不同的类的功能，就可以快速构造出所需的子类。
'''

比如，编写一个多进程模式的TCP服务，定义如下：

class MyTCPServer(TCPServer, ForkingMixIn):
    pass
编写一个多线程模式的UDP服务，定义如下：

class MyUDPServer(UDPServer, ThreadingMixIn):
    pass
如果你打算搞一个更先进的协程模型，可以编写一个CoroutineMixIn：

class MyTCPServer(TCPServer, CoroutineMixIn):
    pass
```

## Python 多态
different behaviors happen depending on which subclass is being used, without having to explicitly know what the subclass actually is


https://www.liaoxuefeng.com/wiki/1016959663602400/1017497232674368
多态的好处就是，当我们需要传入Dog、Cat、Tortoise……时，我们只需要接收Animal类型就可以了，因为Dog、Cat、Tortoise……都是Animal类型，然后，按照Animal类型进行操作即可。由于Animal类型有run()方法，因此，传入的任意类型，只要是Animal类或者子类，就会自动调用实际类型的run()方法，这就是多态的意思。


```python
class Animal(object):
    def run(self):
        print('Animal is running...')

class Dog(Animal):
    def run(self):
        print('Dog is running...')

class Cat(Animal):
    def run(self):
        print('Cat is running...')

def run_twice(animal):
    animal.run()
    animal.run()

a = Animal()
d = Dog()
c = Cat()

print('a is Animal?', isinstance(a, Animal))
print('a is Dog?', isinstance(a, Dog))
print('a is Cat?', isinstance(a, Cat))

print('d is Animal?', isinstance(d, Animal))
print('d is Dog?', isinstance(d, Dog))
print('d is Cat?', isinstance(d, Cat))
run_twice(c)

```

## python exception
```python
# When an exception is raised, it appears to stop program execution immediately.
class EvenOnly(list):
       def append(self, integer):
           if not isinstance(integer, int):
               raise TypeError("Only integers can be added")
           if integer % 2:
               raise ValueError("Only even numbers can be added")
           super().append(integer)


>>> e = EvenOnly()
>>> e.append("a string")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "even_integers.py", line 7, in add
    raise TypeError("Only integers can be added")
TypeError: Only integers can be added
>>> e.append(3)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "even_integers.py", line 9, in add
    raise ValueError("Only even numbers can be added")
ValueError: Only even numbers can be added
>>> e.append(2)
```

```python
 import random
   some_exceptions = [ValueError, TypeError, IndexError, None]
   try:
       choice = random.choice(some_exceptions)
       print("raising {}".format(choice))
       if choice:
           raise choice("An error")
   except ValueError:
       print("Caught a ValueError")
   except TypeError:
       print("Caught a TypeError")
   except Exception as e:
       print("Caught some other error: %s" %
           ( e.__class__.__name__))
else:
       print("This code called if there is no exception")
finally:
       print("This cleanup code is always called")

```

## python test
```python
# Python内置的“文档测试”（doctest）模块可以直接提取注释中的代码并执行测试。
# mydict2.py
class Dict(dict):
    '''
    Simple dict but also support access as x.y style.

    >>> d1 = Dict()
    >>> d1['x'] = 100
    >>> d1.x
    100
    >>> d1.y = 200
    >>> d1['y']
    200
    >>> d2 = Dict(a=1, b=2, c='3')
    >>> d2.c
    '3'
    >>> d2['empty']
    Traceback (most recent call last):
        ...
    KeyError: 'empty'
    >>> d2.empty
    Traceback (most recent call last):
        ...
    AttributeError: 'Dict' object has no attribute 'empty'
    '''
    def __init__(self, **kw):
        super(Dict, self).__init__(**kw)

    def __getattr__(self, key):
        try:
            return self[key]
        except KeyError:
            raise AttributeError(r"'Dict' object has no attribute '%s'" % key)

    def __setattr__(self, key, value):
        self[key] = value

if __name__=='__main__':
    import doctest
    doctest.testmod()
```

## python 进程线程
Unix/Linux操作系统提供了一个fork()系统调用，它非常特殊。普通的函数调用，调用一次，返回一次，但是fork()调用一次，返回两次，因为操作系统自动把当前进程（称为父进程）复制了一份（称为子进程），然后，分别在父进程和子进程内返回。

子进程永远返回0，而父进程返回子进程的ID。这样做的理由是，一个父进程可以fork出很多子进程，所以，父进程要记下每个子进程的ID，而子进程只需要调用getppid()就可以拿到父进程的ID。

```python
import os

print('Process (%s) start...' % os.getpid())
# Only works on Unix/Linux/Mac:
pid = os.fork()
if pid == 0:
    print('I am child process (%s) and my parent is %s.' % (os.getpid(), os.getppid()))
else:
    print('I (%s) just created a child process (%s).' % (os.getpid(), pid)
```
Process (876) start...
I (876) just created a child process (877).
I am child process (877) and my parent is 876.

### multiprocessing
```python
from multiprocessing import Process
import os
# multiprocessing模块提供了一个Process类来代表一个进程对象，下面的例子演示了启动一个子进程并等待其结束：
# 子进程要执行的代码
def run_proc(name):
    print('Run child process %s (%s)...' % (name, os.getpid()))

if __name__=='__main__':
    print('Parent process %s.' % os.getpid())
    p = Process(target=run_proc, args=('test',))
    print('Child process will start.')
    p.start()
    p.join()
    print('Child process end.')
```
Parent process 928.
Child process will start.
Run child process test (929)...
Process end.
创建子进程时，只需要传入一个执行函数和函数的参数，创建一个Process实例，用start()方法启动，这样创建进程比fork()还要简单。
join()方法可以等待子进程结束后再继续往下运行，通常用于进程间的同步。


如果要启动大量的子进程，可以用进程池的方式批量创建子进程：
```python
from multiprocessing import Pool
import os, time, random

def long_time_task(name):
    print('Run task %s (%s)...' % (name, os.getpid()))
    start = time.time()
    time.sleep(random.random() * 3)
    end = time.time()
    print('Task %s runs %0.2f seconds.' % (name, (end - start)))

if __name__=='__main__':
    print('Parent process %s.' % os.getpid())
    p = Pool(4)
    for i in range(5):
        p.apply_async(long_time_task, args=(i,))
    print('Waiting for all subprocesses done...')
    p.close()
    p.join()
    print('All subprocesses done.')

Parent process 669.
Waiting for all subprocesses done...
Run task 0 (671)...
Run task 1 (672)...
Run task 2 (673)...
Run task 3 (674)...
Task 2 runs 0.14 seconds.
Run task 4 (673)...
Task 1 runs 0.27 seconds.
Task 3 runs 0.86 seconds.
Task 0 runs 1.41 seconds.
Task 4 runs 1.91 seconds.
All subprocesses done.
```
对Pool对象调用join()方法会等待所有子进程执行完毕，调用join()之前必须先调用close()，调用close()之后就不能继续添加新的Process了。
请注意输出的结果，task 0，1，2，3是立刻执行的，而task 4要等待前面某个task完成后才执行，这是因为Pool的默认大小在我的电脑上是4，因此，最多同时执行4个进程。这是Pool有意设计的限制，并不是操作系统的限制。如果改成：

p = Pool(5)
就可以同时跑5个进程。

由于Pool的默认大小是CPU的核数，如果你不幸拥有8核CPU，你要提交至少9个子进程才能看到上面的等待效果。




## python 进程间通信
Process之间肯定是需要通信的，操作系统提供了很多机制来实现进程间的通信。Python的multiprocessing模块包装了底层的机制，提供了Queue、Pipes等多种方式来交换数据。
```python
from multiprocessing import Process, Queue
import os, time, random

# 写数据进程执行的代码:
def write(q):
    print('Process to write: %s' % os.getpid())
    for value in ['A', 'B', 'C']:
        print('Put %s to queue...' % value)
        q.put(value)
        time.sleep(random.random())

# 读数据进程执行的代码:
def read(q):
    print('Process to read: %s' % os.getpid())
    while True:
        value = q.get(True)
        print('Get %s from queue.' % value)

if __name__=='__main__':
    # 父进程创建Queue，并传给各个子进程：
    q = Queue()
    pw = Process(target=write, args=(q,))
    pr = Process(target=read, args=(q,))
    # 启动子进程pw，写入:
    pw.start()
    # 启动子进程pr，读取:
    pr.start()
    # 等待pw结束:
    pw.join()
    # pr进程里是死循环，无法等待其结束，只能强行终止:
    pr.terminate()
```

# Java
Java中其实还是值传递的，只不过对于对象参数，值的内容是对象的引用。

```java
```