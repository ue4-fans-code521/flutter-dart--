# dart中的函数

## 概念

### Dart是一种真正的面向对象语言，所以即使函数也是对象，所有也有类型, 类型就是Function。这也就意味着函数可以作为变量定义或者作为其他函数的参数或者返回值.

### 函数的定义方式:

`返回值 函数的名称(参数列表) {  函数体  return 返回值}`

```dart
int sum(num num1, num num2) {
  return num1 + num2;
}
```

### Effective Dart建议对公共的API, 使用类型注解, 但是如果我们省略掉了类型, 依然是可以正常工作的

```dart
sum(num1, num2) {
  return num1 + num2;
}
```

### 另外, 如果函数中只有一个表达式, 那么可以使用箭头语法(*arrow* syntax)

* 注意: ==这里面只能是一个表达式, 不能是一个语句==

```dart
sum(num1, num2) => num1 + num2;
```

## 函数的参数问题

> **函数的参数可以分成两类: ==必须参数和可选参数==**
>
> **参数列表中前面使用的参数都是必须参数.**

### 可选参数:

可选参数可以分为 ==**命名可选参数**== 和 ==**位置可选参数**==

定义方式:

```
命名可选参数: {param1, param2, ...}

位置可选参数: [param1, param2, ...]
```

### 参数默认值

___

>**参数可以有默认值, 在不传入的情况下, 使用默认值**
>
>* **注意, ==只有可选参数才可以有默认值, 必须参数不能有默认值==**
>* **Dart中的main函数就是一个接受可选的列表参数作为参数的, 所以在使用main函数时, 我们可以传入参数, 也可以不传入参数**
___
```dart
// 参数的默认值
printInfo4(String name, {int age = 18, double height=1.88}) {
  print('name=$name age=$age height=$height');
}
```

### 综合上面知识点的代码

```dart
import '5-函数.dart';

main(List<String> args) {
  // ==============位置可选参数=================
  demo_pos(123, 345);
  demo_pos(123, 345,'iwen');
  demo_pos(123, 345,'iwen','mike');
  print("================================");
  // ==============命名可选参数===============
  demo_named(12, 99);
  demo_named(12, 99,n3:'UK');
}

//可选参数中的位置可选参数
void demo_pos(int n1,int n2,[String n3,String n4="trump"]){
  print("$n1 $n2 $n3  $n4");
}

//可选参数中的命名可选参数
void demo_named(int n1,int n2,{String n3,String n4="vivo"}){
  print("$n1 $n2 $n3  $n4");
}
```



## dart中的函数式一等公民的问题

### 概念:

___

>**在很多语言中, 函数并不能作为一等公民来使用, 比如Java/OC. 这种限制让编程不够灵活, 所以现代的编程语言基本都支持函数作为一等公民来使用, Dart也支持.这就意味着你可以==将函数赋值给一个变量==, 也可以==将函数作为另外一个函数的参数或者返回值来使用.==**

___

```dart
//代码1:
main(List<String> args) {
  // 1.将函数赋值给一个变量
  var bar = foo;
  print(bar);

  // 2.将函数作为另一个函数的参数
  test(foo);

  // 3.将函数作为另一个函数的返回值
  var func =getFunc();
  func('kobe');
}

// 1.定义一个函数
foo(String name) {
  print('传入的name:$name');
}

// 2.将函数作为另外一个函数的参数
test(Function func) {
  func('coderwhy');
}

// 3.将函数作为另一个函数的返回值
getFunc() {
  return foo;
}
```

```dart
//代码2:[自己书写]
main(List<String> args) {
  //注意:函数也可以视为一数据类型 ,当然可以赋给某个函数类型的变量
  // Function vv = demo;
  // vv();
  // print(vv());

  //函数作为函数的参数来传递;
  fnParam(demo);

  //把函数作为返回值
  // print(retFn());
  retFn()();
}

Function retFn() {
  return demo;
}

void fnParam(Function fn) {
  fn();
}

demo() {
  print("hello world~~");
}

```

## 匿名函数的使用

### 概念:

>**大部分我们定义的函数都会有自己的名字， 比如前面定义的foo、test函数等等。但是某些情况下，给函数命名太麻烦了，我们可以使用没有名字的函数，这种函数可以被称之为==匿名函数==( *anonymous function*)，也可以叫==*lambda*==或者*==closure==*。**

___

### 例码:

```dart
//技术博客文档;

main(List<String> args) {
  // 1.定义数组
  var movies = ['盗梦空间', '星际穿越', '少年派', '大话西游'];

  // 2.使用forEach遍历: 有名字的函数
  printElement(item) {
    print(item);
  }
  movies.forEach(printElement);

  // 3.使用forEach遍历: 匿名函数
  movies.forEach((item) {
    print(item);
  });
  movies.forEach((item) => print(item));
}
```

```dart
//自己编写代码;

main(List<String> args) {
  var phones = ['oppo', 'vivo', 'mi', 'samsung', 'apple'];
  // print(phones.runtimeType);
  //普通的列表的遍历操作
  // for (var item in phones) {
  //   print(item);
  // }

  //下面我们定义函数,来完成对于list的遍历操作
  fnIterate(item) {
    print(item);
  }
// foreach遍历过程调用声明函数(而非匿名函数)
  // phones.forEach(fnIterate);

  //1. 下面我们遍历同样的list使用匿名函数 1
  phones.forEach((item) => print(item));
  //2. 下面我们遍历同样的list使用匿名函数 2
  phones.forEach((item) {
    print(item);
    print("==================");
  });
}

```

## 词法作用域

### 概念:

>**dart中的词法有自己明确的作用域范围，它是根据代码的结构({})来决定作用域范围的**
>
>**==优先使用自己作用域中的变量，如果没有找到，则一层层向外查找。直到找到需要的同名的变量, 如果始终匹配不到,则直接报错==**

___

### 代码:

```dart
var name = 'global';
main(List<String> args) {
  // var name = 'main';
  void foo() {
    // var name = 'foo';
    print(name);
  }

  foo();
}
```

```dart
//自己代码:

var name='I Love Dart Lang';

main(List<String> args) {
  // var name = 'iwen';

  foo() {
    // var name = 'mike';
    print(name);
  }

  foo();
}

```

## 函数的返回值

### 概念:

>==**返回值问题:**==
>
>**所有函数都返回一个值。如果没有指定返回值，则语句返回null;隐式附加到函数体中了。**
>
>**但是:像明确指定函数的返回值类型为void就是没有返回值,==像没有明确指定返回值的就是返回null==** 

___

### 代码:

```dart
main(List<String> args) {
  print(foo()); // null
}

foo() {
  print('foo function');
}
```

## 词法闭包

### 概念：

>**闭包可以访问其词法范围内的变量，即使函数在其他地方被使用，**
>
>**也可以正常的访问。**

 ```dart
main(List<String> args) {
  makeAdder(num addBy) {
    return (num i) {
      return i + addBy;
    };
  }

  var adder2 = makeAdder(2);
  print(adder2(10)); // 12
  print(adder2(6)); // 8

  var adder5 = makeAdder(5);
  print(adder5(10)); // 15
  print(adder5(6)); // 11
}

 ```



