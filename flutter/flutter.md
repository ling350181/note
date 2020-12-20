
- [本地通知](#本地通知)
- [ExpansionPanelList](#ExpansionPanelList)
- [定义键盘按钮](#键盘自定义按钮)



# 本地通知
- [本地通知的例子](https://bitbucket.org/vipinvijayan1987/tutorialprojects/src/LocalNotifications/FlutterTutorialProjects/flutter_demos)
- [教学视频](https://itnext.io/local-notifications-in-flutter-6136235e1b51)


# ExpansionPanelList
- [中文教程](https://www.jianshu.com/p/ee9aa62caaee)
- [踩坑点](./memo.md)

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

3. 控件现实的俩种方式
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

参照[踩坑点记录来解决](./memo.md)
