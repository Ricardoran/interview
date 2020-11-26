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
• Basic inheritance
• Inheriting from built-ins 可以从Python本身的类去继承，比list
• Multiple inheritance
• Polymorphism and duck typing

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

# Java