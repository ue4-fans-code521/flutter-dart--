# dart编程语言学习笔记

## dart中变量的几种声明方式:

1. **诸如==String / int==等等显示声明的方式**
2. **隐式声明的方式**
   1. 使用==var==推断式进行声明--但是一旦声明之后,变量的类型也就确定了
   2. ==const==-声明常量的方式,一旦声明赋值,后面的类型和值无法更改,而且必须在声明的同时给予赋值.
   3. ==final==-类似于上面的const定义常量的方式,但是不同点在与:可以在运行过程中进行赋值,比如调用函数的返回值进行赋值
   4. ==dynamic==-这是一种动态的推断赋值机制,可以在需要的时候更改变量的类型和值.
3. **dart是==强类型语言==,必须是先声明后使用**
4. dart中想同时输出多个变量-需要使用类似于ES6中的模板字符串 ==print(` $name $age  $price`)==

```dart
void main(List<String> args) {
  // 1.显式直接声明变量类型的方式
  // String name='iwen';
  // int age=123;
  // double price=12.12;
  // name=123;
  // print(name);
  // print(name.runtimeType);
    
    
  //输出多个变量-需要使用模板字符串
 // print("${name}  ${age}  ${price}");
    //或者
`//print("$name  $age  $price");

  /**
  * 
    2.隐式声明变量的方式,又可以分为如下的几种方式
    a.使用var推断式进行声明--但是一旦声明之后,变量的类型也就确定了
    b.const-声明常量的方式,一旦声明赋值,后面的类型和值无法更改,而且必须在声明的同时给予赋值.
    c.final-类似于上面的const定义常量的方式,但是不同点在与:可以在运行过程中进行赋值,比如调用函数的返回值进行赋值
    d.dynamic-这是一种动态的推断赋值机制,可以在需要的时候更改变量的类型和值.
  */

  //1.var隐式推断赋值
  var name = 'iwen';
  print(name);

  // 2.const隐式常量赋值
  const age = 111;
  // age=222;
  print(age);

  // 3.final隐式常量赋值
  final nation = 'UK';
  final refValue = retCall();
  // nation='USA';
  print(nation);
  print(refValue);

  //4.dynamic隐式变量赋值(可以修改之后的变量的类型与值)
  dynamic vvv = 8899;
  print(vvv);

  vvv = 'mike';
  print(vvv);
}

int retCall() {
  return 555;
}

```

## dart中的数字类型与字符串类型之间的相互转换

1. 数值类型-->字符串类型 xxx.toString( )

2. 字符串类型-->数值类型 xxx.

   ```dart
   main(List<String> args) {
     int age = 0x123;
   
     //数值类型-->字符串类型  需要使用xx变量.toString()
     var res=age.toString();
     print('$age ${age.runtimeType}');
     print('$res ${res.runtimeType}');
     // print(age);
     if (true) {
       print('哈哈哈啊');
     }
   
     
     String ss="123.6787";
     //字符串-->数值类型   需要使用类型.parse()
     var stoi=double.parse(ss);
     print('${stoi} ${stoi.runtimeType}');
   
   }
   
   ```

## Dat中不能判断非0即真，或者非空即真

```dart
  if (true) {
    print('哈哈哈啊');
  }
```

## 字符串类型

### 字符串的几种定义的方式

1.  '单引号定义字符串'
2. "双引号定义字符串"
3. '''三引号定义字符串'''---可支持换行

```dart
main(List<String> args) {
  //1.'单引号定义字符串'
  //2.'双引号定义字符串'
  //1.'三引号定义字符串'-可支持换行
  String country='UK';
  String street="avnue 12";
  
  String name='''iwen
  mike
  hongkong
  jackman
  ''';

  print(name);
}
```

### 字符串的拼接---使用${变量}

* 说明:当$后紧跟的是一个变量,那么是可以省略掉{}的
* 但是,当$后面是一个表达式的时候,那么{}是不可以省略的.

```dart
main(List<String> args) {
  //1.'单引号定义字符串'
  //2.'双引号定义字符串'
  //1.'三引号定义字符串'-可支持换行
  String country = 'UK';
  String street = "avnue 12";

  String name = '''iwen''';

 //如果$后面仅仅是一个变量,则{}可以省略
  print("$country---$name---$street");
  //如果是表达式-{}是不可以省略的
  print("${country.runtimeType}---${name.runtimeType}---${street.runtimeType}");
}

```

## dart中的集合类型

1. **List<xxx>类型,类似于数组**
2. **Set<xxx>集合类型**
3. **Map<xxx,yyy>键值对类型**

```dart
main(List<String> args) {
  /**
   * dart中的集合类型分为如下几种
   * 1.List<String>类型
   * 2.Set<String>类型
   * 3.Map<String,dynamic>类型
  */

  //1. 注意集合List<xxx>中的一些常见的操作
  List<String> li = ['mike', 'iwen', 'trump', 'jack', "123", 'trump', "123"];
  print(li);
  print(li.length);
  print(li.contains('mike'));

  li.add('5555');
  print(li);

  li.removeAt(1);
  print(li);

  //2. Set<xxx>集合的类型---不可以包含重复我元素,或者对List<>做重复性的过滤
  Set<int> st = {1, 2, 3, 4, 5, 6};
  print(st);
  //使用set过滤掉list中的重复的元素
  var res = List.from(Set.from(li));
  print(res);

  // 3.Map<String,dynamic>--这样的键值对key-value的形式的集合,一样会使用泛型,
  // 但是一般会把第二个vlaue的泛型设置dynamic动态类型,以方便后面的修改
  Map<String, String> mp = {'name': 'pence', 'age': '123', 'nation': 'UK'};
  print(mp);

//Map中的常见方法
  print(mp.keys);
  print(mp.values);
  print(mp.containsValue('123'));
}

```

