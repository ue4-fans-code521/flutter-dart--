# dart中的数据类型与关键字的区别

## dart中的数据类型

> String / int / Double / dynamic 都是属于**==数据类型==**,涉及到泛型的必须是类型才可以,不能是关键字
>
> 但是,像var / final / const 这种类型推断则是**==关键字==**,泛型中不能使用.

## dart中的数据类型dynamic与object的区别

> dynamic是动态确定的数据类型,只有在运行时才知道,不会做强制的静态(编译前)语法排查
>
> 所以像如下的代码是可以正常的执行的:
>
> ```dart
> dynamic name='iwen'
> print(name.length)//结果:4
> ```
>
> 但是对于object类型而言,是会做编译前的(静态)的语法监测的，所以一下的代码是无法通过编译前的语法监测的,因为编译前就已经确定了数据类型为object[虽然运行过程中以实际赋值类型],但是编译前仍然仍定数据类型就是object
>
> ```dart
> Object name='iwen'
> print(name.length)//结果:出错-静态编译无法通过--运行代码之前仍视为Object类型,而Object是没有					length属性的
> ```
>
> ___

## 