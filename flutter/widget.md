- [ExpansionPanelList](#ExpansionPanelList)
- [ListView](#ListView)
- [PageView](#PageView)
- [TextField](#TextField)
- [Decoration背景设定](#Decoration背景设定)
- [Shape](#Shape)
- [paint](#paint)
- [path](#path)
- [Row和Column](#Row和Column)
- [ScrollPhysics详细](#ScrollPhysics详细)
- [Wrap](#Wrap)
- [圆角的组件ClipRRect](#圆角的组件ClipRRect)
- [CircleAvatar](#CircleAvatar)
- [StickyHeader](#StickyHeader)
- [flutter icon](#flutter图标)
- [RefreshIndicator下拉更新](#RefreshIndicator下拉更新)
- [Divider](#Divider)
- [Visibility](#Visibility)
- [Image](#Image)
- [透明dailog](#透明dailog)
- [NotificationListener](#NotificationListener)

# ExpansionPanelList
- [中文教程](https://www.jianshu.com/p/ee9aa62caaee)
- [踩坑点](./memo.md)

# ListView
- builder属性详细介绍
```dart
// manual 和 onDrag 的效果如下：
// 当前键盘弹出时，如果为manual ，列表滑动过程中键盘不会主动隐藏。
// 为onDrag时，滑动列表时，键盘会主动隐藏 
keyboardDismissBehavior：ScrollViewKeyboardDismissBehavior.manual,
//设置滑动方向 Axis.horizontal 水平  默认 Axis.vertical 垂直
scrollDirection: Axis.vertical,
//内间距
padding: EdgeInsets.all(10.0),
//是否倒序显示 默认正序 false  倒序true
reverse: false,
//false，如果内容不足，则用户无法滚动 而如果[primary]为true，它们尝试滚动。
primary: true,
//确定每一个item的高度 会让item加载更加高效
itemExtent: 50.0,
//内容适配
shrinkWrap: true,
//item 数量
itemCount: list.length,
//滑动类型设置
// AlwaysScrollableScrollPhysics() 总是可以滑动 
// NeverScrollableScrollPhysics 禁止滚动 
// BouncingScrollPhysics 内容超过一屏 上拉有回弹效果 
// ClampingScrollPhysics 包裹内容 不会有回弹
physics: new ClampingScrollPhysics(),
//cacheExtent  设置预加载的区域 
cacheExtent: 30.0, 
//滑动监听
controller ,
```

# PageView

- 主要属性
```dart
PageView({
    Key key,
    this.scrollDirection = Axis.horizontal,     // 页面滑动方向(水平/竖直)
    this.reverse = false,           // 是否反向滑动
    PageController controller,      // 页面控制器
    this.physics,                   // 滑动到首页和末页动画效果
    this.pageSnapping = true,       // 是否整页滑动
    this.onPageChanged,             // 页面监听滑动回调
    List<Widget> children = const <Widget>[],   // Page 页面展示子 Widget
    this.dragStartBehavior = DragStartBehavior.start,
})

PageView.builder({
    Key key,
    this.scrollDirection = Axis.horizontal,
    ...
    this.dragStartBehavior = DragStartBehavior.start,
})

PageView.custom({
    Key key,
    this.scrollDirection = Axis.horizontal,
    ...
    this.dragStartBehavior = DragStartBehavior.start,
})
```

- controller
```dart
class PageController extends ScrollController {
  PageController({
    this.initialPage = 0, // 初始化第一次默认在第几个。
    this.keepPage = true, // 是否保存当前Page的状态，如果保存，下次回复对应保存的 page，initialPage被忽略，如果为false。下次总是从initialPage开始。
    this.viewportFraction = 1.0, // 占屏幕多少，1为占满整个屏幕
  })
}
```
PageController还提供了几个重要的方法，包括animateToPage和jumpToPage等进行具体Page页切换，与其他的Widget联动

- [参考](https://www.jianshu.com/p/625ffd7a353d)


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


# TextField
``` dart
const TextField({
    Key key,
    this.controller,    //编辑框的控制器，跟文本框的交互一般都通过该属性完成，如果不创建的话默认会自动创建
    this.focusNode,  //用于管理焦点
    this.decoration = const InputDecoration(),   //输入框的装饰器，用来修改外观
    TextInputType keyboardType,   //设置输入类型，不同的输入类型键盘不一样
    this.textInputAction,   //用于控制键盘动作（一般位于右下角，默认是完成）
    this.textCapitalization = TextCapitalization.none,
    this.style,    //输入的文本样式
    this.textAlign = TextAlign.start,   //输入的文本位置
    this.textDirection,    //输入的文字排列方向，一般不会修改这个属性
    this.autofocus = false,   //是否自动获取焦点
    this.obscureText = false,   //是否隐藏输入的文字，一般用在密码输入框中
    this.autocorrect = true,   //是否自动校验
    this.maxLines = 1,   //最大行
    this.maxLength,   //能输入的最大字符个数
    this.maxLengthEnforced = true,  //配合maxLength一起使用，在达到最大长度时是否阻止输入
    this.onChanged,  //输入文本发生变化时的回调
    this.onEditingComplete,   //点击键盘完成按钮时触发的回调，该回调没有参数，(){}
    this.onSubmitted,  //同样是点击键盘完成按钮时触发的回调，该回调有参数，参数即为当前输入框中的值。(String){}
    this.inputFormatters,   //对输入文本的校验
    this.enabled,    //输入框是否可用
    this.cursorWidth = 2.0,  //光标的宽度
    this.cursorRadius,  //光标的圆角
    this.cursorColor,  //光标的颜色
    this.keyboardAppearance,
    this.scrollPadding = const EdgeInsets.all(20.0),
    this.dragStartBehavior = DragStartBehavior.down,
    this.enableInteractiveSelection,
    this.onTap,    //点击输入框时的回调(){}
    this.buildCounter,
  })
————————————————
版权声明：本文为CSDN博主「XeonYu」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/yuzhiqiang_1993/article/details/88204031
```

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

# Shape

## ShapeBorder组件

```bash
Material
Card
FloatingActionButton
RawMaterialButton
MaterialButton
    |----FlatButton
    |----RaisedButton
    |----OutlineButton
...

ClipPath

```

## ShapeBorder对象

shape对应ShapeBorder对象 , 它的子类如下:
```bash
ShapeBorder [abstract]
|---BoxBorder [abstract]
    |---BorderDirectional
    |---Border
|---RoundedRectangleBorder
|---ContinuousRectangleBorder
|---CircleBorder
|---InputBorder [abstract]
    |---OutlineInputBorder
    |---UnderlineInputBorder

```

- BoxBorder
  1. BorderDirectional
      
      BoxBorder主要掌管边线方面的事，自身是abstract，不能直接用
  ```dart
  Widget _buildBorderDirectional() {
  return Material(
            color: Colors.orangeAccent,
            shape: BorderDirectional(
                top: BorderSide(
                  color: Colors.white,
                ),
                start: BorderSide(
                  color: Colors.black,
                  width: 15
                ),
              bottom: BorderSide(
                color: Colors.white,
              )
            ),
            elevation: 2,
            child: Container(
              alignment: Alignment.center,
              padding: EdgeInsets.all(10),
              height: 80,
              child: Text(
                "BorderDirectional",
                style: TextStyle(color: Colors.white, fontSize: 20),
              ),
            ),
          );
  }
  ```
  2. Border

      Border通过【top】【bottom】【left】【right】分别控制上下左右的边线,本质上和BorderDirectional并没有什么区别
  ```dart
  Widget _buildBorder() {
  return Material(
    color: Colors.orangeAccent,
    shape: Border(
      top: BorderSide(width: 5.0, color: Color(0xFFFFDFDFDF)),
      left: BorderSide(width: 5.0, color: Color(0xFFFFDFDFDF)),
      right: BorderSide(width: 5.0, color: Color(0xFFFF7F7F7F)),
      bottom: BorderSide(width: 5.0, color: Color(0xFFFF7F7F7F)),
    ),
    ...
  ```
- RoundedRectangleBorder和ContinuousRectangleBorder

  圆角类矩形

- CircleBorder

  圆形

- OutlineInputBorder和UnderlineInputBorder

  常用与输入框的边线


## 参考
https://juejin.cn/post/6844904082629459982

# paint

## 常用参数
```bash
（1） color: 画笔的颜色
（2）strokeWidth: 线的宽度
（3）style： 填充模式，有两种： PaintingStyle.fill : 填充； PaintingStyle.stroke: 空心
（4）strokeCap：转折处的处理
（5）blendMode： 重叠部分的处理，一般在截取Image后合并Image用。
a. src: 只显示源图
b. dst: 只显示目标图
c. srcOver: 都显示，重叠部分是src覆盖在dst的上边；
d. dstOver: 都显示，重叠部分是dst覆盖在src的上边；
e. srcIn; // 只显示重叠部分的src图，透明部分也是不显示的。
f. dstIn; // 只显示重叠部分的dst图，透明部分也是不显示的。
```

## 绘制线段
```dart
// 第一个参数表示起点坐标
// 第二个参数表示终点坐标
// 第三个参数是画笔
canvas.drawLine(Offset(10, 10), Offset(100, 100), p);
```

## 绘制矩形
绘制矩形需要用到Rect， 而React的创建可以通过Offset和Size来完成，Offset 决定他左上角的坐标 Size 决定他的大小。Rect rect = offset & size;
```dart
// 矩形的长宽
Size size = Size(200, 300);
// 矩形左上角的坐标
Offset offset = Offset(200, 200);
// 合成Rect，为什么可以这样合成？ 官方给的！看下边---> 
Rect rect = offset & size;
canvas.drawRect(
    rect,
    Paint()
      ..color = Colors.red
      ..strokeWidth = 3.0);
```

## 绘制多边形
绘制多变形要用到Path的moveTo和lineTo放发，
void moveTo(double x, double y)： 将画笔的起点移动到（x,y)坐标。
void lineTo(double x, double y)： 从当前点绘制一条直线到(x,y)坐标。
```dart
canvas.drawPath(
    Path()
      ..moveTo(30, 200)
      ..lineTo(200, 200)
      ..lineTo(100, 300)
      ..lineTo(50, 330)
      ..lineTo(30, 300)
      ..close(),
    Paint()
      ..color = Colors.lightBlue
      ..strokeWidth = 1.0);
```

## 绘制圆
```dart
// 第一个参数是原点坐标
// 第二个参数是半径
// 第三个参数是画笔
canvas.drawCircle(Offset(100, 100), 50, p);
```

## 虚线

- [sample](././sample/dash_path.dart)

## 参考
- https://www.jianshu.com/p/57714f1cf1a2
- https://juejin.cn/post/6844903919164850189

# path

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

# CircleAvatar

用户的头像。他可以设置颜色和子组件，这样当头像不存在时，一般以颜色和用户名首字母显示

## CircleAvatar的使用

```dart
CircleAvatar(
  backgroundImage: AssetImage('assets/images/icon_head.jpg'),
  radius: 40,
);
```

## 前景图片、背景图片、背景色的关系

前景图片、背景图片、背景色显示的优先级顺序
```dart
foregroundImage > backgroundImage > backgroundColor
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


# flutter图标

- [font awesome](https://fontawesome.com/v5.15/icons?d=gallery&p=2)
- [outline_material_icons](https://fonts.google.com/icons?selected=Material+Iconsw)

pub.dev地址：
- https://pub.dev/packages/font_awesome_flutter

# RefreshIndicator下拉更新
RefreshIndicator的child用ListView可以实现下拉更新。当ListView的长度不够的时候，ListView的physics属性用AlwaysScrollableScrollPhysics()，可以实现下拉更新
```dart
...
child: ListView.builder(
           physics: AlwaysScrollableScrollPhysics(),
           itemCount: itemList.length,
           itemBuilder: (BuildContext context, int i) {
             return ListTile(
               title: Text(itemList[i]),
             );
           },
         ),
...
```

例子：
```dart
@override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.title),
      ),
      body: new RefreshIndicator(
          onRefresh: _onRefresh,
          child: ListView(
            physics: const AlwaysScrollableScrollPhysics(),
            padding: const EdgeInsets.all(8.0),
            children: <Widget>[
              Container(
                padding: const EdgeInsets.all(16.0),
                height: 50,
                color: Colors.amber[600],
                child: Text("Updated at - " + updated_at.toString()),
              ),
            ],
          )),
    );
}
Future<void> _onRefresh() async {
    setState(() {
      updated_at = new DateTime.now();
    });
}
```

# Divider

## Divider的属性
```dart
Divider(
  height: 10,//组件高度
  indent: 20,//左边距
  endIndent: 20,//右边距
  thickness: 1,//线粗细
  color: Colors.orange,//线颜色
),  
```
如果一个应用中需要指定默认的 Divider 样式，每次使用都设置一下显然很麻烦。Flutter中有相关的主题组件 DividerTheme ，其中维护了 DividerThemeData 数据。我们可以通过 该组件使其子节点的  Divider 按照默认样式进行展现
- 在MaterialApp的主主题里可以进行主题设置，这样使用起来的话就不用每次都进行属性设置了
```dart
    theme: ThemeData(
      dividerTheme: DividerThemeData(
        color: Colors.orange,
        thickness: 2,
        space: 5
      ),
```

# Visibility

Opacity,Offstage,Visibility都可以控制控件的显示/隐藏

## Visibility的使用

```dart
Visibility(
  visible: _visible,// true和false来控制组件的显示/隐藏
  child: buildChild(),
),
```

## Visibility的占位组件

想要隐藏时保存尺寸必须maintainSize，maintainAnimation，maintainState要为true
```dart
Visibility(
  visible: _visible,
  child: buildChild(),
  maintainSize: true,// 是否保持大小
  maintainAnimation: true,// 是否停止动画
  maintainState: true,// 是否保持状态
),

```

maintainInteractivity可以控制在隐藏时是否响应事件。

## 参考
https://juejin.cn/post/6995727978034380808

# Image
创建图片有以下几种方式
- Image.asset 
  - 从 AssetBundle创建图片
- Image.network
  - 从网络画创建图片
- Image.file
  - 从本地文件系统创建图片
- Image.memory 
  - 从Uint8List创建图片
  - [图片的Uint8List转换方法](/flutter/dart.md#图片的64basecode转换)

## FadeInImage组件
我们都知道，图片无论是从资源、文件、网络加载，都不会立刻完成，这样会出现短暂的空白，尤其是网络图片。自己处理默认占位图也比较麻烦。FadeInImage 的作用就是：在目标图片加载完成前使用默认图片占位，加载完成后，目标图片会渐变淡入，默认图片会渐变淡出，这样可以既解决图片加载占位问题，渐变的动画在视觉上也不显突兀

简单的使用方法
```dart
class FadeInImageDemo extends StatelessWidget{
  final headUrl =
      'https://sf1-ttcdn-tos.pstatp.com/img/user-avatar/5b2b7b85d1c818fa71d9e2e8ba944a44~300x300.image';
  @override
  Widget build(BuildContext context) {
    return FadeInImage(
      width: 100,
      height: 100,
      placeholder: AssetImage(
        'assets/images/default_icon.png',
      ),
      image: NetworkImage(headUrl),
    );
  }
}
```
- 参考
  - https://juejin.cn/post/6994609469292281892

# 透明dailog
```dart
class Loading {

  static void show(BuildContext context, {bool mateStyle}) {
    Navigator.of(context)
        .push(DialogRouter(LoadingDialog()));
  }

  static void hide(BuildContext context) {
    Navigator.of(context).pop();
  }
}

class LoadingDialog extends Dialog {

  @override
  Widget build(BuildContext context) {
    return WillPopScope(
        child: Material(
          //创建透明层
          type: MaterialType.transparency, //透明类型
          child: Center(
            //保证控件居中效果
            child: CupertinoActivityIndicator(
              radius: 18,
            ),
          ),
        ),
        onWillPop: () async {
          return Future.value(false);
        });
  }
}

class DialogRouter extends PageRouteBuilder{

  final Widget page;

  DialogRouter(this.page)
      : super(
    opaque: false,
    barrierColor: Color(0x00000001),
    pageBuilder: (context, animation, secondaryAnimation) => page,
    transitionsBuilder: (context, animation, secondaryAnimation, child) => child,
  );
}
```
- 参考
  - https://www.jianshu.com/p/421ce2746941

# NotificationListener

## ScrollNotification
- ScrollMetrics

  ScrollNotificationクラスには、metricsプロパティとしてScrollMetricsというクラスのオブジェクトが取得できます。これは、現在のスクロール全体の状態を反映しているプロパティのイメージです。
  - axis
    - Vertical（縦）かHorizontal（横）
  - axisDirection
    - up, down, right, leftのどれか。現在スクロールしている方向ではなく、基準となる方向がどれか（座標の向きのイメージ）
  - extentBefore
    - ウィジェットの初めから現在スクロールしている始点までの大きさ（見えていない手前の部分）
  - extentInside
    - 現在スクロールしている始点から終点までの大きさ（見えている部分）
  - extentAfter
    - 現在スクロールしている終点からウィジェットの終わりまでの大きさ（見えていない後の部分）
  - atEdge
    - スクロール位置が一番端にいるかどうか（=extentBefore or extentAfterが0）
  - maxScrollExtent
    - スクロール可能な最大値
  - minScrollExtent
    - スクロール可能な最小値
  - viewportDimension
    - 画面の大きさ
  - pixels
    - スクロールの位置（画面の始点の端を0とする）

## UserScrollNotification

- ScrollDirection

  ScrollDirectionクラスのオブジェクトが取得可能。種類は、forward, idle, reverseの3つ。

  - forward
    - スクロールの始点に向かってスクロールした時（ScrollMetricsのAxisDirectionがdownの場合は上方向にスクロールしている時）
  - reverse
    - スクロールの終点に向かってスクロールした時（ScrollMetricsのAxisDirectionがdownの場合は下方向にスクロールしている時）
  - idle
    - ユーザーのスクロールが完了した時


## 参考
https://qiita.com/youmeee/items/6ca30bf66646b1639076