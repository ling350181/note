- [GitHub個人トークンの設定方法](#GitHub個人トークンの設定方法)
- [commit](#commit)
- [リモートへブランチをpush](#リモートへブランチをpush)
- [submodule](#submodule)
- [复原文件](#复原文件)
- [git取消push](#git取消push)
- [取消merge](#取消merge)
- [.gitignore无效解决办法](#gitignore无效解决办法)
- [git忽略文件(.gitignore)](#git忽略文件gitignore)

# GitHub個人トークンの設定方法
- [参照](https://qiita.com/kz800/items/497ec70bff3e555dacd0)
# commit
```bash
git status #ファイルの編集状態確認
git add . #コミットしたいファイル追加("."ではなくディレクトリを指定する事で個別に追加する事ができる)
# 例git add app #appディレクトリのファイル変更分のみコミット対象にする
git commit -m "コミットコメント" #addしたファイルをコミット
```

# リモートへブランチをpush
```bash
git push origin ブランチ名 
```

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

- submodule clone

包含submodule的库clone的时候加一个option --recursive
~~~
git clone https://*******.git --recursive
~~~
--recursive忘记加了的情况可以用下列的command取得
~~~
git submodule update --init
~~~
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

# 取消merge

```cmd
git reset --merge
```

# gitignore无效解决办法

>创建完以后发现未生效，因为.gitignore只能忽略那些原来没有被追踪的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是无效的
gitignore规则不生效的解决办法，先把本地缓存删除然后更新

```
git rm -r --cached .
git add .
git commit -m 'update .gitignore'
```

# git忽略文件gitignore

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
**/ios/build/
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