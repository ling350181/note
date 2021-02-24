- [submodule](#submodule)

# submodule

- 新建flutter工程
    - --org: organaizationを指定します。通常、Reverse domain nameで指定します
    - -i swift -a kotlin: デフォルトでプロジェクトのネイティブコードは、Objective-CとJavaで生成されますが、SwiftとKotlinを使いたい場合は、このオプションで指定します。

``` cmd
flutter create --org com.xunjin --template=plugin --platforms=android,ios -a kotlin -i swift flutter-common

```

- 追加对象的路径内

git submodule add https://github.com/ling350181/flutter_customize_calendar.git
