
- [本地通知](#本地通知)
- [ExpansionPanelList](#ExpansionPanelList)
- [定义键盘按钮](#键盘自定义按钮)
- [Decoration背景设定](#Decoration背景设定)
- [多语言国际化](#多语言国际化)
- [TextField](#TextField)



# 本地通知
- [本地通知的例子](https://bitbucket.org/vipinvijayan1987/tutorialprojects/src/LocalNotifications/FlutterTutorialProjects/flutter_demos)
- [教学视频](https://itnext.io/local-notifications-in-flutter-6136235e1b51)


# ExpansionPanelList
- [中文教程](https://www.jianshu.com/p/ee9aa62caaee)
- [踩坑点](./memo.md)

# 键盘自定义按钮

- [教程博客](https://blog.usejournal.com/keyboard-done-button-ux-in-flutter-ios-app-3b29ad46bacc)

## 具体思路如下：

1. 新建一个按钮控件

```dart
class InputDoneView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      width: double.infinity,
      color: Color(Const.doneButtonBg),
      child: Align(
        alignment: Alignment.topRight,
        child: Padding(
          padding: const EdgeInsets.only(top: 4.0, bottom: 4.0),
          child: CupertinoButton(
            padding: EdgeInsets.only(right: 24.0, top: 8.0, bottom: 8.0),
            onPressed: () {
              FocusScope.of(context).requestFocus(new FocusNode());
            },
            child: Text(
              "Done",
              style: TextStyle(color: Color(Const.colorPrimary),fontWeight: FontWeight.bold)
            ),
          ),
        ),
      ),
    );
  }
}

```

2. Hide/show logic
决定按钮显示和隐藏的时机和位置，这里用到一个叫Overlay的外部控件
``` dart
showOverlay(BuildContext context) {
    if (overlayEntry != null) return;
    OverlayState overlayState = Overlay.of(context);
    overlayEntry = OverlayEntry(builder: (context) {
      return Positioned(
          bottom: MediaQuery.of(context).viewInsets.bottom,
          right: 0.0,
          left: 0.0,
          child: InputDoneView());
    });

    overlayState.insert(overlayEntry);
  }

  showOverlay() {
    if (overlayEntry != null) {
      overlayEntry.remove();
      overlayEntry = null;
    }
  }
```

3. 控件实现的俩种方式
    1. 通过外部插件keyboard_visibility
    ``` dart
    KeyboardVisibilityNotification().addNewlistener(
        onShow:(){
            showOverlay(context);
        },
        onHide:(){
            showOverlay();
        },
    );

    ```
    2. 通过TextField的焦点来控制
    ``` dart
    focusNode.addListener(() {
      if (value.focusNode.hasFocus) {
        showOverlay(context);
      } else {
        removeOverlay();
      }
    });

    ```
注意点：
可能会出现一下错误：
> Looking up a deactivated widget's ancestor is unsafe

参照[踩坑点记录来解决](./memo.md#小部件祖先不安全)


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

# 多语言国际化

- [中文官方文档](https://book.flutterchina.club/chapter13/multi_languages_support.html)
- [参考教程](https://blog.csdn.net/zhongad007/article/details/106470787/)

1. 在pubspec.yaml添加依赖
``` dart
dependencies:
  flutter:
    sdk: flutter
  flutter_localizations: # Add this line
    sdk: flutter         # Add this line
```
2. 设置MaterialApp
``` dart
MaterialApp(
  theme: ThemeData(
    primarySwatch: Colors.pink,
    visualDensity: VisualDensity.adaptivePlatformDensity,
    ),
    home: TabBarPage(),
    localizationsDelegates: [
      GlobalCupertinoLocalizations.delegate,// 对应的Cupertino风格
      GlobalMaterialLocalizations.delegate,// 指定本地化的字符串和一些其他的值
      GlobalWidgetsLocalizations.delegate,// 指定默认的文本排列方向, 由左到右或由右到左
      SimpleLocalizationDelegate.delegate// 自定义国际化内容
    ],
    supportedLocales: [
      const Locale("zh","CN"),
      const Locale("ja","JP"),
      const Locale("en","US")
    ],
),
```

注意：如果要指定语言代码、文字代码和国家代码，可以进行如下指定方式：
``` dart
// Full Chinese support for CN, TW, and HK
supportedLocales: [
  const Locale.fromSubtags(languageCode: 'zh'), // generic Chinese 'zh'
  const Locale.fromSubtags(languageCode: 'zh', scriptCode: 'Hans'), // generic simplified Chinese 'zh_Hans'
  const Locale.fromSubtags(languageCode: 'zh', scriptCode: 'Hant'), // generic traditional Chinese 'zh_Hant'
  const Locale.fromSubtags(languageCode: 'zh', scriptCode: 'Hans', countryCode: 'CN'), // 'zh_Hans_CN'
  const Locale.fromSubtags(languageCode: 'zh', scriptCode: 'Hant', countryCode: 'TW'), // 'zh_Hant_TW'
  const Locale.fromSubtags(languageCode: 'zh', scriptCode: 'Hant', countryCode: 'HK'), // 'zh_Hant_HK'
```

3. 获取设备语言
  - localeListResolutionCallback
  设备支持的语言
  - localeResolutionCallback
  当前设备的语言

```dart
//  语言环境列表解析回调
localeListResolutionCallback:(List<Locale> locales, Iterable<Locale> supportedLocales){
  ...
} ,
//  语言环境列表解析回调
localeResolutionCallback: (Locale locale, Iterable<Locale> supportedLocales){
  ...
},
```
4. 创建本地化类

该类用于定义我们需要进行本地化的字符串等信息：
- 我们需要一个构造器，并且传入一个Locale对象（后续会使用到）
- 定义一个Map，其中存放我们不同语言对应的显示文本
- 定义它们对应的getter方法，根据语言环境返回不同的结果

``` dart
class SimpleLocalizations{

  final Locale locale;

  SimpleLocalizations(this.locale);

  // 添加静态方法，方便使用
  static SimpleLocalizations of(BuildContext context){
    return Localizations.of<SimpleLocalizations>(context, SimpleLocalizations);
  }
  static Map<String, Map<String, dynamic>> _localizedValues = {
    "en": {
      "title": "home",
      "greet": "hello~",
      "picktime": "Pick a Time"
    },
    "zh": {
      "title": "首页",
      "greet": "你好~",
      "picktime": "选择一个时间"
    }
  };

  Map<String,dynamic> get _stringMap{
    return _localizedValues[locale.languageCode];
  }

  String get title {
    return _stringMap["title"];
  }

  String get greet {
    return _stringMap["greet"];
  }

  String get pickTime {
    return _stringMap["picktime"];
  }
}
```
5. 自定义Delegate


``` dart
class SimpleLocalizationDelegate extends LocalizationsDelegate<SimpleLocalizations>{

  const SimpleLocalizationDelegate();
  
  @override
  bool isSupported(Locale locale) {
    return ["zh","ja","en"].contains(locale.languageCode);
  }

  @override
  Future<SimpleLocalizations> load(Locale locale) {
    return SynchronousFuture<SimpleLocalizations>(SimpleLocalizations(locale));
  }

  @override
  bool shouldReload(covariant LocalizationsDelegate<SimpleLocalizations> old) {
    return false;
  }

  static SimpleLocalizationDelegate delegate = const SimpleLocalizationDelegate();
  
}
```

6. 使用

在SimpleLocalizations中定义静态方法，方便调用

``` dart
  static SimpleLocalizations of(BuildContext context){
    return Localizations.of<SimpleLocalizations>(context, SimpleLocalizations);
  }
```
方法调用
``` dart
// 静态方法
SimpleLocalizations.of(context).title;
// 自定义语言
SimpleLocalizations(Locale(langageCode)).title;
```
# TextField

- 详解文档(https://blog.csdn.net/yuzhiqiang_1993/article/details/88204031)







