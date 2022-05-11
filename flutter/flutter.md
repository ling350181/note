- [本地通知](#本地通知)
- [定义键盘按钮](#键盘自定义按钮)
- [多语言国际化](#多语言国际化)
- [sqflite](#sqflite)
- [flutter的http通信](#flutter的http通信)
- [Provider状态管理](#Provider状态管理)
- [Health](#Health)
- [app生命周期](#app生命周期)
- [本地相册](#本地相册)
- [图片压缩](#图片压缩)


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

- 中文官方文档
  - https://book.flutterchina.club/chapter13/multi_languages_support.html
  - https://flutter.cn/docs/development/accessibility-and-localization/internationalization#setting-up
- 参考教程
  - https://blog.csdn.net/zhongad007/article/details/106470787/
  - https://qiita.com/tetsukick/items/962505028d14006af370

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

    - [sql基本类](#sql基本类)
    - [创建TABLE](#创建TABLE)
    - [演算子](#演算子)
    - [升顺和降顺](#升顺和降顺)
    - [主key](#主key)
    - [結合](#結合)
    - [統合・集合](#統合集合)
    - [集計](#集計)

## sql基本类
- [基类](https://github.com/ling350181/flutter_common/blob/master/lib/plugin/database/base_db_provider.dart)

## 创建TABLE

```sql
CREATE TABLE テーブル名(カラム名 PRIMARY KEY, ...);
```
## 演算子
- A = B（A と B が等しい）
- A <> B（A と B が等しくない）
- A > B（A より B が小さい）
- A < B（A より B が大きい）
- A >= B（A が B 以上）
- A <= B（A が B 以下）
- IS NULL

「IS NULL演算子」とは、その名の通り「NULLかどうかをチェックしてくれる演算子」です。主に「WHERE」の中の判定文の中で使用されます。

逆の場合はIS NOT NULL
~~~
SELECT * FROM user WHERE name IS NULL;
SELECT * FROM user WHERE name IS NOT NULL;
~~~
- [A] BETWEEN [B] AND [C]

BETWEEN は [A] が [B] <= [A] <= [C] であった時に True を返す論理演算子です。
- [A] LIKE [パターン]
~~~
SELECT  *
FROM    Customers
WHERE   LastName LIKE '%yama%';
~~~
- [A] IN ([B], [C], ..)
- AND,OR
- EXISTS (サブクエリー)

EXISTS は、続くサブクエリが行を含む場合に True を返す論理演算子です。
~~~
SELECT  *
FROM    Customers AS C
WHERE   EXISTS
        (SELECT *
         FROM   Orders AS O
         WHERE  O.CustomerID = C.CustomerID
            	AND O.StoreCity = C.AddressCity);
~~~

## 升顺和降顺
- ASC...升顺
- DESC...降顺

```sql
SELECT * FROM user ORDER BY id DESC, name ASC;
```

## 主key
- 自动设置的主key
  - PRIMARY KEY AUTOINCREMENT
  ```sql
  CREATE TABLE テーブル名(
    カラム名1 INTEGER PRIMARY KEY AUTOINCREMENT,
    カラム名2 TEXT NOT NULL
  );
  ```
- 单个主key
  - PRIMARY KEY
  ```sql
  CREATE TABLE テーブル名(
    カラム名1 TEXT NOT NULL PRIMARY KEY,
    カラム名2 TEXT NOT NULL
  );
  ```
- 复数个主key
  - PRIMARY KEY(カラム名1, カラム名2, ...)
  ```sql
  CREATE TABLE テーブル名(カラム名1, カラム名2, ... ,
  PRIMARY KEY(カラム名1, カラム名2, ...));
  ```

## 結合
sql语句：
```sql
SELECT *
  FROM 結合元のテーブル名(左)
  JOIN 結合先のテーブル名(右)
    ON 両テーブルの結合条件

SELECT *
  FROM users -- 結合元の左テーブル
  JOIN posts --　結合先の右テーブル
    ON users.id = posts.user_id -- 左テーブルのidと右テーブルのuser_idが一致するもの
```

## INNER JOIN(内部結合)
JOIN と INNER JOINは同じです
1. 右テーブルの行数に合わせて左テーブルの行数を複製する
2. 結合相手がいない行は結合結果から消滅する

## 外部結合(OUTER JOIN)
条件に一致させた状態で結合してくれるのに加え、どちらかのテーブルに存在しないもの、NULLのものに関しても強制的に取得してくれます。
- LEFT JOIN
- RIGHT JOIN
- FULL JOIN

```bash
LEFT JOIN = LEFT OUTER JOIN
RIGHT JOIN = RIGHT OUTER JOIN
FULL JOIN = FULL OUTER JOIN
```

## まとめ
| 名前 | 種類 | 内容 |
| --- | --- | --- |
| JOIN | 内部結合 | 右テーブルの行数に合わせて左テーブルの行数を複製する<br>結合相手がいない行は結合結果から消滅する |
| LEFT JOIN | 左外部結合 | 左の行は強制的に全て表示する<br>条件に合わないものは、右テーブルに値が全てNULLである行を生成して結合する |
| RIGHT JOIN | 右外部結合 | 右の行は強制的に全て表示する<br>条件に合わないものは、右テーブルに値が全てNULLである行を生成して結合する |
| FULL JOIN | 完全外部結合 | 左右の全テーブルを全て表示させる |

- [参照](https://qiita.com/ngron/items/db4947fb0551f21321c0)

## 統合・集合
sql语句
```sql
SELECT  列名  FROM  表名1
UNION
SELECT  列名  FROM  表名2
```
- UNION：重複を含めない（重複があったら除外）
- UNION ALL：重複を含む（すべて抽出）

## 集計
```bash
GROUP BYとは、グループ化を行うために使用される命令です。
```
例：
```bash
table:
+--------+------------+
| name   | team       |
+--------+------------+
| 山田   | チームA    |
| 鈴木   | チームA    |
| 加藤   | チームA    |
| 田中   | チームB    |
| 小林   | チームB    |
+--------+------------+
```
```sql
SELECT team, COUNT(team) FROM user GROUP BY team;
```
結果
```sql
+------------+-------------+
| team       | COUNT(team) |
+------------+-------------+
| チームA    |           3 |
| チームB    |           2 |
+------------+-------------+
```

## 「WHERE」と「HAVING」
- 「WHERE」・・・「GROUP BY」の影響の前に条件が適応される
- 「HAVING」・・・「GORUP BY」の影響のあとに条件が適応される

# flutter的http通信

## 方法1:http package
### 参考
https://qiita.com/teracy55/items/de991ddabcf8531208e2

## 方法2:HttpClient

例
``` dart
try {
      var request = await httpClient.getUrl(Uri.parse(path));
      var response = await request.close();
      if (response.statusCode == HttpStatus.ok) {
        var json = await response.transform(utf8.decoder).join();
        var data = jsonDecode(json);
        return data;
      } else {
        result =
        '$path 获取失败:\nHttp status ${response.statusCode}';
      }
    } catch (exception) {
      result = '$path json解析失败';
    }
```

# Provider状态管理

## 引用依赖库
- https://pub.dev/packages/provider
``` yaml
  provider: ^5.0.0
```

## ChangeNotifierProvider

1. 单个provider
``` dart
 return ChangeNotifierProvider(
      create: (_) => CalendarProvider(),
      builder: (context, child) {
        ...
```
2. 多个provider
``` dart
return MultiProvider(
        providers: [
          ChangeNotifierProvider(
            create: (context) => SettingProvider(),
          ),
          ChangeNotifierProvider(
            create: (context) => NotificationProvider())
        ],
```

## Provider和Consumer

``` dart
return ChangeNotifierProvider(
      create: (context) => CalendarProvider(),
      builder: (context, child) {
        return Consumer<CalendarProvider>(
          builder: (context, provider, child) {
            return Scaffold(
              ...
```

## Provider和Selector
``` dart
return ChangeNotifierProvider<ShiftProvider>(
        create: (context) => ShiftProvider(shiftModel: this.shiftModel),
        child: Selector<ShiftProvider, ShiftProvider>(
            selector: (context, provider) => provider,
            shouldRebuild: (_, model) => model.needRebuild,
            builder: (context, provider, child) {
```

# Health

## iOS
Health Careの取り込み
1. Info.plistの追記
```plist
<key>NSHealthShareUsageDescription</key>
<string>We will sync your data with the Apple Health app to give you better insights</string>
<key>NSHealthUpdateUsageDescription</key>
<string>We will sync your data with the Apple Health app to give you better insights</string>
```
2. Enable "HealthKit" inside the "Capabilities" tab
## Android
Google Fitの取り込み
1. console.developers.google.comでFitness APIを有効化
2. console.developers.google.comでOAuth同意画面を作成する
3. Fitness API→認証情報からOAuth 2.0 クライアント IDを作成する
4. manifestファイルにpermissionを追記
```xml
<uses-permission android:name="android.permission.ACTIVITY_RECOGNITION"/>
```

# app生命周期

## 取得できるアプリの状態変化
- detached

  Flutterエンジン上で動作していながらもビューが無い状態。エンジンの初期起動時や Navigator.pop によってビューが破棄された際になるとのことです。

  実際にテストしてみたところ、Android ではバックボタンでアプリからホーム画面に戻った際にこの状態になっていました。
- inactive

  ユーザーからの入力を受け取らない非アクティブ状態。

  iOSでは、例えば、着信があった際やTouchIDの要求画面の表示、コントロールセンターを開いた時、UIViewControllerのトランジション時など。
- paused

  アプリがユーザーに非表示であり入力も受け付けず、バックグラウンドで動作している状態。Android における onPause() に相当。

  この状態の時、コールバック Window.onBeginFrame および Window.onDrawFrame は呼ばれないとのこと。

  実機では完全にホーム画面に戻った時に paused となります。
- resume

  アプリがフォアグラウンドに移行した時。

  アプリは可視状態となり、入力も受け取るようになる。Android における onPostResume() に相当。
- suspending(Androidのみ)

  アプリの一時停止状態。Android における onStop() に相当。iOSには相当するイベントは存在しない。

## アプリの状態変化を監視し、callback
```dart
class _LifeCycleManagerState extends State<LifecycleManager>
    with WidgetsBindingObserver {
  @override
  void initState() {
    WidgetsBinding.instance.addObserver(this);
    super.initState();
  }

  @override
  void dispose() {
    WidgetsBinding.instance.removeObserver(this);
    super.dispose();
  }

   @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    print('state = $state');
    switch (state) {
      case AppLifecycleState.resumed:
        widget.callback?.onResumed();
        break;
      case AppLifecycleState.inactive:
        widget.callback?.onInactive();
        break;
      case AppLifecycleState.paused:
        widget.callback?.onPaused();
        break;
      case AppLifecycleState.detached:
        widget.callback?.onDetached();
        break;
    }
  }
```

## 关于app生命周期
https://zhuanlan.zhihu.com/p/83603371

# 本地相册

## Package
```yaml
photo_manager: ^1.3.10
```
https://pub.dev/packages/photo_manager

## 配置
- iOS
```plist
<key>NSPhotoLibraryUsageDescription</key>
<string>App need your agree, can visit your album</string>
```

- Android
- android/build.gradle
  - Kotlin 版本 ( ext.kotlin_version)升级到1.4.32或最新版本。
- android/gradle/wrapper/gradle-wrapper.properties
  - Gradle 版本（中的那个gradle-wrapper.properties）升级到6.8.3或 最新版本但低于7.0.0.

现在，插件的android部分使用api 29来编译插件，所以你的android sdk环境必须包含api 29（androidQ）

如果你的compileSdkVersion和targetSdkVersion都在28以下，可以使用PhotoManager.forceOldApi强制旧的api访问相册。如果您不确定这部分，请不要调用此方法。而且，我建议你添加android:requestLegacyExternalStorage="true"到你的AndroidManifest.xml

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="top.kikt.imagescannerexample">

    <application
        android:name="io.flutter.app.FlutterApplication"
        android:label="image_scanner_example"
        android:requestLegacyExternalStorage="true"
        android:icon="@mipmap/ic_launcher">
    </application>
</manifest>
```

## 使用方法
```dart
List<AssetPathEntity> list = await PhotoManager.getAssetPathList(
  type:RequestType.image,
  onlyAll : true,
  hasAll : true,
);

List<AssetEntity> assetEntity = list.entity[0].getAssetListPaged(0,60);
```
## 各类参数
- hasAll
  - 包含所有的app，比如ios的"Camera Roll",Android的"Recent"
- onlyAll
  - 如果为真，则只返回一个包含所有项目的相册
- filterOption
  - 过滤选项
  - https://pub.dev/packages/photo_manager#filteroption

## requestPermissionExtend
在iOS14中，Apple将“LimitedPhotos Library”添加到iOS。当用户选择部分照片许可时候，我们必须通过PhotoManager.requestPermissionExtend()来请求许可，
否则没有办法收集到最新照片
```dart
PhotoManager.requestPermissionExtend();
```
以上请求许可，返回用户的许可状态PermissionState
```dart
/// 部分许可
PermissionState.limited
/// 许可
PermissionState.authorized
/// 拒绝
PermissionState.denied
/// 应用无权访问照片库，用户无权授予权限
PermissionState.restricted
/// 用户尚未设置应用的授权状态
PermissionState.notDetermined
```

# 图片压缩

## 第三方sdk
https://pub.dev/packages/flutter_image_compress
```ymal
flutter_image_compress: ^1.1.0
```
## 配置
可能需要将Kotlin更新到版本1.3.72或更高版本。

## 使用
```dart
// 从uint8List取照片
Image decodedImage = await decodeImageFromList(uint8List);
// 图片大小1000kb
int fileSizeLimit = 1000000;

int _count = 1;
while(fileSizeLimit < uint8List.lengthInBytes){
  int minWidth = decodedImage.width ~/ _count.toInt();
  int minHeight = decodedImage.height ~/ _count.toInt();

  int quality = 100 ~/ _count.toInt();

  uint8List = await FlutterImageCopress.compressWithList(
    uint8List,
    minWidth: minWidth,
    minHeight: minHeight,
    quality: quality,
  );
  _count++;
}
return uint8List;
```