- [ExpansionPanel踩坑点](#ExpansionPanel)
- [Looking up a deactivated widget's ancestor is unsafe](#小部件祖先不安全)
- [firebase_core_web not found. Please update settings.gradle](#firebase_core_web找不到)

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

