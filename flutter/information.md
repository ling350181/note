- [创建flutter项目](#创建flutter项目)
- [fvmでバージョン管理](#fvmでバージョン管理)
- [app名字本地化](#app名字本地化)
- [安卓版本发布](#安卓版本发布)
- [flutter SDK channel](#flutter频道)


# 创建flutter项目
```bash
flutter create --org com.yourdomain your_app_name
```
# fvmでバージョン管理

## fvm のインストール
```bash
dart pub global activate fvm
```
warnning出ていたら、下記記事を参照に環境変数を設定する
- [Windows](https://qiita.com/idani/items/0e45d037b4c2a93840a7)
- Mac
  - .bashrcや.zshrcへ環境変数を設定する
    ```bash
    export PATH="$PATH":"$HOME/.pub-cache/bin"
    ```
  - 変更を適用する
    ```bash
    exec $SHELL -l
    ```
- 次のコマンドが実行できたら、fvmへパスが通っています
  ```bash
  fvm --version
  ```

## MinGWのインストール
- [Windows](https://note.com/tango9512357/n/nf4c237ebe684#0IZTI)
  - もしflutterSdkPathはC:\src\flutterで設定されている場合、下記の環境変数の設定も必要です。
  ```bash
  C:\src\flutter\bin\cache\dart-sdk\bin
  ```
- Mac
  - Macの場合、MinGWのインストールは不要です。

## fvmでバージョン管理
- 指定したFlutterバージョンをインストール
  ```bash
  $ fvm install <バージョン>
  ```
- 次のコマンドで、fvm flutterでなく、単にflutterでfvmでインストールしたFlutterを使えるようになります。
  ```bash
  fvm use <バージョン> global
  ```
## windowsでそのまま実行する、下記のエラーが出るかも
```bash
Usage Exception: Seems you don't have the required permissions on C:\Users\xxx\fvm On Windows FVM requires to run as an administrator or turn on developer... 
```
原因は管理者権限のコマンドラインで実行してないからです。
```bash
fvm config --cache-path "C:\dev\flutter\versions"
# コンフィグを確認
fvm config
```

# app名字本地化

## ios
- 教程(https://blog.csdn.net/qq_39860954/article/details/84568609)

## Android

- 打开文件：android\app\src\main\AndroidManifest.xml 
  修改：android:label="@string/app_name"

- 然后在 android\app\src\main\res 下面，新建对应的文件夹，并放入一个strings.xml即可。
  比如  
  values-zh/strings.xml 

  values-en/strings.xml

  ※默认文件的values里values/strings.xml的添加莫忘！否则会出错

- string.xml内容如下

``` xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">你的应用名字</string>
</resources>
```

# 安卓版本发布

- 版本号管理
  - build-name=版本名
  - build-number=版本号

  用以下代码打包

  ``` cmd
  flutter build apk --build-name=1.0.1 --build-number=2
  ```

# flutter频道

- flutter SDK channel
   1. master
   2. dev
   3. beta
   4. stable(稳定版,推荐使用)

- channel切换

``` cmd
flutter channel stable
```

- 版本更新升级
```cmd
flutter upgrade
```
