- [ExpansionPanelList](#ExpansionPanelList)
- [TextField](#TextField)
- [Decoration背景设定](#Decoration背景设定)
- [Row和Column](#Row和Column)

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
- [Flutter 基础组件之 Row、Column](#https://blog.csdn.net/zgcqflqinhao/article/details/85239084)