
- [本地通知](#本地通知)
- [定义键盘按钮](#键盘自定义按钮)
- [多语言国际化](#多语言国际化)
- [sqflite](#sqflite)



# 本地通知
- [本地通知的例子](https://bitbucket.org/vipinvijayan1987/tutorialprojects/src/LocalNotifications/FlutterTutorialProjects/flutter_demos)
- [教学视频](https://itnext.io/local-notifications-in-flutter-6136235e1b51)

## ios
- 配置

  AppDelegate.m / AppDelegate.swift文件的didFinishLaunchingWithOptions方法添加以下语句

Objective-C：
``` Objective-C
if (@available(iOS 10.0, *)) {
  [UNUserNotificationCenter currentNotificationCenter].delegate = (id<UNUserNotificationCenterDelegate>) self;
}
```

swift:
``` swift
if #available(iOS 10.0, *) {
  UNUserNotificationCenter.current().delegate = self as? UNUserNotificationCenterDelegate
}
```

例：
``` swift
import UIKit
import Flutter

@UIApplicationMain
@objc class AppDelegate: FlutterAppDelegate {
  override func application(
    _ application: UIApplication,
    didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
  ) -> Bool {
    GeneratedPluginRegistrant.register(with: self)
    if #available(iOS 10.0, *) {
      UNUserNotificationCenter.current().delegate = self as? UNUserNotificationCenterDelegate
    }
    
    return super.application(application, didFinishLaunchingWithOptions: launchOptions)
  }
}

```



## Android
- 配置  
  文件：android\app\src\main\AndroidManifest.xml 

  [sample](https://bitbucket.org/vipinvijayan1987/tutorialprojects/src/LocalNotifications/FlutterTutorialProjects/flutter_demos/android/app/src/main/AndroidManifest.xml)
``` xml
# 端末が起動されたときに通知を受ける権限を要求する
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>

# ~~~
# 再起動時およびアプリケーションの更新後も通知のスケジュールを確実に維  持するために必要
<receiver android:name="com.dexterous.flutterlocalnotifications.ScheduledNotificationBootReceiver">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="android.intent.action.MY_PACKAGE_REPLACED"/>
    </intent-filter>
</receiver>

# ~~~
# プラグインがスケジュールされた通知の表示を処理するために必要
<receiver android:name="com.dexterous.flutterlocalnotifications.ScheduledNotificationReceiver" />

# ~~~
# (省略可)Android通知のバイブレーションパターンをカスタマイズする場合に必要
<uses-permission android:name="android.permission.VIBRATE" />
```

- icon设置
  - Android Studio打开安卓工程
  - android/app/src/main/res > 右键 > New > Image Asset
  - 个人更喜欢直接用lancher图片
  
    本地通知安卓配置代码
     ``` dart
    AndroidInitializationSettings('@mipmap/ic_launcher');
    ```

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

# sqflite

- 日文依赖库文档(https://flutter.ctrnost.com/logic/sqlite/)
- sqflite使用demo(https://blog.csdn.net/weixin_39852620/article/details/103332636)







