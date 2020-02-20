# dart中的运算符与流程控制

## 运算符

### 概念:

>这里，我只列出来相对其他语言比较特殊的运算符，因为某些运算符太简单了，不浪费时间，比如+、-、+=、==。
>
>你可能会疑惑，Dart为什么要搞出这么多特殊的运算符呢？
>
>你要坚信一点：所有这些特殊的运算符都是为了让我们在开发中可以更加方便的操作，而不是让我们的编码变得更加复杂。
>
>下面我们仅就dart中较为特殊的运算符进行说明

___

### 除法、整除、取模运算

```dart
var num = 7;
print(num / 3); // 除法操作, 结果2.3333..
print(num ~/ 3); // 整除操作, 结果2;
print(num % 3); // 取模操作, 结果1;
```

###   ??= 赋值操作

> * dart有一个很多语言都不具备的赋值运算符：==**??=**==
>
> * 当变量为null时，使用后面的内容进行赋值。
> * 当变量有值时，使用自己原来的值。

```dart
main(List<String> args) {
  var name1 = 'coderwhy';
  print(name1);
  // var name2 = 'kobe';
  var name2 = null;
  name2 ??= 'james'; 
  print(name2); // 当name2初始化为kobe时，结果为kobe，当初始化为null时，赋值了james
}
```

###  条件运算符：

> * Dart中包含一直比较特殊的条件运算符：==**expr1 ?? expr2**==
>
> * 如果expr1是null，则返回expr2的结果;
>
> * 如果expr1不是null，直接使用expr1的结果。

```
var temp = 'why';
var temp = null;
var name = temp ?? 'kobe';
print(name);
```

### 级联语法：==..==

> * **某些时候，我们希望对一个对象进行连续的操作，这个时候可以使用==级联语法==**

```dart
class Person {
  String name;

  void run() {
    print("${name} is running");
  }

  void eat() {
    print("${name} is eating");
  }

  void swim() {
    print("${name} is swimming");
  }
}

main(List<String> args) {
  final p1 = Person();
  p1.name = 'why';
  p1.run();
  p1.eat();
  p1.swim();

  final p2 = Person()
              ..name = "why"
              ..run()
              ..eat()
              ..swim();
}
```

##  流程控制

> **和大部分语言的特性比较相似，这里就不再详细赘述，看一下即可**

###  if和else

> **和其他语言用法一样这里有一个注意点：==不支持非空即真或者非0即真，必须有明确的bool类型==**
>
> **我们来看下面name为null的判断**

![image-20190911085225484](https://segmentfault.com/img/remote/1460000020376240)

### 循环操作

#### 基本的for循环

```dart
for (var i = 0; i < 5; i++) {
  print(i);
}
```

#### for in遍历List和Set类型

```
var names = ['why', 'kobe', 'curry'];
for (var name in names) {
  print(name);
}
```

#### while和do-while和其他语言一致

#### break和continue用法也是一致

### switch-case

> * **普通的switch使用**
>
> * **注意：每一个case语句，==默认情况下必须以一个break结尾==**

```
main(List<String> args) {
  var direction = 'east';
  switch (direction) {
    case 'east':
      print('东面');
      break;
    case 'south':
      print('南面');
      break;
    case 'west':
      print('西面');
      break;
    case 'north':
      print('北面');
      break;
    default:
      print('其他方向');
  }
}
```