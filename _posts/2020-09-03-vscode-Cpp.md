---
title: 'VS Code添加第三方库'
date: 2020-09-03
permalink: /posts/2020/09/VSCode/
tags:
  - VS Code
---

例如：将需要添加的第三方库文件（stdc++.h）放到桌面，然后在终端输入：

```shell
sudo cp /Users/walden/Desktop/stdc++.h /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/include/c++/v1/stdc++.h
```

如何找到应该存放的位置？在编辑器中，随意选择一个已存在的第三方库（例如#include\<iostream\>，command+鼠标左键点击iostream，即可进入其目录。