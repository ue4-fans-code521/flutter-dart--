# dart中的对象

## 类和对象

> Dart是一个面向对象的语言，面向对象中非常重要的概念就是类，类产生了对象。
>
> 这一节，我们就具体来学习类和对象，但是Dart对类进行了很多其他语言没有的特性，所以，这里我会花比较长的篇幅来讲解。

### 3.1. 类的定义

在Dart中，定义类用`class关键字`。

类通常有两部分组成：成员（member）和方法（method）。

定义类的伪代码如下：

```dart
class 类名 {
  类型 成员名;
  返回值类型 方法名(参数列表) {
    方法体
  }
}
```

编写一个简单的Person类：

* **这里有一个注意点:** 我们在方法中使用属性(成员/实例变量)时，`并没有加this`；
* Dart的开发风格中，在方法中通常使用属性时，会`省略this`，但是有`命名冲突`时，`this不能省略`；

```dart
class Person {
  String name;

  eat() {
    print('$name在吃东西');
  }
}
```

我们来使用这个类，创建对应的对象：

* 注意：从Dart2开始，new关键字可以省略。

```dart
main(List<String> args) {
  // 1.创建类的对象
  var p = new Person(); // 直接使用Person()也可以创建

  // 2.给对象的属性赋值
  p.name = 'why';

  // 3.调用对象的方法
  p.eat();
}
```

### 3.2. 构造方法

#### 3.2.1. 普通构造方法

我们知道, 当通过类创建一个对象时，会调用这个类的构造方法。

* 当类中`没有明确指定构造方法`时，将默认拥有一个`无参的构造方法`。
* 前面的Person中我们就是在调用这个构造方法.

我们也可以根据自己的需求，定义自己的构造方法:

* **注意一：**当有了自己的构造方法时，`默认的构造方法将会失效`，不能使用
  * 当然，你可能希望明确的写一个默认的构造方法，但是会和我们自定义的构造方法冲突；
  * 这是因为Dart本身`不支持函数的重载`（名称相同, 参数不同的方式）。
* **注意二：**这里我还实现了toString方法

```dart
class Person {
  String name;
  int age;

  Person(String name, int age) {
    this.name = name;
    this.age = age;
  }

  @override
  String toString() {
    return 'name=$name age=$age';
  }
}
```

另外，在实现构造方法时，通常做的事情就是通过**`参数`**给**`属性`**赋值

为了简化这一过程, Dart提供了一种更加简洁的`语法糖形式`.

上面的构造方法可以优化成下面的写法：

```dart
  Person(String name, int age) {
    this.name = name;
    this.age = age;
  }
  // 等同于
  Person(this.name, this.age);
```

#### 3.2.2. 命名构造方法

但是在开发中, 我们确实希望实现更多的构造方法，怎么办呢？

* 因为不支持方法（函数）的重载，所以我们没办法创建相同名称的构造方法。

我们需要使用**命名构造方法**:

```dart
class Person {
  String name;
  int age;

  Person() {
    name = '';
    age = 0;
  }
    // 命名构造方法
  Person.withArgments(String name, int age) {
    this.name = name;
    this.age = age;
  }

  @override
  String toString() {
    return 'name=$name age=$age';
  }
}

// 创建对象
var p1 = new Person();
print(p1);
var p2 = new Person.withArgments('why', 18);
print(p2);
```

在之后的开发中, 我们也可以利用命名构造方法，提供更加便捷的创建对象方式:

* 比如开发中，我们需要经常将一个Map转成对象，可以提供如下的构造方法

```dart
  // 新的构造方法
    Person.fromMap(Map<String, Object> map) {
    this.name = map['name'];
    this.age = map['age'];
  }

    // 通过上面的构造方法创建对象
  var p3 = new Person.fromMap({'name': 'kobe', 'age': 30});
  print(p3);
```

#### 3.2.3. 初始化列表

我们来重新定义一个类Point, 传入x/y，可以得到它们的距离distance:

```dart
class Point {
  final num x;
  final num y;
  final num distance;

  // 错误写法
  // Point(this.x, this.y) {
  //   distance = sqrt(x * x + y * y);
  // }

  // 正确的写法
  Point(this.x, this.y) : distance = sqrt(x * x + y * y);
}
```

上面这种初始化变量的方法, 我们称之为`初始化列表(Initializer list)`

#### 3.2.4. 重定向构造方法

在某些情况下, 我们希望在一个构造方法中去调用另外一个构造方法, 这个时候可以使用`重定向构造方法`：

* 在一个构造函数中，去调用另外一个构造函数（注意：是在冒号后面使用this调用）

```dart
class Person {
  String name;
  int age;

  Person(this.name, this.age);
  Person.fromName(String name) : this(name, 0);
}
```

#### 3.2.5. 常量构造方法

在某些情况下，`传入相同值时`，我们希望`返回同一个对象`，这个时候，可以使用常量构造方法.

默认情况下，创建对象时，即使传入相同的参数，创建出来的也不是同一个对象，看下面代码:

* 这里我们使用`identical(对象1, 对象2)`函数来判断两个对象是否是同一个对象:

```dart
main(List<String> args) {
  var p1 = Person('why');
  var p2 = Person('why');
  print(identical(p1, p2)); // false
}

class Person {
  String name;

  Person(this.name);
}
```

但是, 如果将构造方法前加`const进行修饰`，那么可以保证同一个参数，创建出来的对象是相同的

* 这样的构造方法就称之为`常量构造方法`。

```dart
main(List<String> args) {
  var p1 = const Person('why');
  var p2 = const Person('why');
  print(identical(p1, p2)); // true
}

class Person {
  final String name;

  const Person(this.name);
}
```

常量构造方法有一些注意点:

* **注意一：**拥有常量构造方法的类中，所有的**成员变量必须是final修饰**的.
* **注意二:** 为了可以通过常量构造方法，创建出相同的对象，不再使用 **new**关键字，而是使用const关键字
  * 如果是将结果赋值给const修饰的标识符时，const可以省略.

#### 3.2.6. 工厂构造方法

Dart提供了factory关键字, 用于通过工厂去获取对象

```dart
main(List<String> args) {
  var p1 = Person('why');
  var p2 = Person('why');
  print(identical(p1, p2)); // true
}

class Person {
  String name;

  static final Map<String, Person> _cache = <String, Person>{};

  factory Person(String name) {
    if (_cache.containsKey(name)) {
      return _cache[name];
    } else {
      final p = Person._internal(name);
      _cache[name] = p;
      return p;
    }
  }

  Person._internal(this.name);
}
```

### 3.3. setter和getter

> * 默认情况下，Dart中类定义的属性是可以直接被外界访问的。
> * 但是某些情况下，我们希望监控这个`类的属性`被访问的过程，这个时候就可以使用`setter和getter`了
> * ==get 修饰的方法就变成了一个属性了,不能用普通的方法进行调用是不再有参数列表的()==
> * 同样的,==set修饰的方法编程为一个类中属性了==,通过class.xxx=" 参数"的方式进行调用赋值

```dart
main(List<String> args) {
  final d = Dog("黄色");
  d.setColor = "黑色";
  print(d.getColor);
}

class Dog {
  String color;

  String get getColor {
    return color;
  }
  set setColor(String color) {
    this.color = color;
  }

  Dog(this.color);
}
```

### 3.4. 类的继承

> * 面向对象的其中一大特性就是继承，继承不仅仅可以`减少我们的代码量`，也是`多态的使用前提`。
>
> * Dart中的继承使用`extends关键字`，子类中使用super来访问父类。
>
> * 父类中的所有成员变量和方法都会被继承,，但是构造方法除外。

```dart
main(List<String> args) {
  var p = new Person();
  p.age = 18;
  p.run();
  print(p.age);
}

class Animal {
  int age;

  run() {
    print('在奔跑ing');
  }
}

class Person extends Animal {

}
```

子类可以`拥有自己的成员变量,` 并且可以`对父类的方法进行重写`：

```dart
class Person extends Animal {
  String name;

  @override
  run() {
    print('$name在奔跑ing');
  }
}
```

子类中可以调用父类的构造方法，对某些属性进行初始化：

* 子类的构造方法在执行前，将隐含调用父类的`无参默认构造方法`（没有参数且与类同名的构造方法）。
* 如果父类没有`无参默认构造方法`，则子类的构造方法必须在初始化列表中通过`super`显式调用父类的某个构造方法。

```dart
class Animal {
  int age;

  Animal(this.age);

  run() {
    print('在奔跑ing');
  }
}

class Person extends Animal {
  String name;

  Person(String name, int age) : name=name, super(age);

  @override
  run() {
    print('$name在奔跑ing');
  }

  @override
  String toString() {
    return 'name=$name, age=$age';
  }
}
```

### 3.5. 抽象类

> 我们知道，继承是多态使用的前提。
>
> 所以在定义很多通用的**`调用接口`**时, 我们通常会让调用者`传入父类`，通过多态来实现更加灵活的调用方式。
>
> 但是，父类本身可能并不需要对某些方法进行具体的·实现，所以父类中定义的方法,，我们可以定义为**抽象方法**。
>
> 什么是 **抽象方法**? 在Dart中没有具体实现的方法(没有方法体)，就是抽象方法。
>
> ==抽象方法，必须存在于抽象类中。==
>
> ==抽象类是使用**`abstract`**声明的类。==

下面的代码中, Shape类就是一个抽象类, 其中包含一个抽象方法.

```dart
abstract class Shape {
  getArea();
}

class Circle extends Shape {
  double r;

  Circle(this.r);

  @override
  getArea() {
    return r * r * 3.14;
  }
}

class Reactangle extends Shape {
  double w;
  double h;

  Reactangle(this.w, this.h);

  @override
  getArea() {
    return w * h;
  }
}
```

注意事项:

* **注意一：**==抽象类不能实例化==.
* **注意二：**抽象类中的==抽象方法必须被子类实现==, ==抽象类中的已经被实现方法, 可以不被子类重写==.

### 3.6. 隐式接口

> *  Dart中的接口比较特殊, **==没有一个专门的关键字来声明接口.==**
>
> * 默认情况下，==**定义的每个类都相当于默认也声明了一个接口**==，可以由其他的类来实现(因为Dart不支持多继承)
>
> * dart中虽然没有专门的用于定义接口的关键字interface(默认,每个类都是一个抽象的接口), 但是却提供了用于==实现多个接口的关键字implements==.
>
> * ==一个类可以同时实现多个接口,但是只能够继承一个父类==.
>
> * ==而且,一旦实现某个接口,必须重写其中的全部的方法.==
>
> 在开发中，我们通常将用于给别人实现的类声明为抽象类:

```dart
abstract class Runner {
  run();
}

abstract class Flyer {
  fly();
}

class SuperMan implements Runner, Flyer {
  @override
  run() {
    print('超人在奔跑');
  }

  @override
  fly() {
    print('超人在飞');
  }
}
```

### 3.7. Mixin混入

> 在通过implements实现某个类时，类中所有的方法都必须**`被重新实现`**(无论这个类原来是否已经实现过该方法)。
>
> 但是某些情况下，一个类可能希望直接复用之前类的原有实现方案，怎么做呢?
>
> 使用继承吗？但是Dart只支持单继承，那么意味着你只能复用一个类的实现。
>
> Dart提供了另外一种方案: ==**Mixin混入的方式**==
>
> * **除了可以通过class定义类之外，也可以通过mixin关键字来定义一个类。**
>
> * **只是通过==mixin==定义的类用于被其他类混入使用，通过==with==关键字来进行混入。**
> * **很明显,使用mixin方式定义的类,还是可以被很好的混入和继承的.**

```dart
main(List<String> args) {
  var superMan = SuperMain();
  superMan.run();
  superMan.fly();
}

mixin Runner {
  run() {
    print('在奔跑');
  }
}

mixin Flyer {
  fly() {
    print('在飞翔');
  }
}

// implements的方式要求必须对其中的方法进行重新实现
// class SuperMan implements Runner, Flyer {}

class SuperMain with Runner, Flyer {

}
```

### 3.8. 类成员和方法

> 前面我们在类中定义的成员和方法都属于**==对象级别==**的, 在开发中, 我们有时候也需要定义**==类级别==**的成员和方法
>
> 在Dart中我们使用**==static关键字==**来定义:

```dart
main(List<String> args) {
  var stu = Student();
  stu.name = 'why';
  stu.sno = 110;
  stu.study();

  Student.time = '早上8点';
  // stu.time = '早上9点'; 错误做法, 实例对象不能访问类成员
  Student.attendClass();
  // stu.attendClass(); 错误做法, 实现对象补鞥呢访问类方法
}

class Student {
  String name;
  int sno;

  //全局的变量-只能够使用类来调用
  static String time;

  study() {
    print('$name在学习');
  }
//全局的函数,只能够使用类来直接的调用
  static attendClass() {
    print('去上课');
  }
}
```

### 3.9. 枚举类型枚举在开发中也非常常见, 枚举也是一种**特殊的类**, 

> 通常用于表示**固定数量的常量值**。

#### 3.9.1. 枚举的定义

* 枚举使用==**enum关键字**==来进行定义:

```dart
main(List<String> args) {
  print(Colors.red);
}

enum Colors {
  red,
  green,
  blue
}
```

#### 3.9.2. 枚举的属性

枚举类型中有两个比较常见的属性:

* index: 用于表示每个枚举常量的索引, 从0开始.
* values: 包含每个枚举值的List.

```dart
main(List<String> args) {
  print(Colors.red.index);
  print(Colors.green.index);
  print(Colors.blue.index);

  print(Colors.values);
}

enum Colors {
  red,
  green,
  blue
}
```

枚举类型的注意事项:

* **注意一**: **==您不能子类化、混合或实现枚举.==**
* **注意二**: **==不能显式实例化一个枚举.==**

## 四. 泛型

### 4.1. 为什么使用泛型?

对于有基础的同学, 这部分不再解释

### 4.2. List和Map的泛型

* List使用时的泛型写法:

```dart
  // 创建List的方式
  var names1 = ['why', 'kobe', 'james', 111];
  print(names1.runtimeType); // List<Object>

  // 限制类型
  var names2 = <String>['why', 'kobe', 'james', 111]; // 最后一个报错
  List<String> names3 = ['why', 'kobe', 'james', 111]; // 最后一个报错
```

* Map使用时的泛型写法:

```dart
  // 创建Map的方式
  var infos1 = {1: 'one', 'name': 'why', 'age': 18}; 
  print(infos1.runtimeType); // _InternalLinkedHashMap<Object, Object>

  // 对类型进行显示
  Map<String, String> infos2 = {'name': 'why', 'age': 18}; // 18不能放在value中
  var infos3 = <String, String>{'name': 'why', 'age': 18}; // 18不能放在value中
```

### 4.3. 类定义的泛型

> * 如果我们需要定义一个类, 用于存储位置信息Location, 但是并不确定使用者希望使用的是int类型,还是double类型, 甚至是一个字符串, 这个时候如何定义呢?
>
> * 一种方案是使用Object类型, 但是在之后使用时, 非常不方便(不推荐使用)
>
> * 另一种方案就是**==使用泛型==**.

___

#### Location类的定义: **==Object方式==**

```dart
main(List<String> args) {
  Location l1 = Location(10, 20);
  print(l1.x.runtimeType); // Object
}

class Location {
  Object x;
  Object y;

  Location(this.x, this.y);
}
```

#### Location类的定义: **==泛型方式==**

```dart
main(List<String> args) {
  Location l2 = Location<int>(10, 20);
  print(l2.x.runtimeType); // int 

  Location l3 = Location<String>('aaa', 'bbb');
  print(l3.x.runtimeType); // String
}
}

class Location<T> {
  T x;
  T y;

  Location(this.x, this.y);
}
```

#### 如果我们希望类型==只能是num类型==, 怎么做呢?

* 必须在类的定义的泛型说明中使用泛型继承类型才可以限定一下泛型的适用范围.
* class xxx<T extends num>{} 仅限于是num类型的泛型

```dart
main(List<String> args) {
  Location l2 = Location<int>(10, 20);
  print(l2.x.runtimeType);
    
  // 错误的写法, 类型必须继承自num
  Location l3 = Location<String>('aaa', 'bbb');
  print(l3.x.runtimeType);
}

class Location<T extends num> {
  T x;
  T y;

  Location(this.x, this.y);
}
```

### 4.4. 泛型方法的定义

> * 最初，==Dart仅仅在类中支持泛型==。
>
> * 后来一种称为==泛型方法==的新语法允许在方法和函数中使用类型参数。
> *  想必,之后泛型函数也是一种趋势.还是应该好好的理解与掌握.

```dart
main(List<String> args) {
  var names = ['why', 'kobe'];
  var first = getFirst(names);
  print('$first ${first.runtimeType}'); // why String
}

T getFirst<T>(List<T> ts) {
  return ts[0];
}
```