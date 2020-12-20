- [ExpansionPanel踩坑点](#ExpansionPanel)
- [Looking up a deactivated widget's ancestor is unsafe](#Looking up a deactivated widget's ancestor is unsafe)

# ExpansionPanel

## Exception报错

>The following assertion was thrown building ExpansionPanelList(dependencies: [_LocalizationsScope-[GlobalKey#63a3b]], state: _ExpansionPanelListState#61f06):
>'package:flutter/src/material/mergeable_material.dart': Failed assertion: line 456 pos 18: '_children[j] is MaterialGap': is not true.

## 原因

Dynamically adding a new panel with isExpanded = true, I don't get the expected gap.
添加一个新的pannel的时候，一个pannel是展开状态的时候(isExpanded = true)，会出现以上报错

暂时可以避免的措施是新添一个pannel的时候，强行把所有展开的pannel给关闭掉(isExpanded = false)

# Looking up a deactivated widget's ancestor is unsafe
>Looking up a deactivated widget's ancestor is unsafe

解决方法：
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

