- [submodule](#submodule)
- [复原文件](#复原文件)
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