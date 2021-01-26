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

## implements and extends
1. implements defines API signature defination
interface Collection: add(E), size(), contains(Object)
2. The relationship between interface and class is not necessiarily strong 
class House implements AirConditioning
class Car implements Air...

implements 可以表示为has的关系
extends 可以表示为is的关系，更紧密

## abstract class and concrete class
当你需要给类去实例化的时候，用concrete，不需要就用abs
一个类只要有1或多个抽象方法，就是抽象类，空方法不算抽象方法。
interface里面只能有抽象方法，而且必须是public
```java
// abs
class Car{
    public void Drive()
}
// 实例类
class gasCar extends Car{

}
// 实例类
class eleCar extends Car{

}
```

## inheritance
子类继承父类的一切filed，包括private，只是不可见，但是可以通过父类的public getter setter去看和更改
 
## polymophism
reference类型决定哪些函数能调用，Instance类型决定调用的具体版本和行为
哪些类下的函数能调用，取决于reference的类型 
对于被override的函数，调用哪个版本，取决于object类型


## overload and override
override
一定要在不同类下
要求签名是完全一样的
运行时解析

overload
必须在同一个类里面
signature绝对不同
编译时解析

# interface and abstract class
一个类里面没有函数体就是abstract

```java
abstruct class A{
    public abstract void f(); // f是抽象函数， g不是
    public void g(){

    }
}
```

interface里面只能有虚函数


1. java允许多实现，不允许多实现
2. 一个abstract class可能有实函数
3. abstract class 继承是 is a的关系， interface是has a function or can do的关系


## 单例
只有一个对象的类


## exception
check exception 编译时错误

## Java 垃圾回收机制
Java编程思想 161
“引用记数 (reference counting)”
是一种简单但速度很慢的垃圾回收技术。每个对象都含有一个引用记 数器，当有引用连接至对象时，引用计数加 1。当引用离开作用域或被置为 null 时，引用计 数减 1。虽然管理引用记数的开销不大，但需要在整个程序生命周期中持续地开销。垃圾回 收器会在含有全部对象的列表上遍历，当发现某个对象的引用计数为 0 时，就释放其占用的
空间。这种方法有个缺陷，如果对象之间存在循环引用，可能会出现“对象应该被回收，但 引用计数却不为零”的情况。对垃圾回收器而言，定位这样存在交互引用的对象组所需的工 作量极大。引用记数常用来说明垃圾收集的工作方式，似乎从未被应用于任何一种 Java 虚拟机实现中。

在一些更快的模式中，垃圾回收器并非基于引用记数技术。它们依据的思想是:对任何“活” 的对象，一定能最终追溯到其存活在堆栈或静态存储区之中的引用。这个引用链条可能会穿 过数个对象层次。由此，如果你从堆栈和静态存储区开始，遍历所有的引用，就能找到所有 “活”的对象。对于发现的每个引用，你必须追踪它所引用的对象，然后是此对象包含的所 有引用，如此反复进行，直到“根源于堆栈和静态存储区的引用”所形成的网络全部被访问 为止。你所访问过的对象必须都是“活”的。注意，这就解决了“存在交互引用的整体对象” 的问题，这些对象根本不会被发现，因此也就被自动回收了。

在这种方式下，Java 虚拟机将采用一种“自适应”的垃圾回收技术。至于如何处理找到的存 活对象，取决于不同的 Java 虚拟机实现。有一种作法名为“停止——复制”(stop-and-copy)。 这意味着，先暂停程序的运行，(所以它不属于后台回收模式)，然后将所有存活的对象从当 前堆复制到另一个堆，没有被复制的全部都是垃圾。当对象被复制到新堆时，它们是一个挨 着一个的，所以新堆保持紧凑排列，然后就可以按前述方法简单、直接地分配新空间了。
当把对象从一处搬到另一处时，所有指向它的那些引用都必须修正。位于堆或静态存储区的 引用可以直接被修正，但可能还有其它指向这些对象的引用，它们在遍历的过程中才能被找 到。你可以想象有个表格，将旧地址映射至新地址，这样就可以在遍历的同时进行修改了。
对于这种所谓的“复制式回收器”而言，有两个原因会降低效率。首先，你得有两个堆，然 后你得在这两个分离的堆之间来回捣腾，从而得维护比实际需要多一倍的空间。某些 Java 虚拟机对此问题的处理方式是，按需从堆中分配几块较大的内存，复制动作发生在这些大块 内存之间。

第二个问题在于复制。你的程序进入稳定状态之后，可能只会产生少量垃圾，甚至没有垃圾。 尽管如此，复制式回收器仍然会将所有内存自一处复制到另一处，这很浪费。为了避免这种 情形，一些 Java 虚拟机会进行检查:要是没有新垃圾产生，就会转换到另一种工作模式(此 即“自适应”)。这种模式称为“标记——清扫(mark-and-sweep)”， Sun 公司早期版本的 Java 虚拟机使用了这种技术。对一般用途而言，“标记——清扫”方式速度相当慢，但是当 你知道你只会产生少量垃圾甚至不会产生垃圾时，它的速度就很快了。
“标记——清扫”所依据的思路同样是从堆栈和静态存储区出发，遍历所有的引用，进而找 出所有存活的对象。每当它找到一个存活对象，就会给对象设一个标记，这个过程中不会回 收任何对象。只有全部标记工作完成的时候，清除动作才会开始。在清处过程中，没有标记 的对象将被释放，不会发生任何复制动作。所以剩下的堆空间是不连续的，垃圾回收器要是 希望得到连续空间的话，就得重新整理剩下的对象。