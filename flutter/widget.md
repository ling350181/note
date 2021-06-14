- [ExpansionPanelList](#ExpansionPanelList)
- [TextField](#TextField)
- [Decoration背景设定](#Decoration背景设定)
- [Row和Column](#Row和Column)
- [PageView](#PageView)
- [ScrollPhysics详细](#ScrollPhysics详细)
- [Wrap](#Wrap)
- [圆角的组件ClipRRect](#圆角的组件ClipRRect)
- [StickyHeader](#StickyHeader)

# ExpansionPanelList
- [中文教程](https://www.jianshu.com/p/ee9aa62caaee)
- [踩坑点](./memo.md)


# TextField

- 详解文档(https://blog.csdn.net/yuzhiqiang_1993/article/details/88204031)

# Decoration背景设定
- BoxDecoration:实现边框、圆角、阴影、形状、渐变、背景图像

- ShapeDecoration:实现四个边分别指定颜色和宽度、底部线、矩形边色、圆形边色、体育场（竖向椭圆）、 角形（八边角）边色

- FlutterLogoDecoration:实现Flutter图片

- UnderlineTabindicator:下划线

1. BoxDecoration例子
``` dart
const BoxDecoration({
    this.color, // 底色
    this.image, // 图片
    this.border, 边色
    this.borderRadius, // 圆角度
    this.boxShadow, // 阴影
    this.gradient, // 渐变
    this.backgroundBlendMode, // 混合Mode
    this.shape = BoxShape.rectangle,  // 形状
  })
```
2. 边框+圆角
``` dart
decoration: new BoxDecoration(
  border: new Border.all(color: Color(0xFFFF0000), width: 0.5), // 边色与边宽度
  color: Color(0xFF9E9E9E), // 底色
  //        borderRadius: new BorderRadius.circular((20.0)), // 圆角度
  borderRadius: new BorderRadius.vertical(top: Radius.elliptical(20, 50)), // 也可控件一边圆角大小
),
```

3. 阴影
``` dart

decoration: new BoxDecoration(
    border: new Border.all(color: Color(0xFFFF0000), width: 0.5), // 边色与边宽度
// 生成俩层阴影，一层绿，一层黄， 阴影位置由offset决定,阴影模糊层度由blurRadius大小决定（大就更透明更扩散），阴影模糊大小由spreadRadius决定
    boxShadow: [
      BoxShadow(
        color: Color(0x99FFFF00),
        offset: Offset(5.0, 5.0),    
        blurRadius: 10.0, spreadRadius: 2.0)
       ],
),
```

4. 形状（圆形与矩形）
``` dart
decoration: new BoxDecoration(
  border: new Border.all(color: Color(0xFFFFFF00), width: 0.5), // 边色与边宽度
  color: Color(0xFF9E9E9E), // 底色
  //        shape: BoxShape.circle, // 圆形，使用圆形时不可以使用borderRadius
  shape: BoxShape.rectangle, // 默认值也是矩形
  borderRadius: new BorderRadius.circular((20.0)), // 圆角度
),
```

5. 渐变（环形、扫描式、线性）
``` dart
decoration: new BoxDecoration(
  border: new Border.all(color: Color(0xFFFFFF00), width: 0.5), // 边色与边宽度
// 环形渲染
  gradient: RadialGradient(colors: [Color(0xFFFFFF00), Color(0xFF00FF00), Color(0xFF00FFFF)],radius: 1, tileMode: TileMode.mirror)
//扫描式渐变
//        gradient: SweepGradient(colors: [Color(0xFFFFFF00), Color(0xFF00FF00), Color(0xFF00FFFF)], startAngle: 0.0, endAngle: 1*3.14)
// 线性渐变
//        gradient: LinearGradient(colors: [Color(0xFFFFFF00), Color(0xFF00FF00), Color(0xFF00FFFF)], begin: FractionalOffset(1, 0), end: FractionalOffset(0, 1))
),
```

6. 背景图像
```dart

decoration: new BoxDecoration(
  border: new Border.all(color: Color(0xFFFFFF00), width: 0.5), // 边色与边宽度
  image: new DecorationImage(
  image: new NetworkImage('https://avatar.csdn.net/8/9/A/3_chenlove1.jpg'), // 网络图片
  // image: new AssetImage('graphics/background.png'), 本地图片
  fit: BoxFit.fill // 填满
  //          centerSlice: new Rect.fromLTRB(270.0, 180.0, 1360.0, 730.0),// 固定大小
  ),
```

7. ShapeDecoration例子
  - 构造方法
  ``` dart
const ShapeDecoration({
  this.color,
  this.image,
  this.gradient,
  this.shadows,
  @required this.shape,
})
  ```
  - 例子
  ``` dart
  decoration: new ShapeDecoration(
  color: Color(0xFFFF00FF), // 底色
  // 统一四边颜色和宽度
  shape: Border.all(color: Color(0xFF00FFFF),style: BorderStyle.solid,width: 2)
// 四个边分别指定颜色和宽度， 当只给bottom时与UnderlineInputBorder一致效果
//          shape: Border(top: b, bottom: b, right: b, left: b)
// 底部线
//          shape: UnderlineInputBorder( borderSide:BorderSide(color: Color(0xFFFFFFFF), style: BorderStyle.solid, width: 2))
// 矩形边色
//        shape: RoundedRectangleBorder(borderRadius: BorderRadius.all(Radius.circular(10)), side: BorderSide(color: Color(0xFFFFFFFF), style: BorderStyle.solid, width: 2))
// 圆形边色
//        shape: CircleBorder(side: BorderSide(color: Color(0xFFFFFF00), style: BorderStyle.solid, width: 2))
// 体育场（竖向椭圆）
//        shape: StadiumBorder(side: BorderSide(width: 2, style: BorderStyle.solid, color: Color(0xFF00FFFF))
// 角形（八边角）边色
//          shape: BeveledRectangleBorder(borderRadius: BorderRadius.all(Radius.circular(10)), side: BorderSide(color: Color(0xFFFFFFFF), style: BorderStyle.solid, width: 2))
),
  ```

8. UnderlineTabindicator
``` dart
decoration: new UnderlineTabIndicator(
  borderSide: BorderSide(width: 2.0, color: Colors.white),
  insets: EdgeInsets.fromLTRB(0,0,0,10)
),
```

# 圆角的组件ClipRRect

## 顶部圆角

```dart
ClipRRect(
  borderRadius: BorderRadius.vertical(top: Radius.circular(20)),
  child: Container(
    color: Colors.white,
  )
)
```

## 底部圆角

```
ClipRRect(
  borderRadius: BorderRadius.vertical(bottom: Radius.circular(20)),
  child: Container(
    color: Colors.white,
  )
)
```

## 全部圆角

```dart
ClipRRect(
  borderRadius: BorderRadius.all(Radius.circular(20)),
  child: Container(
    color: Colors.white,
  )
)
```

## 特定部分圆角

```dart
ClipRRect(
  borderRadius: BorderRadius.only(topLeft:Radius.circular(20),topRight:Radius.circular(20),bottomLeft:Radius.circular(20),bottomRight:Radius.circular(20)),
  child: Container(
    color: Colors.white,
  )
)
```

# Row和Column
## 常用属性

- crossAxisAlignment：子组件沿着 Cross 轴（在 Row 中是纵轴）如何摆放，其实就是子组件对齐方式，可选值有：
    - CrossAxisAlignment.start：子组件在 Row 中顶部对齐。
    - CrossAxisAlignment.end：子组件在 Row 中底部对齐。
    - CrossAxisAlignment.center：子组件在 Row 中居中对齐。    
    - CrossAxisAlignment.stretch：拉伸填充满父布局。
    - CrossAxisAlignment.baseline：在 Row 组件中会报错。
- mainAxisAlignment：子组件沿着 Main 轴（在 Row 中是横轴）如何摆放，其实就是子组件排列方式，可选值有：
    - MainAxisAlignment.start：靠左排列。
    - MainAxisAlignment.end：靠右排列。
    - MainAxisAlignment.center：居中排列。
    - MainAxisAlignment.spaceAround：每个子组件左右间隔相等，也就是 margin 相等。
    - MainAxisAlignment.spaceBetween：两端对齐，也就是第一个子组件靠左，最后一个子组件靠右，剩余组件在中间平均分散排列。
    - MainAxisAlignment.spaceEvenly：每个子组件平均分散排列，也就是宽度相等。
- MainAxisSize：在主轴方向占有空间的值，默认是max。
    - max：根据传入的布局约束条件，最大化主轴方向的可用空间；
    - min：与max相反，是最小化主轴方向的可用空间；
- VerticalDirection：定义了children摆放顺序，默认是down。
    - down：从top到bottom进行布局；
    - up：从bottom到top进行布局。
## 参考文档
- [Flutter 基础组件之 Row、Column](https://blog.csdn.net/zgcqflqinhao/article/details/85239084)

# PageView
## 常用属性

- scrollDirection
  - 滚动方向,分为 Axis.horizontal(默认) 和 Axis.vertical。
- reverse
  - 反转，是否从最后一个开始算0，默认为false。
- controller
  - initialPage 初始化第一次默认在第几个。
  - viewportFraction 占屏幕多少，1为占满整个屏幕
  - keepPage

    是否保存当前 Page 的状态，如果保存，下次回复对应保存的 page，initialPage被忽略，如果为 false 。下次总是从 initialPage 开始。
- physics

  滚动的方式
  - BouncingScrollPhysics 阻尼效果
  - ClampingScrollPhysics 水波纹效果
- pageSnapping
  - 是否具有回弹效果，默认为 true
- onPageChanged
  - 监听事件
- children
  - 具体子控件的布局

## PageView.build()的sample

### 薪资管理日历pageview的代码
``` dart
PageView.builder(
  onPageChanged: onPageChanged,
  controller: pageController,
  physics: new ScrollPhysics(),
  itemBuilder:(context,index){
    return CalendarBody(
      showCount: DateUtil.weeksInMonth(targetMonth.month, targetMonth.year)*7,
      dates: DateUtil.collectDateTime(targetMonth),
      showBorder: false,
      childAspectRatio: 1,
      onPressed: onPressed,
      targetMonth: targetMonth,
      selectedDay: selectedDay
    );
  }
)
```

# ScrollPhysics详细

### NeverScrollablePhysics

`NeverScrollablePhysics`呈现不可滚动的列表。使用此选项可以完全禁用`ListView`的滚动。

### BouncingScrollPhysics

当列表结束时，`BouncingScrollPhysics`会弹回列表。在iOS上使用类似的效果。

### ClampingScrollPhysics

这是Android上使用的默认滚动物理。 列表在结尾处停止并给出指示的水波纹效果。

### FixedExtentScrollPhysics

这与此列表中的其他内容略有不同，因为它仅适用于`FixedExtendScrollControllers`和使用它们的列表。如下：采用`ListWheelScrollView`来制作类似轮子的列表。

`FixedExtentScrollPhysics`仅滚动到项目而不是其间的任何偏移。

## 参考
https://juejin.im/post/5be1342fe51d45715f654562

# Wrap

## Wrap属性
``` dart
direction
主轴的方向，默认水平
alignment
主轴的对其方式
spacing
主轴方向上的间距
textDirection
文本方向
verticalDirection
定义了 children 摆放顺序，默认是 down，见 Flex 相关属性介绍。
runAlignment
run 的对齐方式。run 可以理解为新的行或者 列，如果是水平方向布局的话，run 可以理解 为新的一行
runSpacing
run 的间距
```

# StickyHeader

可滚动内容上放置标题，当内容滚动时，这些标题将粘在容器的顶部。

引用第三方库:pubspec.ymal
```dart
  sticky_headers: ^0.1.8+1
```

例：
``` dart
class Example extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ListView.builder(itemBuilder: (context, index) {
      return StickyHeader(
        header: Container(
          height: 50.0,
          color: Colors.blueGrey[700],
          padding: EdgeInsets.symmetric(horizontal: 16.0),
          alignment: Alignment.centerLeft,
          child: Text('Header #$index',
            style: const TextStyle(color: Colors.white),
          ),
        ),
        content: Container(
          child: Image.network(imageForIndex(index), fit: BoxFit.cover,
            width: double.infinity, height: 200.0),
        ),
      );
    });
  }
}
```
pub.dev地址：
https://pub.dev/packages/sticky_headers