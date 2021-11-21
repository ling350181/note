- [单例的写法](#单例的写法)
- [factory关键字](#factory关键字)
- [late关键字](#late关键字)
- [用part来拆分库](#用part来拆分库)
- [List和Map的clone](#List和Map的clone)
- [Map](#Map)
- [数组操作方法](#数组操作方法)
- [扩展运算符(...)](#扩展运算符)
- [Future、Isolate和事件循环](#Future、Isolate和事件循环)
- [extension](#extension)
- [密码的正则表现](#密码的正则表现)
- [图片的64basecode转换](#图片的64basecode转换)
- [时间](#时间)

# 单例的写法

```dart
class Singleton {
  static final Singleton _singleton = Singleton._internal();

  factory Singleton() {
    return _singleton;
  }
  
  Singleton._internal();
}
```

# factory关键字

## 3个使用场景

1. 避免创建过多的重复实例，如果已创建该实例，则从缓存中拿出来。
```dart
class Logger {
  final String name;
  // 缓存已创建的对象
  static final Map<String, Logger> _cache = <String, Logger>{}; 

  factory Logger(String name) {
  // 不理解putIfAbsent可以查看文末的简述
    return _cache.putIfAbsent(  
        name, () => Logger._internal(name));
  }

// 私有的构造函数
  Logger._internal(this.name){
    print("生成新实例：$name");
  }
}

// 测试
main() {
  var p1 = new Logger("1");
  var p2 = new Logger('22');
  var p3 = new Logger('1');// 相同对象直接访问缓存

  //identical会对两个对象的地址进行比较，相同返回true，
  //等同于 == ，好处是如果重写了==，那用identical 会更方便。
  print(identical(p1,p3)); 
}
```

2. 调用子类的构造函数(工厂模式 factory pattern)

```dart
abstract class Animal {
  String name;
  void getNoise();
  factory Animal(String type,String name) {
    switch(type) {
      case "cat":
        return new Cat(name);
      case "dog":
        return new Dog(name);
      default:
        throw "The '$type' is not an animal";
    }
  }
}

class Cat implements Animal {
  String name;
  Cat(this.name);
  @override
  void getNoise() {
    print("${this.name}: mew~");
  }
}

class Dog implements Animal {
  String name;
  Dog(this.name);
  @override
  void getNoise() {
    print("${this.name}: wang~");
  }
}

int main(){
  var cat = new Animal("cat","wiki");
  var dog = new Animal("dog","baobao");
  cat.getNoise();
  dog.getNoise();
  return  0;
}
```

3. [singleton pattern实现单例模式](#单例的写法)

# late关键字

1. 显式声明一个非空的变量，但不初始化
```dart
// Using null safety:
class Coffee {
  late String _temperature;
 
  void heat() { _temperature = 'hot'; }
  void chill() { _temperature = 'iced'; }
 
  String serve() => _temperature + ' coffee';
}
 
main() {
  var coffee = Coffee();
  coffee.heat();
  coffee.serve();
```

2. 延迟初始化变量

- temperature变量看起来是在声明时就被初始化了，但因为late关键字的存在，如果temperature这个变量没有被使用的话，_readThermometer()这个函数不会被调用，temperature也就不会被初始化了。

```dart
late String temperature = _readThermometer(); // Lazily initialized.
```
# 用part来拆分库

dart中，通过使用part、part of、library来实现拆分库，这样，就可以将一个庞大的库拆分成各种小库，只要引用主库即可

## 子库calculator
```dart
//和主库建立连接
part of util;

int add(int i, int j) {
  return i + j;
}

int sub(int i, int j) {
  return i - j;
}

int random(int no) {
  return Random().nextInt(no);
}
```

## 子库logger
```dart
//和主库建立连接
part of util;

class Logger {
  String _app_name;
  Logger(this._app_name);
  void error(error) {
    print('${_app_name}Error:${error}');
  }

  void warn(msg) {
    print('${_app_name}Error:${msg}');
  }

  void debug(msg) {
    print('${_app_name}Error:${msg}');
  }
}
```

## 主库util.dart
```dart
//给库命名
library util;

//导入math，子库用到
import 'dart:math';

//和子库建立联系
part 'logger.dart';
part 'calculator.dart';//给库命名
library util;

//导入math，子库用到
import 'dart:math';

//和子库建立联系
part 'logger.dart';
part 'calculator.dart';
```

# List和Map的clone

Dart2.3.0以前可以用下面的方法进行clone
```dart
List<String> clone = []..addAll(originalList);
```
Dart2.3.0之后有更方便的方法
1. List
```dart
List<String> clone = [...originalList];
```

2. Map
```dart
Map<String, int> clone = {...originalMap};
```

# Map
- addEntries
  - MapEntry（キー/値のペア）を要素として末尾へ追加
  - 以下の場合は、Iterable<MapEntry<int, String>>
```dart
void main() {
  final map1 = <int, String>{
    0: 'Banana',
  };
  final map2 = <int, String>{
    0: 'Apple',
    1: 'Peach',
    2: 'Litchi',
  };
  print(map2.entries);
  map1.addEntries(map2.entries);
  print(map1);
}
```
実行結果
```dart
(MapEntry(0: Apple), MapEntry(1: Peach), MapEntry(2: Litchi))
{0: Apple, 1: Peach, 2: Litchi}
```

- putIfAbsent
  - 指定したkeyが無い場合に、追加する

```dart
void main() {
  final map = <int, String>{
    0: 'Apple',
    1: 'Peach',
    2: 'Litchi',
  };
  map.putIfAbsent(0, () => 'Banana');
  map.putIfAbsent(3, () => 'Banana');
  print(map);

  for (var key in [4, 5]) {
    map.putIfAbsent(key, () => 'Strawberry');
  }
  print(map);
}
```

- update
  - 指定したkeyの値を変更する
  - 指定したkeyが無い場合には、追加する場合は ifAbsentオプションを追加する
  - ※以下ではifAbsent: () => 'Apple'が無いと実行時エラーとなる
    - Unhandled Exception: Invalid argument (key): Key not in map.: 3
```dart
void main() {
  final map = <int, String>{
    0: 'Apple',
    1: 'Cherry',
    2: 'Litchi',
  };
  map.update(0, (String value) => 'Banana');
  print(map);
  map.update(3, (String value) => 'Strawberry', ifAbsent: () => 'Apple');
  print(map);
}
```
実行結果
```dart
{0: Banana, 1: Cherry, 2: Litchi}
{0: Banana, 1: Cherry, 2: Litchi, 3: Apple}
```

- sort
  - Mapにはない
  - 似てようなことを行うためには、SplayTreeMapクラスを利用

```dart
void main() {
  final map1 = SplayTreeMap();
  final map2 = <int, String>{
    2: 'Litchi',
    0: 'Apple',
    1: 'Peach',
  };
  map1.addAll(map2);
  //または
  SplayTreeMap.from(map2);// 昇順
  SplayTreeMap.from(map2,(a,b) => b.compareTo(b)); // 降順
}
```

- [参照](https://zenn.dev/iwaku/articles/2020-12-27-iwaku#map)


# 数组操作方法
- [参考](https://www.jianshu.com/p/1c7d828b1153)

# 扩展运算符

Dart 2.3引入了扩展运算符（...）和支持null的扩展运算符（...？），它提供了一种将多个元素插入集合的简洁方法

- 扩展运算符( ...) 
```dart
var list = [1, 2, 3];
var list2 = [0, ...list];
assert(list2.length == 4);
```

- 空感知扩展运算符( ...?)
```dart
var list;
var list2 = [0, ...?list];
assert(list2.length == 1);
```

# Future、Isolate和事件循环

## Async
- 该方法的返回值是一个 Future；
- 它同步执行该方法的代码直到第一个 await 关键字，然后它暂停该方法其他部分的执行；
- 一旦由 await 关键字引用的 Future 执行完成，下一行代码将立即执行。

```dart
void main() async {
  methodA();
  await methodB();
  await methodC('main');
  methodD();
}

methodA(){
  print('A');
}

methodB() async {
  print('B start');
  await methodC('B');
  print('B end');
}

methodC(String from) async {
  print('C start from $from');

  Future((){                // <== 该代码将在未来的某个时间段执行
    print('C running Future from $from');
  }).then((_){
    print('C end of Future from $from');
  });

  print('C end from $from');
}

methodD(){
  print('D');
}
```

执行结果如下
```
A
B start
C start from B
C end from B
B end
C start from main
C end from main
D
C running Future from B
C end of Future from B
C running Future from main
C end of Future from main
```

如果你最初希望示例代码中仅在所有代码末尾执行 methodD() ，那么你应该按照以下方式编写

```dart
void main() async {
  methodA();
  await methodB();
  await methodC('main');
  methodD();
}

methodA(){
  print('A');
}

methodB() async {
  print('B start');
  await methodC('B');
  print('B end');
}

methodC(String from) async {
  print('C start from $from');

  await Future((){                  // <== 在此处进行修改
    print('C running Future from $from');
  }).then((_){
    print('C end of Future from $from');
  });
  print('C end from $from');
}

methodD(){
  print('D');
}
```

执行结果
```
A
B start
C start from B
C running Future from B
C end of Future from B
C end from B
B end
C start from main
C running Future from main
C end of Future from main
C end from main
D
```

## Isolate

```
「Isolate」在 Flutter 中并不共享内存。不同「Isolate」之间通过「消息」进行通信。
```

- [参考引用](https://www.jianshu.com/p/0aefa62372c6)

# extension

dart扩展方法，支持api的扩展。Dart 扩展需要关键词:extension。这个关键字只有在 Dart 版本 2.7 及其以上才支持。所以请确保你工程项目中 pubspec.yaml 文件中:
``` yaml
environment:
sdk: ">=2.7.0 <3.0.0"
```

写法如下
```dart
extension StringExtension1 on String {
  //字符转换成Color对象
  toColor() {
    var hexColor = this.replaceAll("#", "");
    if (hexColor.length == 6) {
      hexColor = "FF" + hexColor;
    }
    if (hexColor.length == 8) {
      return Color(int.parse("0x$hexColor"));
    }
  }

  //字符转int
  parseInt() {
    return int.parse(this);
  }
}
```

- [参考](https://www.jianshu.com/p/48973aaf5883)

# 密码的正则表现
- 半角数字
```dart
[0-9]
```
- 半角記号
```dart
[-/:-@\[-~]
```
- 半角英数字記号
```dart
[-~]
```
- 半角カタカナ
```dart
[ｦ-ﾟ]
```
- カタカナ
```dart
[ァ-ヶ]
```
- ひらがな
```dart
[ぁ-ん]
```
- アルファベット
```dart
[A-z]
```
大写英文，小写英文，数字，符号混合3种以上，8-64个字母的正则表现
```dart
String pattern = r'^((?=.*[a-z])(?=.*[A-Z])(?=.*[0-9])|(?=.*[a-z])(?=.*[A-Z])(?=.*[!-/:-@[-`{-~’”])|(?=.*[A-Z])(?=.*[0-9])(?=.*[!-/:-@[-`{-~’”])|(?=.*[a-z])(?=.*[0-9])(?=.*[!-/:-@[-`{-~’”])).([a-zA-Z0-9!-/:-@[-`{-~’”]){8,64}$';
RegExp regExp = new RegExp(pattern);
regExp.hasMatch(text);
```

# 图片的64basecode转换
- BASE64 to image
```dart
// base64 code
base64 = event.snapshot.value['image'];
// Uint8List
unit8 = base64Decode(base64);
// 图片显示
Image.memory(unit8)
```

- image to BASE64

本地图片提取依赖包
```ymal
image_picker: ^0.8.3+3
```
```dart
// 本地图片选择
XFile? image = await ImagePicker().pickImage(source:ImageSource.gallery);
// 取得的图片文件
final pickedImage;
if(image == null){
  // 没有取得图片，处理结束
  return;
}
pickedImage = File(image.path);
// 转换成字节
List<int> imageBytes = await pickedImage.readAsBytes();
// base64 code
String base64Image = base64Encode(imageBytes);
```

# 时间

## DateFormat
```dart
  // 只有日
  // 例：31日 英文：31
  DateFormat.d([locale]) : this('d', locale);
  // 周几
  // 例：周二 英文：Tue
  DateFormat.E([locale]) : this('E', locale);
  // 星期几
  // 例：星期二 英文：Tuesday
  DateFormat.EEEE([locale]) : this('EEEE', locale);
  // 只有月
  // 例：8月 英文：Aug
  DateFormat.LLL([locale]) : this('LLL', locale);
  // 只有月（大写）
  // 例：八月 英文：August
  DateFormat.LLLL([locale]) : this('LLLL', locale);
  // 只有月
  // 例：8月 英文：8
  DateFormat.M([locale]) : this('M', locale);
  // 月日
  // 例：8/31 英文：8/31
  DateFormat.Md([locale]) : this('Md', locale);
  // 月日周几
  // 例：8/31周二 英文：Tue,8/31
  DateFormat.MEd([locale]) : this('MEd', locale);
  // 只有月
  // 例：8月 英文：Aug
  DateFormat.MMM([locale]) : this('MMM', locale);
  // 月日
  // 例：8月31日 英文：Aug 31
  DateFormat.MMMd([locale]) : this('MMMd', locale);
  // 月日周几
  // 例：8月31日周二 英文：Tue,Aug 31
  DateFormat.MMMEd([locale]) : this('MMMEd', locale);
  // 只有月（大写）
  // 例：八月 英文：August
  DateFormat.MMMM([locale]) : this('MMMM', locale);
  // 月日
  // 例：8月31日 英文：August 31
  DateFormat.MMMMd([locale]) : this('MMMMd', locale);
  // 月日星期几
  // 例：8月31日星期二 英文：Tuesday,August 31
  DateFormat.MMMMEEEEd([locale]) : this('MMMMEEEEd', locale);
  // 季度
  // 例：3季度 英文：Q3
  DateFormat.QQQ([locale]) : this('QQQ', locale);
  // 季度
  // 例：第三季度 英文：3rd quarter
  DateFormat.QQQQ([locale]) : this('QQQQ', locale);
  // 年
  // 例：2021年 英文：2021
  DateFormat.y([locale]) : this('y', locale);
  // 年月
  // 例：2021年8月 英文：8/2021
  DateFormat.yM([locale]) : this('yM', locale);
  // 年月日
  // 例：2021/8/31 英文：8/31/2021
  DateFormat.yMd([locale]) : this('yMd', locale);
  // 年月日周几
  // 例：2021/8/31周二 英文：Tue,8/31/2021
  DateFormat.yMEd([locale]) : this('yMEd', locale);
  // 年月
  // 例：2021年8月 英文：Aug 2021
  DateFormat.yMMM([locale]) : this('yMMM', locale);
  // 年月日
  // 例：2021年8月31日 英文：Aug 31,2021
  DateFormat.yMMMd([locale]) : this('yMMMd', locale);
  // 年月日周几
  // 例：2021年8月31日周二 英文：Tue,Aug 31,2021
  DateFormat.yMMMEd([locale]) : this('yMMMEd', locale);
  // 年月
  // 例：2021年8月 英文：August 2021
  DateFormat.yMMMM([locale]) : this('yMMMM', locale);
  // 年月日
  // 例：2021年8月31日 英文：August 31,2021
  DateFormat.yMMMMd([locale]) : this('yMMMMd', locale);
  // 年月日星期几
  // 例：2021年8月31日星期二 英文：Tuesday,August 31,2021
  DateFormat.yMMMMEEEEd([locale]) : this('yMMMMEEEEd', locale);
  // 年季度
  // 例：2021年第3季度 英文：Q3 2021
  DateFormat.yQQQ([locale]) : this('yQQQ', locale);
  // 年季度
  // 例：2021年第3季度 英文：3rd quarter 2021
  DateFormat.yQQQQ([locale]) : this('yQQQQ', locale);
  // 小时
  // 例：17时 英文：17
  DateFormat.H([locale]) : this('H', locale);
  // 小时分钟
  // 例：17:26 英文：17:26
  DateFormat.Hm([locale]) : this('Hm', locale);
  // 小时分钟秒
  // 例：17:26:59 英文：17:26:59
  DateFormat.Hms([locale]) : this('Hms', locale);
  // 小时
  // 例：下午5时 英文：5PM
  DateFormat.j([locale]) : this('j', locale);
  // 小时分钟
  // 例：下午5:26 英文：5:26 PM
  DateFormat.jm([locale]) : this('jm', locale);
  // 小时分钟秒
  // 例：下午5:26:59 英文：5:26:59 PM
  DateFormat.jms([locale]) : this('jms', locale);
  // 分
  DateFormat.m([locale]) : this('m', locale);
  // 分秒
  DateFormat.ms([locale]) : this('ms', locale);
  // 秒
  DateFormat.s([locale]) : this('s', locale);
```

## 时间转换&操作
- 创建时间UTC
```dart
var d = new DateTime.utc(2019, 10, 10, 9, 30);
```
- 解析时间 ISO 8601
```dart
// String to DateTime
String dateString = "2018-10-10 09:30:30+09:00";
DateTime date = DateFormat("yyyy-MM-ddTHH:mm:ss").parse(dateString);
// DateTime to String
DateTime day = DateTime.now();
String st = DateFormat("yyyy-MM-dd'T'HH:mm:ss'+09:00'").format(day);
```
- 时间增减量
```dart
var d1 = DateTime.now();
print(d1);
print(d1.add(new Duration(minutes: 5)));//加五分钟
print(d1.add(new Duration(minutes: -5)));//减五分钟
```
- 时间比较
```dart
  var d1 = new DateTime(2018, 10, 1);
  var d2 = new DateTime(2018, 10, 10);
  print(d1.isAfter(d2));//是否在d2之后
  print(d1.isBefore(d2));//是否在d2之前
  var d3 = DateTime.now();
  var d4 = d3.add(new Duration(milliseconds: 30));
  print(d3.isAtSameMomentAs(d4));//是否相同
```
- 时间差
```dart
var d1 = new DateTime(2018, 10, 1);
var d2 = new DateTime(2018, 10, 10);
var difference = d1.difference(d2);
print([difference.inDays, difference.inHours]);//d1与d2相差的天数与小时
```
- 时间戳
```dart
  var now = new DateTime.now();
  print(now.millisecondsSinceEpoch);//单位毫秒，13位时间戳
  print(now.microsecondsSinceEpoch);//单位微秒,16位时间戳
```