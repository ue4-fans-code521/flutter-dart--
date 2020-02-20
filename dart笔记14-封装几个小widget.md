# Flutter(十一)之封装几个小Widget

> **更新地点：** 首发于公众号，第二天更新于掘金、思否、开发者头条等地方；
>
> **更多交流：** 可以添加我的微信 372623326，关注我的微博：coderwhy

> 学习完列表渲染后，我打算做一个综合一点的练习小项目：豆瓣Top电影排行列表；



![image-20191002084721424](https://user-gold-cdn.xitu.io/2019/10/6/16d9fff1e93b178c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



这个练习小项目主要是为了锻炼布局Widget，但是也涉及到一些其他知识点：评分展示、分割线、bottomNavigationBar等。

这些内容，我们放到后面进行补充，但是在进行豆瓣Top电影模仿时，有两个东西实现起来比较复杂：

1、**评分展示：** 我们需要根据不同的评分显示不同的星级展示，这里我封装了一个StarRating的小Widget来实现；

2、**分割线：** 最初我考虑使用`边框虚线`来完成分割线，后来发现Flutter并不支持`虚线边框`，因此封装了一个DashedLine的小Widget来实现。

当然，这个章节如果你觉得过于复杂，可以直接把我封装好的两个东西拿过去使用；

## 一. StarRating

### 1.1. 最终效果展示

目的：实现功能展示的同时，提供高度的定制效果

* `rating`：必传参数，告诉Widget当前的评分。
* `maxRating`：可选参数，最高评分，根据它来计算一个比例，默认值为10；
* `size`：星星的大小，决定每一个star的大小；
* `unselectedColor`：未选中星星的颜色（该属性是使用默认的star才有效）；
* `selectedColor`：选中星星的颜色（该属性也是使用默认的star才有效）；
* `unselectedImage`：定制未选中的star；
* `selectedImage`：定义选中时的star；
* `count`：展示星星的个数；

暂时实现上面的定制，后续有新的需求继续添加新的功能点~



![image-20191002085908926](https://user-gold-cdn.xitu.io/2019/10/6/16d9fff1e9601f44?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



### 1.2. 实现思路分析

理清楚思路后，你会发现并不是非常复杂，主要就是两点的展示：

* 未选中star的展示：根据个数和传入的unselectedImage创建对应个数的Widget即可；
* 选中star的展示：
  * 计算出满star的个数，创建对应的Widget；
  * 计算剩余比例的评分，对最后一个Widget进行裁剪；

**问题一：选择StatelessWidget还是StatefulWidget？**

考虑到后面可能会做用户点击进行评分或者用户手指滑动评分的效果，所以这里选择StatefulWidget

* 目前还没有讲解事件监听相关，所以暂时不添加这个功能

**问题二：如何让`选中的star`和`未选中的star`重叠显示？**

* 非常简单，使用Stack即可；

```dart
child: Stack(
  children: <Widget>[
    Row(children: getUnSelectImage(), mainAxisSize: MainAxisSize.min,),
    Row(children: getSelectImage(), mainAxisSize: MainAxisSize.min,),
  ],
),
复制代码
```

**问题三：如何实现对选中的最后一个star进行裁剪？**

* 可以使用ClipRect定制CustomClipper进行裁剪

定义CustomClipper裁剪规则：

```dart
class MyRectClipper extends CustomClipper<Rect>{
  final double width;

  MyRectClipper({
    this.width
  });

  @override
  Rect getClip(Size size) {
    return Rect.fromLTRB(0, 0, width, size.height);
  }

  @override
  bool shouldReclip(MyRectClipper oldClipper) {
    return width != oldClipper.width;
  }
}
复制代码
```

使用MyRectClipper进行裁剪：

```dart
Widget leftStar = ClipRect(
  clipper: MyRectClipper(width: leftRatio * widget.size),
  child: widget.selectedImage,
);
复制代码
```

### 1.3. 最终代码实现

最终代码并不复杂，而且我也有给出主要注释：

```dart
import 'package:flutter/material.dart';

class HYStarRating extends StatefulWidget {
  final double rating;
  final double maxRating;
  final Widget unselectedImage;
  final Widget selectedImage;
  final int count;
  final double size;
  final Color unselectedColor;
  final Color selectedColor;

  HYStarRating({
    @required this.rating,
    this.maxRating = 10,
    this.size = 30,
    this.unselectedColor = const Color(0xffbbbbbb),
    this.selectedColor = const Color(0xffe0aa46),
    Widget unselectedImage,
    Widget selectedImage,
    this.count = 5,
  }): unselectedImage = unselectedImage ?? Icon(Icons.star, size: size, color: unselectedColor,),
        selectedImage = selectedImage ?? Icon(Icons.star, size: size, color: selectedColor);

  @override
  _HYStarRatingState createState() => _HYStarRatingState();
}

class _HYStarRatingState extends State<HYStarRating> {
  @override
  Widget build(BuildContext context) {
    return Container(
      child: Stack(
        children: <Widget>[
          Row(children: getUnSelectImage(), mainAxisSize: MainAxisSize.min),
          Row(children: getSelectImage(), mainAxisSize: MainAxisSize.min),
        ],
      ),
    );
  }

  // 获取评星
  List<Widget> getUnSelectImage() {
    return List.generate(widget.count, (index) => widget.unselectedImage);
  }

  List<Widget> getSelectImage() {
    // 1.计算Star个数和剩余比例等
    double oneValue = widget.maxRating / widget.count;
    int entireCount = (widget.rating / oneValue).floor();
    double leftValue = widget.rating - entireCount * oneValue;
    double leftRatio = leftValue / oneValue;

    // 2.获取start
    List<Widget> selectedImages = [];
    for (int i = 0; i < entireCount; i++) {
      selectedImages.add(widget.selectedImage);
    }

    // 3.计算
    Widget leftStar = ClipRect(
      clipper: MyRectClipper(width: leftRatio * widget.size),
      child: widget.selectedImage,
    );
    selectedImages.add(leftStar);

    return selectedImages;
  }
}


class MyRectClipper extends CustomClipper<Rect>{
  final double width;

  MyRectClipper({
    this.width
  });

  @override
  Rect getClip(Size size) {
    return Rect.fromLTRB(0, 0, width, size.height);
  }

  @override
  bool shouldReclip(MyRectClipper oldClipper) {
    return width != oldClipper.width;
  }
}
复制代码
```

## 二. DashedLine

### 2.1. 最终实现效果

目的：实现效果的同时，提供定制，并且可以实现水平和垂直两种虚线效果：

* `axis`：确定虚线的方向；
* `dashedWidth`：根据虚线的方向确定自己虚线的宽度；
* `dashedHeight`：根据虚线的方向确定自己虚线的高度；
* `count`：内部会根据设置的个数和宽高确定密度（虚线的空白间隔）；
* `color`：虚线的颜色，不多做解释；



![image-20191002105938669](https://user-gold-cdn.xitu.io/2019/10/6/16d9fff1e958adb5?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



暂时实现上面的定制，后续有新的需求继续添加新的功能点~

### 2.2. 实现思路分析

实现比较简单，主要是根据用户传入的方向确定添加对应的SizedBox即可。

**这里有一个注意点：虚线到底是设置多宽或者多高呢？**

* 我这里是根据方向获取父Widget的宽度和高度来决定的；
* 通过LayoutBuilder可以获取到父Widget的宽度和高度；

```dart
return LayoutBuilder(
  builder: (BuildContext context, BoxConstraints constraints) {
    // 根据宽度计算个数
    return Flex(
      direction: this.axis,
      mainAxisAlignment: MainAxisAlignment.spaceBetween,
      children: List.generate(this.count, (int index) {
        return SizedBox(
          width: dashedWidth,
          height: dashedHeight,
          child: DecoratedBox(
            decoration: BoxDecoration(color: color),
          ),
        );
      }),
    );
  },
);
复制代码
```

### 2.3. 最终代码实现

比较简单的封装，直接给出最终代码实现：

```dart
class HYDashedLine extends StatelessWidget {
  final Axis axis;
  final double dashedWidth;
  final double dashedHeight;
  final int count;
  final Color color;

  HYDashedLine({
    @required this.axis,
    this.dashedWidth = 1,
    this.dashedHeight = 1,
    this.count,
    this.color = const Color(0xffff0000)
  });

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (BuildContext context, BoxConstraints constraints) {
        // 根据宽度计算个数
        return Flex(
          direction: this.axis,
          mainAxisAlignment: MainAxisAlignment.spaceBetween,
          children: List.generate(this.count, (int index) {
            return SizedBox(
              width: dashedWidth,
              height: dashedHeight,
              child: DecoratedBox(
                decoration: BoxDecoration(color: color),
              ),
            );
          }),
        );
      },
    );
  }
}
复制代码
```

## 三. 实现底部TabBar

### 3.1. TabBar实现说明



![image-20191002121426017](https://user-gold-cdn.xitu.io/2019/10/6/16d9fff1e974303e?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



在即将完成的小练习中，我们有实现一个底部的TabBar，如何实现呢？

在Flutter中，我们会使用Scaffold来搭建页面的基本结构，实际上它里面有一个属性就可以实现底部TabBar功能：bottomNavigationBar。

bottomNavigationBar对应的类型是BottomNavigationBar，我们来看一下它有什么属性：

* 属性非常多，但是都是设置底部TabBar相关的，我们介绍几个：
* `currentIndex`：当前选中哪一个item；
* `selectedFontSize`：选中时的文本大小；
* `unselectedFontSize`：未选中时的文本大小；
* `type`：当item的数量超过2个时，需要设置为fixed；
* `items`：放入多个BottomNavigationBarItem类型；
* `onTap`：监听哪一个item被选中；

```dart
class BottomNavigationBar extends StatefulWidget {
  BottomNavigationBar({
    Key key,
    @required this.items,
    this.onTap,
    this.currentIndex = 0,
    this.elevation = 8.0,
    BottomNavigationBarType type,
    Color fixedColor,
    this.backgroundColor,
    this.iconSize = 24.0,
    Color selectedItemColor,
    this.unselectedItemColor,
    this.selectedIconTheme = const IconThemeData(),
    this.unselectedIconTheme = const IconThemeData(),
    this.selectedFontSize = 14.0,
    this.unselectedFontSize = 12.0,
    this.selectedLabelStyle,
    this.unselectedLabelStyle,
    this.showSelectedLabels = true,
    bool showUnselectedLabels,
  }) 
}
复制代码
```

当实现了底部TabBar展示后，我们需要监听它的点击来切换显示不同的页面，这个时候我们可以使用IndexedStack来管理多个页面的切换：

```dart
body: IndexedStack(
  index: _currentIndex,
  children: <Widget>[
    Home(),
    Subject(),
    Group(),
    Mall(),
    Profile()
  ],
复制代码
```

### 3.2. TabBar代码实现

注意事项：

* 1、我们需要在其他地方创建对应要切换的页面；
* 2、需要引入对应的资源，并且在pubspec.yaml中引入；

```dart
import 'package:flutter/material.dart';
import 'views/home/home.dart';
import 'views/subject/subject.dart';
import 'views/group/group.dart';
import 'views/mall/mall.dart';
import 'views/profile/profile.dart';


class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: "豆瓣",
      theme: ThemeData(
        primaryColor: Colors.green,
        highlightColor: Colors.transparent,
        splashColor: Colors.transparent
      ),
      home: MyStackPage(),
    );
  }
}

class MyStackPage extends StatefulWidget {
  @override
  _MyStackPageState createState() => _MyStackPageState();
}

class _MyStackPageState extends State<MyStackPage> {

  var _currentIndex = 0;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: _currentIndex,
        selectedFontSize: 14,
        unselectedFontSize: 14,
        type: BottomNavigationBarType.fixed,
        items: [
          createItem("home", "首页"),
          createItem("subject", "书影音"),
          createItem("group", "小组"),
          createItem("mall", "市集"),
          createItem("profile", "我的"),
        ],
        onTap: (index) {
          setState(() {
            _currentIndex = index;
          });
        },
      ),
      body: IndexedStack(
        index: _currentIndex,
        children: <Widget>[
          Home(),
          Subject(),
          Group(),
          Mall(),
          Profile()
        ],
      ),
    );
  }
}

BottomNavigationBarItem createItem(String iconName, String title) {
  return BottomNavigationBarItem(
      icon: Image.asset("assets/images/tabbar/$iconName.png", width: 30,),
      activeIcon: Image.asset("assets/images/tabbar/${iconName}_active.png", width: 30,),
      title: Text(title)
  );
}
复制代码
```

> 备注：所有内容首发于公众号，之后除了Flutter也会更新其他技术文章，TypeScript、React、Node、uniapp、mpvue、数据结构与算法等等，也会更新一些自己的学习心得等，欢迎大家关注