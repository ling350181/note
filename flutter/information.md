- [app名字本地化](#app名字本地化)
- [安卓版本发布](#安卓版本发布)
- [flutter SDK channel](#flutter频道)
- [flutter icon](#flutter图标)


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

# flutter图标

[font awesome](https://fontawesome.com/v5.15/icons?d=gallery&p=2)
[outline_material_icons](https://fonts.google.com/icons?selected=Material+Iconsw)