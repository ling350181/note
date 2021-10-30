# FCM設定編
- [参考](https://zuma-lab.com/posts/flutter-fcm-push-notify-settings)

# FCM実装編
1. pubspec.yaml
```yaml
 firebase_core:
 firebase_messaging:
 flutter_local_notifications:
```
2. ios許可
```dart
await FirebaseMessaging.instance.requestPermission(
    alert: false,
    badge: true,
    sound: true,
);
```

3. Device Token
```dart
// FCM device tokenの取得
FirebaseMessaging.instance.getToken();
```

4. 监听推送
- 应用在前台

当应用在前台时，收到消息需要通过FirebaseMessaging中的onMessage，它是一个Stream，我们通过listen监听，它返回的是RemoteMessage对象。同时应用在前台时，也不会发出系统Notification，业务需要的话，需手动发出。(该方法同7.0.3 onMessage)

对应应用在前台，不会发出系统Notification，只发送消息，如需系统Notification。则需要手动通过flutter_local_notifications第三方库发送。

应用在前台时，发送推送需要添加以下的许可。
```dart
await FirebaseMessaging.instance.setForegroundNotificationPresentationOptions(
  alert: true, // Required to display a heads up notification
  badge: true,
  sound: true,
);
```

```dart
FirebaseMessaging.onMessage.listen((RemoteMessage message) {
  RemoteNotification notification = message.notification;
  AndroidNotification android = message.notification?.android;

  // If `onMessage` is triggered with a notification, construct our own
  // local notification to show to users using the created channel.
  if (notification != null && android != null) {
    flutterLocalNotificationsPlugin.show(
        notification.hashCode,
        notification.title,
        notification.body,
        NotificationDetails(
          android: AndroidNotificationDetails(
            channel.id,
            channel.name,
            channel.description,
            icon: android?.smallIcon,
            // other properties...
          ),
        ));
  }
});

```
- 应用在后台

对应的方法是onMessageOpenedApp，点击了Notification，打开App的数据流了。它是一个Stream，也是通过listen监听它，获取相应的数据，然后就可以进行一些回调。
```dart
/// 应用在后台，点击推送消息进行页面跳转
FirebaseMessaging.onMessageOpenedApp.listen((RemoteMessage message) {
      if (message.data['type'] == 'chat') {
        Navigator.pushNamed(context, '/chat',
          arguments: ChatArguments(message));
      }
    });
```

- 应用被杀掉

当App被杀掉后，onMessageOpenedApp方法就凉凉了，这时候该getInitialMessage方法登场了(对应onLaunch方法)。它会获取最近一次的Remote消息。

```dart
   // Get any messages which caused the application to open from
    // a terminated state.
    RemoteMessage initialMessage =
        await FirebaseMessaging.instance.getInitialMessage();

    // If the message also contains a data property with a "type" of "chat",
    // navigate to a chat screen
    if (initialMessage != null && initialMessage.data['type'] == 'chat') {
      Navigator.pushNamed(context, '/chat',
          arguments: ChatArguments(initialMessage));
    }
```

调用firebase服务
```dart
/// Define a top-level named handler which background/terminated messages will
/// call.
///
/// To verify things are working, check out the native platform logs.
Future<void> _firebaseMessagingBackgroundHandler(RemoteMessage message) async {
  // If you're going to use other Firebase services in the background, such as Firestore,
  // make sure you call `initializeApp` before using other Firebase services.
  await Firebase.initializeApp();
  print('Handling a background message ${message.messageId}');
}
await Firebase.initializeApp();

// Set the background messaging handler early on, as a named top-level function
FirebaseMessaging.onBackgroundMessage(_firebaseMessagingBackgroundHandler);

/// push通知许可
await FirebaseMessaging.instance.requestPermission(
    alert: false,
    badge: true,
    sound: true,
);

/// Update the iOS foreground notification presentation options to allow
/// heads up notifications.
await FirebaseMessaging.instance.setForegroundNotificationPresentationOptions(
  alert: true, // Required to display a heads up notification
  badge: true,
  sound: true,
);
```
