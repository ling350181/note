- [submodule](#submodule)
- [复原文件](#复原文件)
- [git取消push](#git取消push)
- [git忽略文件(.gitignore)](#git忽略文件(.gitignore))

# submodule

- 新建flutter工程
    - --org: organaizationを指定します。通常、Reverse domain nameで指定します
    - -i swift -a kotlin: デフォルトでプロジェクトのネイティブコードは、Objective-CとJavaで生成されますが、SwiftとKotlinを使いたい場合は、このオプションで指定します。

``` cmd
flutter create --org com.xunjin --template=plugin --platforms=android,ios -a kotlin -i swift flutter-common

```

- 追加对象的路径内
``` cmd
git submodule add https://github.com/ling350181/flutter_customize_calendar.git
```

- 删除submodule
``` cmd
git submodule deinit -f flutter_customize_calendar
git rm -f flutter_customize_calendar   
```
# 复原文件

- 取commit id
``` cmd
$ git log
```

- 复原文件
``` cmd
$ git checkout commitid 文件名
```
※复原删除文件时，应寻删除之前的commitid

# git取消push

## 对应方法有俩种

1. git reset(不会留下修改履历)
2. git revert(会留下修改履历)

## 方法1.git reset

取消上一次的commit，返回上次commit的状态，可以用下面的comment，option用hard
``` cmd
$ git reset --hard HEAD^
```
当想留下本地修改变更的情况，option可以用soft
``` cmd
$ git reset --soft HEAD^
```
用上面的comment可以取消commit。

git reset之后然remote push
``` cmd
$ git push -f origin HEAD
```

## 注意点
git reset取消一次commit用「HEAD^」，取消n次commit可以用「HEAD~n」

## 方法2.git revert

返回上一次commit，会留下记录
``` cmd
$ git revert HEAD
```
往remote push
``` cmd
$ git push origin HEAD
```

## 参考
https://qiita.com/S42100254h/items/db435c98c2fc9d4a68c2

# git忽略文件(.gitignore)

flutter的.gitignore文件
```gitignore
# Created by https://www.toptal.com/developers/gitignore/api/flutter
# Edit at https://www.toptal.com/developers/gitignore?templates=flutter

### Flutter ###
# Flutter/Dart/Pub related
**/doc/api/
.dart_tool/
.flutter-plugins
.flutter-plugins-dependencies
.packages
.pub-cache/
.pub/
build/
lib/generated_plugin_registrant.dart
.idea/
.history/
pubspec.lock
android/.classpath
android/.settings/org.eclipse.buildship.core.prefs

# Android related
**/android/**/gradle-wrapper.jar
**/android/.gradle
**/android/captures/
**/android/gradlew
**/android/gradlew.bat
**/android/key.properties
**/android/local.properties
**/android/**/GeneratedPluginRegistrant.java

# iOS/XCode related
**/ios/**/*.mode1v3
**/ios/**/*.mode2v3
**/ios/**/*.moved-aside
**/ios/**/*.pbxuser
**/ios/**/*.perspectivev3
**/ios/**/*sync/
**/ios/**/.sconsign.dblite
**/ios/**/.tags*
**/ios/**/.vagrant/
**/ios/**/DerivedData/
**/ios/**/Icon?
**/ios/**/Pods/
**/ios/**/.symlinks/
**/ios/**/profile
**/ios/**/xcuserdata
**/ios/.generated/
**/ios/Flutter/App.framework
**/ios/Flutter/Flutter.framework
**/ios/Flutter/Flutter.podspec
**/ios/Flutter/Generated.xcconfig
**/ios/Flutter/app.flx
**/ios/Flutter/app.zip
**/ios/Flutter/flutter_assets/
**/ios/Flutter/flutter_export_environment.sh
**/ios/ServiceDefinitions.json
**/ios/Runner/GeneratedPluginRegistrant.*
**/ios/Podfile.lock
**/ios/._Runner.xcodeproj
**/ios/._Runner.xcworkspace
**/ios/Flutter/.last_build_id

# Exceptions to above rules.
!**/ios/**/default.mode1v3
!**/ios/**/default.mode2v3
!**/ios/**/default.pbxuser
!**/ios/**/default.perspectivev3
!/packages/flutter_tools/test/data/dart_dependencies_test/**/.packages

# End of https://www.toptal.com/developers/gitignore/api/flutter
```