- [单例的写法](#单例的写法)
- [factory关键字](#factory关键字)
- [late关键字](#late关键字)
- [用part来拆分库](#用part来拆分库)
- [List和Map的clone](#List和Map的clone)
- [扩展运算符(...)](#扩展运算符(...))

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

# 扩展运算符(...)

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