---
title: 深入Python——查漏补缺(二)
date: 2019-10-29 18:43:48
categories: Python工程
tags: Python廖雪峰、Python查漏补缺
mathjax: true
---

* 和静态语言不同，Python允许对实例变量绑定任何数据，也就是说，对于两个实例变量，虽然它们都是同一个类的不同实例，但拥有的变量名称都可能不同。

* 如果要让内部属性不被外部访问，可以把属性的名称前加上两个下划线\_\_，在Python中，实例的变量名如果以\__开头，就变成了一个私有变量（private），只有内部可以访问，外部不能访问。<!--more-->需要注意的是，在Python中，变量名类似\_\_xxx\_\_的，也就是以双下划线开头，并且以双下划线结尾的，是特殊变量，特殊变量是可以直接访问的，不是private变量。有些时候，以一个下划线开头的实例变量名，比如_name，这样的实例变量外部是可以访问的，但是，按照约定俗成的规定，看到这样的变量时，意思就是，“虽然我可以被访问，但是，请把我视为私有变量，不要随意访问”。

* 在继承关系中，如果一个实例的数据类型是某个子类，那它的数据类型也可以被看做是父类。但是，反过来就不行。多态的好处就是，当我们需要传入Dog、Cat、Tortoise……时，我们只需要接收Animal类型就可以了，因为Dog、Cat、Tortoise……都是Animal类型，然后，按照Animal类型进行操作即可。由于Animal类型有run()方法，因此，传入的任意类型，只要是Animal类或者子类，就会自动调用实际类型的run()方法，这就是多态的意思。对于一个变量，我们只需要知道它是Animal类型，无需确切地知道它的子类型，就可以放心地调用run()方法，而具体调用的run()方法是作用在Animal、Dog、Cat还是Tortoise对象上，由运行时该对象的确切类型决定，这就是多态真正的威力：调用方只管调用，不管细节，而当我们新增一种Animal的子类时，只要确保run()方法编写正确，不用管原来的代码是如何调用的。这就是著名的“开闭”原则：对扩展开放：允许新增Animal子类；对修改封闭：不需要修改依赖Animal类型的run_twice()等函数。

* 对于静态语言（例如Java）来说，如果需要传入Animal类型，则传入的对象必须是Animal类型或者它的子类，否则，将无法调用run()方法；对于Python这样的动态语言来说，则不一定需要传入Animal类型。我们只需要保证传入的对象有一个run()方法就可以了。这就是动态语言的“鸭子类型”，它并不要求严格的继承体系，一个对象只要“看起来像鸭子，走起路来像鸭子”，那它就可以被看做是鸭子。

* type( )函数返回对应的Class类型，isinstance( )判断的是一个对象是否是该类型本身，或者位于该类型的父继承链上。如果要获得一个对象的所有属性和方法，可以使用dir()函数，它返回一个包含字符串的list，比如，获得一个str对象的所有属性和方法。己写的类，如果也想用len(myObj)的话，就自己写一个\_\_len\_\_()方法。仅仅把属性和方法列出来是不够的，配合getattr()、setattr()以及hasattr()，我们可以直接操作一个对象的状态。getattr()，如果试图获取不存在的属性，会抛出AttributeError的错误，可以传入一个default参数，如果属性不存在，就返回默认值，也可以获得对象的方法。通过内置的一系列函数，可以对任意一个Python对象进行剖析，拿到其内部的数据。要注意的是，只有在不知道对象信息的时候，才会去获取对象信息。

* 实例属性属于各个实例所有，互不干扰；类属性属于类所有，所有实例共享一个属性；不要对实例属性和类属性使用相同的名字，因为相同名称的实例属性将屏蔽掉类属性，但是当你删除实例属性后，再使用相同的名称，访问到的将是类属性。

* 可以动态的给Python对象和类动态地绑定方法，但想要限制实例的属性，比如，只允许对Student实例添加name和age属性，Python允许在定义class的时候，定义一个特殊的\_\_slots\_\_变量，来限制该class实例能添加的属性。使用\_\_slots\_\_要注意，\_\_slots\_\_定义的属性仅对当前类实例起作用，对继承的子类是不起作用的，除非在子类中也定义 \_\_slots\_\_，这样，子类实例允许定义的属性就是自身的\_\_slots\_\_加上父类的\_\_slots\_\_。

* 既能检查参数，又可以用类似属性这样简单的方式来访问类的变量，Python内置的@property装饰器就是负责把一个方法变成属性调用的。还可以定义只读属性，只定义getter方法，不定义setter方法就是一个只读属性。

* 通过多重继承，一个子类就可以同时获得多个父类的所有功能。在设计类的继承关系时，通常，主线都是单一继承下来的，例如，Ostrich继承自Bird。但是，如果需要“混入”额外的功能，通过多重继承就可以实现，比如，让Ostrich除了继承自Bird外，再同时继承Runnable。这种设计通常称之为MixIn。MixIn的目的就是给一个类增加多个功能，这样，在设计类的时候，我们优先考虑通过多重继承来组合多个MixIn的功能，而不是设计多层次的复杂的继承关系。Python自带的很多库也使用了MixIn。举个例子，Python自带了TCPServer和UDPServer这两类网络服务，而要同时服务多个用户就必须使用多进程或多线程模型，这两种模型由ForkingMixIn和ThreadingMixIn提供。通过组合，我们就可以创造出合适的服务来。由于Python允许使用多重继承，因此，MixIn就是一种常见的设计。只允许单一继承的语言（如Java）不能使用MixIn的设计。

* 定制类，打印一个实例：\_\_str\_\_( )、\_\_repr\_\_( )，如果一个类想被用于for ... in循环，类似list或tuple那样，就必须实现一个\_\_iter\_\_( )方法，该方法返回一个迭代对象，然后，Python的for循环就会不断调用该迭代对象的\_\_next\_\_( )方法拿到循环的下一个值，直到遇到StopIteration错误时退出循环。要表现得像list那样按照下标取出元素，需要实现\_\_getitem\_\_( )方法。

* ```python
  class Fib(object):
      def __init__(self):
          self.a, self.b = 0, 1 # 初始化两个计数器a，b
  
      def __iter__(self):
          return self # 实例本身就是迭代对象，故返回自己
  
      def __next__(self):
          self.a, self.b = self.b, self.a + self.b # 计算下一个值
          if self.a > 100000: # 退出循环的条件
              raise StopIteration()
          return self.a # 返回下一个值
        
  for n in Fib():
      print(n)
  ```

* Python还有另一个机制，那就是写一个\_\_getattr\_\_()方法，动态返回一个属性，返回函数也是完全可以的。这实际上可以把一个类的所有属性和方法调用全部动态化处理了，不需要任何特殊手段。这种完全动态调用的特性，可以针对完全动态的情况作调用。

* 任何类，只需要定义一个\_\_call\_\_()方法，就可以直接对实例进行调用。\_\_call\_\_()还可以定义参数。对实例进行直接调用就好比对一个函数进行调用一样，所以你完全可以把对象看成函数，把函数看成对象，因为这两者之间本来就没啥根本的区别。通过callable()函数，我们就可以判断一个对象是否是“可调用”对象。

* Python 使用枚举类，既可以用成员名称引用枚举常量，又可以直接根据value的值获得枚举常量，`Enum`可以把一组相关常量定义在一个class中，且class不可变，而且成员可以直接比较。

* Python 使用元类，class的定义是运行时动态创建的，而创建class的方法就是使用type()函数，type()函数既可以返回一个对象的类型，又可以创建出新的类型。要创建一个class对象，type()函数依次传入3个参数：class的名称；继承的父类集合，注意Python支持多重继承，如果只有一个父类，别忘了tuple的单元素写法；class的方法名称与函数绑定。通过type()函数创建的类和直接写class是完全一样的，因为Python解释器遇到class定义时，仅仅是扫描一下class定义的语法，然后调用type()函数创建出class。动态语言本身支持运行期动态创建类，这和静态语言有非常大的不同，要在静态语言运行期创建类，必须构造源代码字符串再调用编译器，或者借助一些工具生成字节码实现，本质上都是动态编译，会非常复杂。

* 除了使用type()动态创建类以外，要控制类的创建行为，还可以使用metaclass。metaclass，直译为元类，简单的解释就是：当我们定义了类以后，就可以根据这个类创建出实例，所以：先定义类，然后创建实例。但是如果我们想创建出类呢？那就必须根据metaclass创建出类，所以：先定义metaclass，然后创建类。连接起来就是：先定义metaclass，就可以创建类，最后创建实例。所以，metaclass允许你创建类或者修改类。换句话说，你可以把类看成是metaclass创建出来的“实例”。

* 需要通过metaclass修改类定义的。ORM就是一个典型的例子。ORM全称“Object Relational Mapping”，即对象-关系映射，就是把关系数据库的一行映射为一个对象，也就是一个类对应一个表，这样，写代码更简单，不用直接操作SQL语句。要编写一个ORM框架，所有的类都只能动态定义，因为只有使用者才能根据表的结构定义出对应的类来。

* metaclass可以隐式地继承到子类，但子类自己却感觉不到。metaclass是Python中非常具有魔术性的对象，它可以改变类创建时的行为。这种强大的功能使用起来务必小心。


























