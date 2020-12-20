- [ExpansionPanel踩坑点](#ExpansionPanel)

# ExpansionPanel

## Exception报错

>The following assertion was thrown building ExpansionPanelList(dependencies: [_LocalizationsScope-[GlobalKey#63a3b]], state: _ExpansionPanelListState#61f06):
>'package:flutter/src/material/mergeable_material.dart': Failed assertion: line 456 pos 18: '_children[j] is MaterialGap': is not true.

## 原因

Dynamically adding a new panel with isExpanded = true, I don't get the expected gap.
添加一个新的pannel的时候，一个pannel是展开状态的时候(isExpanded = true)，会出现以上报错

暂时可以避免的措施是新添一个pannel的时候，强行把所以展开的pannel给关闭掉(isExpanded = false)

