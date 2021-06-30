- [submodule](#submodule)
- [复原文件](#复原文件)
- [git取消push](#git取消push)

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

# git取消push

## 对应方法有俩种

1. git reset(不会留下修改履历)
2. git revert(会留下修改履历)

## 方法1.git reset

取消上一次的commit，返回上次commit的状态，可以用下面的comment，option用hard
``` cmd
$ git reset --hard HEAD^
```
当想留下本地修改变更的情况，option可以用soft
``` cmd
$ git reset --soft HEAD^
```
用上面的comment可以取消commit。

git reset之后然remote push
``` cmd
$ git push -f origin HEAD
```

## 注意点
git reset取消一次commit用「HEAD^」，取消n次commit可以用「HEAD~n」

## 方法2.git revert

返回上一次commit，会留下记录
``` cmd
$ git revert HEAD
```
往remote push
``` cmd
$ git push origin HEAD
```

## 参考
https://qiita.com/S42100254h/items/db435c98c2fc9d4a68c2
