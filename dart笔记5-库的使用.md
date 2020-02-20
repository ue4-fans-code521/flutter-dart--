## 五. 库的使用

> 在Dart中，你可以导入一个库来使用它所提供的功能。
>
> 库的使用可以使代码的重用性得到提高，并且可以更好的组合代码。
>
> Dart中任何一个dart文件都是一个库，即使你没有用关键字`library`声明

### 5.1. 库的导入

import语句用来导入一个库，后面跟一个字符串形式的Uri来指定表示要引用的库，语法如下：

```dart
import '库所在的uri';
```

#### **常见的库URI有三种不同的形式**

* 来自dart标准版，比如dart:io、dart:html、dart:math、dart:core(但是这个可以省略,默认载入)
* 上述的引入库的方式仅仅适用于dart中的**==标准库==**

```dart
//dart:前缀表示Dart的标准库，如dart:io、dart:html、dart:math
import 'dart:io';
import 'dart:math';

main(List<String> args) {
  print(pow(2, 3));//结果8,如果不引入dart中的标准库,则会报错
}

```

* 使用==**相对路径**==导入的库，通常指==**自己项目中定义的其他dart文件**==

```dart
//当然，你也可以用相对路径或绝对路径的dart文件来引用
import 'lib/student/student.dart';
```

* **==Pub包管理工具管理的一些库==**，包括自己的配置以及一些第三方的库，通常使用**==前缀package==**
* **==flutter中的管理第三方库的工具==**,类似于nodeJS中的npm管理工具.

```dart
//Pub包管理系统中有很多功能强大、实用的库，可以使用前缀 package:
import 'package:flutter/material.dart';
```

#### **库文件中内容的显示和隐藏**

>  如果希望`只导入库中某些内容`，或者刻意`隐藏库里面某些内容`，可以使用`show`和`hide`关键字
>
> * **show关键字：**可以显示某个成员（屏蔽其他）
>
> * **hide关键字：**可以隐藏某个成员（显示其他）

```dart
import 'lib/student/student.dart' show Student, Person;

import 'lib/student/student.dart' hide Person;
```

#### **库中内容和当前文件中的名字冲突**

> * 当各个库有命名冲突的时候，可以使用`as关键字`来使用命名空间
> * 当使用as重新命名之后,我们仍然可以使用show或者hide来决定导入哪些或者屏蔽掉哪些.

```dart
import 'lib/student/student.dart' as Stu;

Stu.Student s = new Stu.Student();
```

### 5.2. 库的定义

#### **library关键字**

> 通常在定义库时，我们可以使用library关键字给库起一个名字。
>
> 但目前我发现，库的名字并不影响导入，因为import语句用的是字符串URI
>
> 直接使用uri导入即可,无需再使用library命名

```dart
library math;
```

#### **part关键字**

> * 在之前我们使用student.dart作为演练的时候，只是将该文件作为一个库。
>
> * 在开发中，如果一个库文件太大，将所有内容保存到一个文件夹是不太合理的，我们有可能希望将这个库进行拆分，这个时候就可以使用`part`关键字了
> * 最重要的两个关键字`part`以及`part of`
>
> * **==不过官方已经不建议使用这种方式了==**：
> * https://dart.dev/guides/libra...](https://dart.dev/guides/libraries/create-library-packages)

![image-20190911173722226](https://segmentfault.com/img/remote/1460000020376241)

1. `mathUtils.dart`文件dartateUtils.dart`文件
2. **==关键字part是最为关键的---声明该模块是被整合在哪个管理模块之下==**

```dart
part of "utils.dart";

String dateFormat(DateTime date) {
  return "2020-12-12";
}
```

3. `utils.dart`文件[**==整合模块==**]
4. 在该整合模块中最为关键的是**==part关键字==**,指明了在该管理模块中管理着哪些具体的模块

```dart
part "mathUtils.dart";
part "dateUtils.dart";
```

5. `test_libary.dart`文件

```dart
import "lib/utils.dart";

main(List<String> args) {
  print(sum(10, 20));
  print(dateFormat(DateTime.now()));
}
```

<img src="https://segmentfault.com/img/remote/1460000020376242" alt="image-20190911173346382" style="zoom:80%;" />

#### part分模块的代码原理分析

![image-20200212210043106](C:\Users\linux\AppData\Roaming\Typora\typora-user-images\image-20200212210043106.png)

#### **export关键字**

> * 官方**==不推荐==**使用`part关键字`，那如果库非常大，如何进行管理呢？
>
> * 将每一个dart文件作为库文件，使用**==export关键字==**在某个库文件中单独导入

1. `mathUtils.dart`文件

```dart
int sum(int num1, int num2) {
  return num1 + num2;
}
```

2. `dateUtils.dart`文件

```dart
String dateFormat(DateTime date) {
  return "2020-12-12";
}
```

3. `utils.dart`文件

```dart
library utils;

export "mathUtils.dart";
export "dateUtils.dart";
```

4. `test_libary.dart`文件

```dart
import "lib/utils.dart";

main(List<String> args) {
  print(sum(10, 20));
  print(dateFormat(DateTime.now()));
}
```

5. 使用完整的代码展示export管理分模块的原理 

![image-20200212212634882](C:\Users\linux\AppData\Roaming\Typora\typora-user-images\image-20200212212634882.png)

最后，也可以通过Pub管理自己的库自己的库，在项目开发中个人觉得不是非常有必要，所以暂时不讲解这种方式。