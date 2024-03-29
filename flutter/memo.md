- [ExpansionPanel踩坑点](#ExpansionPanel)
- [Looking up a deactivated widget's ancestor is unsafe](#小部件祖先不安全)
- [firebase_core_web not found. Please update settings.gradle](#firebase_core_web找不到)
- [flutter_localizations闪退](#flutter_localizations闪退)
- [local_auth插件个人特征验证](#local_auth插件个人特征验证)
- [The method 'findAncestorStateOfType' was called on null.](#BuildContext为空)
- [google ads更换版本时出现build失败的情况](#谷歌广告build失败)
- [ios发布时Archive失败但是可以build成功](#ios发布时Archive失败)
- [页面bottomBar底部固定控件](#页面bottomBar底部固定控件)
- [Flutterで画像を角丸に切って綺麗にボーダーをつける](#Flutterで画像を角丸に切って綺麗にボーダーをつける)
- [ListView里面再套一个ListView](#ListView里面再套一个ListView)
- [build pods失败](#build_pods失败)
- ['Flutter/Flutter.h' file not found ](#Flutterh文件找不到)
- [ColumnやRowのtextDirectionでエラーが起きる](#ColumnやRowのtextDirectionでエラーが起きる)
- [dialog里输入框点击就刷新问题](#dialog里输入框点击就刷新问题)
- [local_notification的LateInitializationError](#local_notification的LateInitializationError)
- [点击本地通知无法进行PageRoute](#点击本地通知无法进行PageRoute)
- [Target of URI hasn't been generated.'serializers.g.dart'](#serializers生成错误)
- [运行pod install时CocoaPods install失败](#CocoaPods构建失败)
- [Scrollbar的错误](#Scrollbar的错误)

# ExpansionPanel

## Exception报错

>The following assertion was thrown building ExpansionPanelList(dependencies: [_LocalizationsScope-[GlobalKey#63a3b]], state: _ExpansionPanelListState#61f06):
>'package:flutter/src/material/mergeable_material.dart': Failed assertion: line 456 pos 18: '_children[j] is MaterialGap': is not true.

## 原因

Dynamically adding a new panel with isExpanded = true, I don't get the expected gap.
添加一个新的pannel的时候，一个pannel是展开状态的时候(isExpanded = true)，会出现以上报错

暂时可以避免的措施是新添一个pannel的时候，强行把所有展开的pannel给关闭掉(isExpanded = false)

# 小部件祖先不安全
>Looking up a deactivated widget's ancestor is unsafe

### 原因
找不到靠谱的上下文，也就是所传递的上下文无效了。
由于context已经被销毁，变得不可用。请检测后面执行的代码是否依赖了销毁的context。

### 解决方法：
1. 定义一个global参数
``` dart
 final GlobalKey<ScaffoldState> _scaffoldKey = GlobalKey<ScaffoldState>();
```
2.  register the key on your widget build's scaffold eg
``` dart
@override
    Widget build(BuildContext context) {
     return Scaffold(
       key: _scaffoldKey,
       ...
```
3. Pass that scaffold context to the showDialog method
``` dart 
showDialog(context: _scaffoldKey.currentContext ,builder: (context){
  return dialog;
 });
}
```

# firebase_core_web找不到

有Admob广告时，安卓build会出现以下错误
``` log
Plugin project :firebase_core_web not found. Please update settings.gradle.
```

[android] -> [settings.gradle]文件打开，追加下列代码

``` gradle
def flutterProjectRoot = rootProject.projectDir.parentFile.toPath()

def plugins = new Properties()
def pluginsFile = new File(flutterProjectRoot.toFile(), '.flutter-plugins')
if (pluginsFile.exists()) {
    pluginsFile.withReader('UTF-8') { reader -> plugins.load(reader) }
}

plugins.each { name, path ->
    def pluginDirectory = flutterProjectRoot.resolve(path).resolve('android').toFile()
    include ":$name"
    project(":$name").projectDir = pluginDirectory
} 
```

# flutter_localizations闪退

本地通知用第三方插件flutter_localizations时出现闪退，出现下列error

``` log
E/AndroidRuntime(30847): java.lang.RuntimeException: Unable to start receiver com.dexterous.flutterlocalnotifications.ScheduledNotificationReceiver: java.lang.NullPointerException: Attempt to invoke virtual method 'int java.lang.Integer.intValue()' on a null object reference
E/AndroidRuntime(30847): 	at android.app.ActivityThread.handleReceiver(ActivityThread.java:4072)
E/AndroidRuntime(30847): 	at android.app.ActivityThread.access$1600(ActivityThread.java:240)
E/AndroidRuntime(30847): 	at android.app.ActivityThread$H.handleMessage(ActivityThread.java:2067)
E/AndroidRuntime(30847): 	at android.os.Handler.dispatchMessage(Handler.java:107)
E/AndroidRuntime(30847): 	at android.os.Looper.loop(Looper.java:238)
E/AndroidRuntime(30847): 	at android.app.ActivityThread.main(ActivityThread.java:7864)
E/AndroidRuntime(30847): 	at java.lang.reflect.Method.invoke(Native Method)
E/AndroidRuntime(30847): 	at com.android.internal.os.RuntimeInit$MethodAndArgsCaller.run(RuntimeInit.java:492)
E/AndroidRuntime(30847): 	at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:998)
E/AndroidRuntime(30847): Caused by: java.lang.NullPointerException: Attempt to invoke virtual method 'int java.lang.Integer.intValue()' on a null object reference
E/AndroidRuntime(30847): 	at com.dexterous.flutterlocalnotifications.FlutterLocalNotificationsPlugin.setSmallIcon(FlutterLocalNotificationsPlugin.java:228)
E/AndroidRuntime(30847): 	at com.dexterous.flutterlocalnotifications.FlutterLocalNotificationsPlugin.createNotification(FlutterLocalNotificationsPlugin.java:174)
E/AndroidRuntime(30847): 	at com.dexterous.flutterlocalnotifications.FlutterLocalNotificationsPlugin.showNotification(FlutterLocalNotificationsPlugin.java:767)
E/AndroidRuntime(30847): 	at com.dexterous.flutterlocalnotifications.ScheduledNotificationReceiver.onReceive(ScheduledNotificationReceiver.java:46)
E/AndroidRuntime(30847): 	at android.app.ActivityThread.handleReceiver(ActivityThread.java:4056)
```

该错误是初期化安卓本地通知时，插件找不到icon所致。即使安卓设置时有初期化，不知为何还是会出现以上错误

- 安卓设置初期化

``` dart
var initializationSettingsAndroid =
    AndroidInitializationSettings('@mipmap/ic_launcher');
```

- 解决方法如下：

``` dart
var androidChannelSpecifics = AndroidNotificationDetails(
  'CHANNEL_ID 4',
  'CHANNEL_NAME 4',
  "CHANNEL_DESCRIPTION 4",
  importance: Importance.max,
  icon: "@mipmap/ic_launcher",// 设置icon
  priority: Priority.high,
);
var iOSChannelSpecifics = IOSNotificationDetails();
var platformChannelSpecifics =
NotificationDetails(android: androidChannelSpecifics,iOS: iOSChannelSpecifics);
```

# local_auth插件个人特征验证

- local插件出现以下错误

``` log
PlatformException(no_fragment_activity, local_auth plugin requires activity to be a FragmentActivity
```
- 初始Mainactivity.kt
``` kt
package com.example.bio_met

import io.flutter.embedding.android.FlutterActivity

class MainActivity: FlutterActivity() {
}
```

- 替换为下列文件
``` kt
package //your package name for eg.com.example.filename

import androidx.annotation.NonNull;
import io.flutter.embedding.android.FlutterActivity
import io.flutter.embedding.android.FlutterFragmentActivity
import io.flutter.embedding.engine.FlutterEngine
import io.flutter.plugins.GeneratedPluginRegistrant

class MainActivity: FlutterFragmentActivity() {
    override fun configureFlutterEngine(@NonNull flutterEngine: FlutterEngine){
        GeneratedPluginRegistrant.registerWith(flutterEngine);
    }
}
```

# BuildContext为空
``` dart
 Unhandled Exception: NoSuchMethodError: The method 'findAncestorStateOfType' was called on null.
 ```

 通常是由于传入的buildContext为空导致的。

 # 谷歌广告build失败

 - 原因

    AdMob最近将其SDK更新至8.0.0。通过固定Google-Mobile-Ads-SDK到Podfile中的7.69.0版

Podfile
 ``` pod
...
target 'Runner' do
  use_frameworks!
  use_modular_headers!

  flutter_install_all_ios_pods File.dirname(File.realpath(__FILE__))

  # Try adding this
  pod 'Google-Mobile-Ads-SDK', '~> 7.69.0'
end
```
- 运行以下步骤
1. Add to Podfile: pod 'Google-Mobile-Ads-SDK', '~> 7.69.0'
2. Delete Podfile.lock
3. Run: pod install
4. flutter run

- [参考](https://github.com/FirebaseExtended/flutterfire/issues/4931)

# ios发布时Archive失败

- 现象
  
  可以正常build成功,但是发布Archive时，失败了，尝试了以下方法重新build，还是Archive失败
  
  ``` cmd
  $flutter clean
  $rm ios/podfile.lock
  $rm ios/podfile
  $pod update
  $flutter pub get
  ```

- log
  ``` log
  module 'firebase_admob' not found
  ```

- 原因

  ios Archive打开的是Runner.xcodeproj，所以出现以上错误，应该打开Runner.xcworkspace，就解决问题了。

# 页面bottomBar底部固定控件

当想在一个页面底部固定一个widget当时候，可以用以下布局可以实现
  ``` dart
 Column(
  children: [
    Expanded(
      child: SingleChildScrollView()
    ),
    Container(),/// 固定对象
  ]
)
  ```

- 参考：https://zenn.dev/nitaking/articles/ad61015f8ddfae

# Flutterで画像を角丸に切って綺麗にボーダーをつける

1. 一番綺麗な書き方

``` dart
return Container(
      width: 200,
      height: 200,
      foregroundDecoration: BoxDecoration(
        border: Border.all(color: Colors.white, width: 3),
        borderRadius: BorderRadius.circular(10),
      ),
      // 魔法の padding
      padding: const EdgeInsets.all(0.5),
      child: ClipRRect(
        borderRadius: BorderRadius.circular(10),
        clipBehavior: Clip.hardEdge,
        child: Image.network('https://i.picsum.photos/id/563/200/200.jpg'),
      ),
    );
```

- 参考：https://qiita.com/macoshita/items/7a26737946ffbf317384

2. foregroundDecorationを使う
``` dart
return Container(
      width: 200,
      height: 200,
      foregroundDecoration: BoxDecoration( 
        border: Border.all(color: Colors.white, width: 3),
        borderRadius: BorderRadius.circular(10),
      ),
      child: ClipRRect(
        borderRadius: BorderRadius.circular(10),
        clipBehavior: Clip.hardEdge,
        child: Image.network('https://i.picsum.photos/id/563/200/200.jpg'),
      ),
    );
```

3. BoxDecoration で image, border, borderRadius を指定
``` dart
return Container(
      width: 200,
      height: 200,
      decoration: BoxDecoration(
        image: DecorationImage(
          image: NetworkImage('https://i.picsum.photos/id/563/200/200.jpg'),
        ),
        border: Border.all(color: Colors.white, width: 3),
        borderRadius: BorderRadius.circular(10),
      ),
    );
```

# ListView里面再套一个ListView

ListView里想再套一个ListView时会出现下面的error

``` log
════════ Exception caught by rendering library ═════════════════════════════════
'package:flutter/src/rendering/sliver_multi_box_adaptor.dart': Failed assertion: line 544 pos 12: 'child.hasSize': is not true.
The relevant error-causing widget was
ListView
════════════════════════════════════════════════════════════════════════════════

```

- 解决方案

ListView的属性中加入shrinkWrap: true

- 参考

https://stackoverflow.com/questions/53465394/flutter-listview-builder-inside-another-listview

# build_pods失败

出现以下编译错误
```bash
 Unable to find matching .xcframework slice in
    '.../ios/Pods/WebRTC-SDK/WebRTC.xcframework WebRTC framework
    ios-x86_64-simulator ios-arm64' for the current build architectures (arm64 x86_64 i386).
```
- 修改以下内容
  - xcode→Pods→Architectures→Excluded Architectures→any ios simulator sdk ---arm64
- 参考
  - https://issueexplorer.com/issue/CocoaPods/CocoaPods/10978


# Flutter.h文件找不到

```bash
Shared preferences throws 'Flutter/Flutter.h' file not found 
```

Podfile里加上这一行
```bash
post_install do |installer|
  installer.pods_project.targets.each do |target|
    flutter_additional_ios_build_settings(target)
  end
end

```

- 参考
  - https://github.com/flutter/flutter/issues/76594

# ColumnやRowのtextDirectionでエラーが起きる

## 错误
在Row中定义textDirection时出现错误
```dart
TextDirection.rtl
```

The getter 'rtl' isn't defined for the type 'TextDirection'.
编译器变换出现TextDirection.RTL。但是还是出现错误

## 原因
俩个地方都有定义TextDirection，编译出现混乱
```dart
class TextDirection {
  static const LTR = TextDirection._('LTR', 'ltr');
  static const RTL = TextDirection._('RTL', 'rtl');
```
下列文件定义的
```dart
import 'package:intl/intl.dart'
```

## 解决方法
import的时候用as
```dart
import 'package:intl/intl.dart' as hoge

(old) DateFormat('HH:mm').format(date);

(new) format.DateFormat('HH:mm').format(date);
```

## 参考
https://qiita.com/ryota47/items/2a0d789e7eab9bf1fca2

# dialog里输入框点击就刷新问题

## 问题
在dialog中实装输入框TextField时，发生一点击输入框就rebuild的问题

## 解决方案
showDialog下面包一个Scaffold就能解决
```dart
showDialog(
  context: context,
  builder: (BuildContext context){
    return Scaffold(
      backgroundColor: Colors.transparent,
      body: Container(
        child: ...
      ),
    );
  }
)
```

# local_notification的LateInitializationError

## 报错
使用flutter_local_notification的zoneSchedule时，TZdateTime无法设置时，错误
```bash
LateInitializationError: Field '_local@xxxxxxxx' has not been initialized in flutter
```

## 原因
TZDateTime的local是late定义的，还没进行初始化
- [代码](https://github.com/srawlins/timezone/blob/a4f14a018690233294e914d95efcb09bc9dbf0a0/lib/src/env.dart#L20)

## 对应
```dart
import 'package:timezone/data/latest.dart' as tz;

...
// local初始化
tz.initializeTimeZones();
TZDateTime scheduledDate = TZDateTime(
  local,dateTime.year,dateTime.month,dateTime.day,dateTime.hour,dateTime.minute);
```

# 点击本地通知无法进行PageRoute

## 问题
使用本地通知用第三方插件flutter_localizations时，点击通知，无法进行进行页面跳转。

## 原因
flutter_localizations的onSelectNotification里回调
```
Navigator.of(context).push(MaterialPageRoute(...));
```
因为外包了多层的view。context失效。所以路由无效，无法进行页面跳转
```dart
  setOnNotificationClick(Function onNotificationClick) async {
    await notificationsPlugin.initialize(initializationSettings, onSelectNotification:
      (String payload) async{
        onNotificationClick();
      }
    );
  }
```

## 解决方法
把回调写到顶级函数里

# serializers生成错误
open api自动生成的时候出现下面的错误
~~~
Target of URI hasn't been generated: 'serializers.g.dart'.> Documentation
Try running the generator that will generate the file referenced by the URI.```
~~~
可以用以下代码解决
~~~
flutter packages pub run build_runner build --delete-conflicting-outputs
~~~

# CocoaPods构建失败
ios构建时报错。CocoaPods install失败
~~~
Running pod install...
CocoaPods' output:
    Analyzing dependencies

    Inspecting targets to integrate
      Using `ARCHS` setting to build architectures of target `Pods-Runner`: (``)

    Fetching external sources
    -> Fetching podspec for `Flutter` from `.symlinks/flutter/ios`
    -> Fetching podspec for `flutter_plugin` from `.symlinks/plugins/flutter_plugin/ios`

    Resolving dependencies of `Podfile`

    Comparing resolved specification to the sandbox manifest
      A Flutter
      A flutter_plugin

    Downloading dependencies

    -> Installing Flutter (1.0.0)

    -> Installing flutter_plugin (0.0.1)
      - Running pre-install hooks
    [!] Unable to determine Swift version for the following pods:

    - `flutter_plugin` does not specify a Swift version and none of the targets (`Runner`) integrating it has the `SWIFT_VERSION` attribute set. Please contact the author or set the `SWIFT_VERSION` attribute in at least one of the targets that integrate this pod.

    /Library/Ruby/Gems/2.3.0/gems/cocoapods-1.6.1/lib/cocoapods/installer/xcode/target_validator.rb:115:in `verify_swift_pods_swift_version'
    /Library/Ruby/Gems/2.3.0/gems/cocoapods-1.6.1/lib/cocoapods/installer/xcode/target_validator.rb:37:in `validate!'
    /Library/Ruby/Gems/2.3.0/gems/cocoapods-1.6.1/lib/cocoapods/installer.rb:459:in `validate_targets'
    /Library/Ruby/Gems/2.3.0/gems/cocoapods-1.6.1/lib/cocoapods/installer.rb:138:in `install!'
    /Library/Ruby/Gems/2.3.0/gems/cocoapods-1.6.1/lib/cocoapods/command/install.rb:48:in `run'
    /Library/Ruby/Gems/2.3.0/gems/claide-1.0.2/lib/claide/command.rb:334:in `run'
    /Library/Ruby/Gems/2.3.0/gems/cocoapods-1.6.1/lib/cocoapods/command.rb:52:in `run'
    /Library/Ruby/Gems/2.3.0/gems/cocoapods-1.6.1/bin/pod:55:in `<top (required)>'
    /usr/local/bin/pod:22:in `load'
    /usr/local/bin/pod:22:in `<main>'
~~~

## 解决方案
通过将flutter和CocoaPods更新到最新版本然后运行以下命令来修复
~~~
flutter clean
rm -Rf ios/Pods
rm -Rf ios/.symlinks
rm -Rf ios/Flutter/Flutter.framework
rm -Rf ios/Flutter/Flutter.podspec
~~~
对于Apple M1芯片，只需卸载所有已cocoapods安装的gem，然后尝试安装brew。
~~~
sudo gem uninstall cocoapods
brew install cocoapods
~~~

# Scrollbar的错误
错误信息
~~~
The Scrollbar’s ScrollController has no ScrollPosition attached.
~~~

## 解决方案
https://github.com/flutter/flutter/issues/97873#issuecomment-1048677475
```dart
...
child: ScrollConfiguration(
          behavior: HorizontalScrollBehaviour(),
          child: ListView.builder(
              shrinkWrap: true,
              controller: _controller,
              scrollDirection: Axis.horizontal,

...
class HorizontalScrollBehaviour extends ScrollBehavior{

  @override
  Widget buildScrollbar(BuildContext context, Widget child, ScrollableDetails details) {
    return Scrollbar(child: child, controller: details.controller, thumbVisibility: true);
  }
}
```